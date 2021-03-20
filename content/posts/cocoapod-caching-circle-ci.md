---
title: "Cocoapod Caching & Mengurangi Build Time CircleCI"
date: 2019-08-03T00:30:48+07:00
categories:
- Notes
tags:
- development
- swift
- git
- xcode
keywords:
- xcode
- circleCI
- buildtime
- cocoapods
- build
- Continous Integration
- CI
- CD
- Kitabisa
- fastlane
- ruby
draft: false
thumbnailImage: https://source.unsplash.com/ymf4_9Y9S_A/
#thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/d0AcxMk33is/
---

Hi 👋

Beberapa waktu yang lalu, ketika ada waktu luang karena sudah selesai mengerjakan sprint, gue mencoba untuk mengerjakan salah satu [Technical debt](https://en.wikipedia.org/wiki/Technical_debt) yang belum terlaksana di [aplikasi Kitabisa versi iOS](https://apps.apple.com/id/app/kitabisa/id1458307938) yaitu implement [Continuous Integration](https://martinfowler.com/articles/continuousIntegration.html) dan [Continuous Delivery](https://martinfowler.com/bliki/ContinuousDelivery.html).
<!--more-->
Untuk mempermudah, _fastlane_ dijadikan pilihan utama sebagai alat bantunya. Selain simple, _fastlane_ juga punya action [`Match`](https://docs.fastlane.tools/actions/match/) yang memudahkan _code signing_. Dan untuk machine nya sendiri, [kitabisa](https://kitabisa.com) sudah menggunakan [CircleCI](https://circleci.com/).

Sebelum memulai, perlu diketahui di aplikasi kitabisa ada 2 tahap development, yaitu staging dan production yang semua setting dan configurasinya ada di info.plist. Jadi, hanya menggunakan satu target tapi punya 2 scheme. Oleh karena itu `dotenv` bisa dimanfaatkan untuk membedakan environment buildnya. Di post ini ga akan dibahas mendetail tentang setup fastlane ya, dan diasumsikan kalian sudah lumayan familiar dengan apa yang akan dibahas.
<!--toc-->

# First Step
![steps](https://source.unsplash.com/bt-Sc22W-BE/1800x620)

Awalnya simple aja, ikutin beberapa tutorial dari hasil search sana-sini, dan ini adalah script `ruby` untuk dipanggil oleh fastlane untuk archive project iOS kitabisa.

```ruby
def buildGym(method:, configuration:, provisioning:)
    build_number = TZInfo::Timezone.get('Asia/Jakarta').now.strftime("%y%m%d.%H%M")
    increment_build_number build_number: build_number

    settings_to_override = {
        :PROVISIONING_PROFILE_SPECIFIER => "match #{provisioning} #{ENV['BUNDLE_IDENTIFIER']}"
    }

    gym(
        workspace: @WORKSPACE_PATH,
        scheme: ENV['SCHEME'],
        configuration: configuration,
        export_method: method,
        xcargs: settings_to_override,
    )
    putChangeLog
end
```

Variable `settings_to_override` digunakan untuk me-override _code-signing_ agar sesuai dengan yang diinginkan (e.g untuk upload ke fabric maka provisioning yang digunakan adalah provisioning adhoc).

Dan untuk fastlane lane-nya seperti berikut,

```ruby
desc "build for staging/production"
lane :build do
    buildGym(
        method: ENV['METHOD'],
        configuration: ENV['CONFIGURATION'],
        provisioning: ENV['PROVISIONING']
    )
end
```

Jadi ketika dipanggil hanya perlu pasing env-nya saja seperti berikut,

```bash
fastlane build --env staging
```
atau
```bash
fastlane build
```
karena env defaultnya adalah production.

Ok, coba running di local, success me-archive sehingga menghasilkan file `.ipa` dan `.dsym`.

Untuk setting workflow circleCI pada awalnya seperti berikut,

```yaml
# .circleci/config.yml
version: 2
jobs:
  buildStaging:
    macos:
      xcode: "10.2.1"
    environment:
      FASTLANE_LANE: build
    shell: /bin/bash --login -o pipefail
    steps:
      - checkout
      - restore_cache:
          key: 1-gems-{{ checksum "Gemfile.lock" }}
      - run:
          name: bundle installation
          command: bundle check || bundle install --path vendor/bundle
      - save_cache:
          key: 1-gems-{{ checksum "Gemfile.lock" }}
          paths:
            - vendor/bundle
      - run:
          name: Fastlane build staging
          command: bundle exec fastlane $FASTLANE_LANE --env staging
      - persist_to_workspace:
          root: .
          paths:
            # needed to upload to fabric beta
            - Pods/Crashlytics/
            - output/
            - fastlane/      
            - Gemfile
            - Gemfile.lock

  stagingDistribution:
    macos:
      xcode: "10.2.1"
    environment:
      FASTLANE_LANE: beta
      FASTLANE_LANE_APPCENTER: appcenter
    shell: /bin/bash --login -o pipefail
    steps:
      - attach_workspace:
          at: .
      - restore_cache:
          key: 1-gems-{{ checksum "Gemfile.lock" }}
      - run:
          name: bundle installation
          command: bundle check || bundle install --path vendor/bundle
      - save_cache:
          key: 1-gems-{{ checksum "Gemfile.lock" }}
          paths:
            - vendor/bundle
      - run:
          name: Upload to fabric beta
          command: bundle exec fastlane $FASTLANE_LANE --env staging
      - store_artifacts:
          path: output/

workflows:
  version: 2
  buildForBeta:
    jobs:
      - buildStaging
      - stagingDistribution:
          requires:
            - buildStaging
```

Workflow-nya berjalan seperti ini, job yang pertama membuild & me-archive project sehingga menghasilkan produk akhir yaitu `.ipa` dan `.dsym` yang sudah di zip. Setelah job pertama selesai, dilanjutkan ke job berikutnya, yaitu mendistribusi file `.ipa` dan `.dsym` ke fabric beta. Karena binary untuk upload ke fabric beta termasuk dalam pods nya fabric beta, maka beberapa file perlu 'dikirim' ke job berikutnya, dengan perintah `persist_to_workspace` dan pada job berikutnya file tersebut diterima dengan perintah `attach_workspace`.

## Nightmare
![nighmare][nightmare]
[nightmare]: https://source.unsplash.com/P_GeWr5wNQc/1800x620

Sebenarnya semua berjalan dengan lancar, build success, beta distribution pun berjalan.

Sampai pada akhirnya, CircleCI mengirimkan baris response berikut.

```bash
Blocked due to plan-no-credits-available Please purchase a new credit block then push a new commit or call the API to run a new build.
```

Ya, kehabisan kredit di CircleCI.

Total build untuk project iOS sampai dengan berikut
![Gile](https://res.cloudinary.com/tendabiru/image/upload/v1564853737/obushv.png)

Ya, ternyata rata-rata build time sekitar 15-17 menit, total untuk staging & production sekitar 30 menit sekali jalan.

# Pods Caching

Hal yang dicurigai pertama adalah proses build nya pod. Ok gimana kalo pods nya di cache aja? toh jarang-jarang juga update pod.

Googling-googling sampai lah ke artikel Om [Loïs Di Qual](https://blog.takescoop.com/improve-ios-ci-build-time-with-cocoapods-caching-4a049ee45e63).

Konsepnya sih, instead of build workspacenya, build aja masing-masing projectnya (project cocoapods dan main). Khusus untuk project cococapods, setelah success cache folder `build` kemudian di link ke main projectnya.

Untuk detailnya bisa baca link di atas. Hanya saja, ketika dicoba cocoapodpod framework yang sudah di buat selalu gagal di - link - ke dalam app Kitabisa.

Setelah beberapa kali coba-coba akhirnya ini patch yang bisa dipake di project Kitabisa.

```ruby
desc "patch app"
lane :patch_app do
    fastlane_require 'xcodeproj'
    project = Xcodeproj::Project.open("../Kitabisa.xcodeproj")
    target = project.targets.select { |target| target.name == "Kitabisa" }.first
    phase = target.shell_script_build_phases.select { |phase| phase.name.include?('Embed Pods Frameworks') }.first        
    phase.shell_script = [
        "BUILT_PRODUCTS_DIR=#{File.expand_path('../build/Release-iphoneos')}",
        "#{phase.shell_script}"
    ].join("\n")
    
    project.save()
end
```

Dan, di linknya mas [Loïs Di Qual](https://blog.takescoop.com/improve-ios-ci-build-time-with-cocoapods-caching-4a049ee45e63) juga menggunakan s3 nya aws. Sementara gue memilih untuk menggunakan [system cache-nya si CircleCI](https://circleci.com/docs/2.0/caching/).

Jadi untuk build pods dan cachenya kira-kira begini,
```ruby
def build_pod()
    if File.directory?(File.expand_path('../build'))
        puts "Folder exists"
    else
        xcodebuild(
            :project => File.expand_path('../Pods/Pods.xcodeproj'),
            :scheme => "Pods-Kitabisa",
            :configuration => 'Release',
            :destination => 'generic/platform=iOS',
            :xcargs => 'BITCODE_GENERATION_MODE=bitcode',
        )
    end
end

desc "build pod project"
lane :build_pod do
    build_pod
end
```

Sementara untuk build main projectnya, menjadi seperti berikut,

```ruby
def build_app(method:, configuration:, provisioning:)
    fastlane_require 'tzinfo'
    get_match
    build_number = TZInfo::Timezone.get('Asia/Jakarta').now.strftime("%y%m%d.%H%M")
    increment_build_number build_number: build_number
    cache_folder = File.expand_path('../build/Release-iphoneos')

    settings_to_override = {
        :PROVISIONING_PROFILE_SPECIFIER => "match #{provisioning} #{ENV['BUNDLE_IDENTIFIER']}",
        :PODS_CONFIGURATION_BUILD_DIR => "#{cache_folder}",
        :FRAMEWORK_SEARCH_PATHS => "$(inherited) #{cache_folder}"
    }

    gym(
        project: "Kitabisa.xcodeproj",
        scheme: ENV['SCHEME'],
        configuration: configuration,
        export_method: method,
        xcargs: settings_to_override,
    )
    putChangeLog
end

def putChangeLog()
    File.open('changes.txt', 'a') { |file| file.puts last_git_commit[:message]}
end

desc "build for staging/production"
lane :build do
    build_app(
        method: ENV['METHOD'],
        configuration: ENV['CONFIGURATION'],
        provisioning: ENV['PROVISIONING']
    )
end
```

Sehingga basic flownya seperti berikut,

```bash
fastlane build_pod
fastlane patch_app
fastlane build --env '{environment}'
```

Ok test di CircleCI dan ini hasilnya.

Sebelum di cache
![sebelum di cache](https://res.cloudinary.com/tendabiru/image/upload/v1564855235/aksjfkaljfkljf_frfrzz.png)

Setelah di cache
![setelah di cache](https://res.cloudinary.com/tendabiru/image/upload/v1564855240/ieopqrOPIUhre_yivbs3.png)

Hm, ternyata hanya turun sekitar 5 menit saja.

# Optimize to the max

Setelah pods di cache, ternyata cuma turun 5 menit-an saja.

Setelah dicari-cari, yang paling lama adalah process xcrun seperti digambar berikut
![xcrun](https://res.cloudinary.com/tendabiru/image/upload/v1564855242/kPOJEjrkLJrkfja_bfhlzr.png)

Kalau dilihat, dari start process xcrun sampai selesai memakan waktu kira - kira 5-6 menit.

Sebenarnya process apa itu?

Setelah googling-googling (lagi), ternyata itu adalah salah satu process [App Thining](https://help.apple.com/xcode/mac/current/#/devbbdc5ce4f)nya si apple yaitu embedded bitcode untuk keperluan LLVM.

Berhubung untuk staging hanya keperluan QA, jadi sepertinya process ini bisa diskip dan tetap di enable untuk production yang masuk ke appstore connect. Tapi untuk cocoapods gue akan tetap menanbahkan bitcode pada buildnya agar bisa dipakai di staging ataupun production, toh sudah di cache juga.

Untuk me-disable bitcode via gym, hanya perlu ditambahkan parameter uploadBitcode & complieBitcode pada export_options-nya.

Jadi perubahannya seperti berikut.

```ruby
def build_app(method:, configuration:, provisioning:)
    fastlane_require 'tzinfo'
    get_match
    build_number = TZInfo::Timezone.get('Asia/Jakarta').now.strftime("%y%m%d.%H%M")
    increment_build_number build_number: build_number
    cache_folder = File.expand_path('../build/Release-iphoneos')

    settings_to_override = {
        :PROVISIONING_PROFILE_SPECIFIER => "match #{provisioning} #{ENV['BUNDLE_IDENTIFIER']}",
        :PODS_CONFIGURATION_BUILD_DIR => "#{cache_folder}",
        :FRAMEWORK_SEARCH_PATHS => "$(inherited) #{cache_folder}"
    }

    gym(
        project: "Kitabisa.xcodeproj",
        scheme: ENV['SCHEME'],
        configuration: configuration,
        export_method: method,
        xcargs: settings_to_override,
        export_options: {
            uploadBitcode: false,
            uploadSymbols: true,
            compileBitcode: false
        }
    )
    putChangeLog
end

def putChangeLog()
    File.open('changes.txt', 'a') { |file| file.puts last_git_commit[:message]}
end

desc "build for staging/production"
lane :build do
    build_app(
        method: ENV['METHOD'],
        configuration: ENV['CONFIGURATION'],
        provisioning: ENV['PROVISIONING']
    )
end
```

Terakhir, untuk workflow CircleCI dirubah menjadi seperti ini,

```yaml
# .circleci/config.yml

aliases:
  - &restore_cache
    key: v2-gems-{{ checksum "Gemfile.lock" }}
  - &run_cache
    name: bundle installation
    command: bundle check || bundle install --path vendor/bundle
  - &save_cache
      key: v2-gems-{{ checksum "Gemfile.lock" }}
      paths:
        - vendor/bundle
  
  - &restore_pod
    key: v2-pods-{{ checksum "Podfile.lock" }}
  - &run_pod
    name: pod cache
    command: bundle exec fastlane build_pod
  - &save_pod
    key: v2-pods-{{ checksum "Podfile.lock" }}
    paths:
      - build/

  - &persist_to_workspace
    root: .
    paths:
      # needed to upload to fabric beta
      - Pods/Crashlytics/
      - output/
      - fastlane/      
      - Gemfile
      - Gemfile.lock

  - &xcode_patch
    name: xcode patch
    command: bundle exec fastlane patch_app

defaults: &defaults
  macos:
    xcode: "10.2.1"
  shell: /bin/bash --login -o pipefail

version: 2
jobs:
  build_staging:
    <<: *defaults
    steps:
      - checkout
      - restore_cache: *restore_cache
      - run: *run_cache
      - save_cache: *save_cache
      - restore_cache: *restore_pod
      - run: *run_pod
      - save_cache: *save_pod
      - run: *xcode_patch
      - run:
          name: fastlane build staging
          command: bundle exec fastlane build --env staging
      - persist_to_workspace: *persist_to_workspace
    
  build_production:
    <<: *defaults
    steps:
      - checkout
      - restore_cache: *restore_cache
      - run: *run_cache
      - save_cache: *save_cache
      - restore_cache: *restore_pod
      - run: *run_pod
      - save_cache: *save_pod
      - run: *xcode_patch
      - run:
          name: fastlane build production
          command: bundle exec fastlane build
      - persist_to_workspace: *persist_to_workspace

            
  qa_release:
    <<: *defaults
    steps:
      - attach_workspace:
          at: .
      - restore_cache: *restore_cache
      - run: *run_cache
      - save_cache: *save_cache
      - run:
          name: upload to fabric beta
          command: bundle exec fastlane beta
  
  appstore_release:
    <<: *defaults
    steps:
      - attach_workspace:
          at: .
      - restore_cache: *restore_cache
      - run: *run_cache
      - save_cache: *save_cache
      - run:
          name: upload to testflight
          command: bundle exec fastlane release
      

workflows:
  version: 2
  staging_build:
    jobs:
      - build_staging:
          filters:
            branches:
              only:
                - /pull\/[0-9]+/
      - qa_release:
          requires:
            - build_staging
  production_build:
    jobs:
      - build_production:
          filters:
            branches:
              only:
                - preDevelop
                - develop
      - qa_release:
          requires:
            - build_production
      - appstore_release:
          requires:
            - build_production
          filters:
            branches:
              only:
                - develop
```

Sekarang semua keperluan untuk dikirim ke QA ditrigger hanya dengan membuat Pull Request, dan build production di trigger melalu branch preDevelop saja, dan akan dikirim ke appstore hanya ketika ada di branch develop. 

Setelah coba dijalankan, hasilnya seperti berikut.
![maximize](https://res.cloudinary.com/tendabiru/image/upload/v1564855236/JkjkjKlPOPre_edjobn.png)

Wow, sekarang total build hanya sekitar 5-6 menit saja 🍻.

Oh iya, ini belum sama Unit Test ya, karena memang belum dibikin dan perlu beberapa re-factor yang belum sempat di kulik dari sisi code-nya untuk implement unit text dan ui test 😳.

# Lessons learned

Dari kejadian [di atas](#nightmare), gue jadi belajar kalau ~~CircleCI itu mahal~~ project yang menggunakan cocoapod masih bisa dipecah dan dioptimalisasi lagi untuk build timenya bahkan di local environment. Selain itu, gue juga mengerti bahwa CircleCI memungkinkan untuk di build dr pull request, dan yaml bisa pake alias sehingga mengurangi copy-paste.

Kalau ada pertanyaan atau saran atau apapun, bisa pass comment dibawah.

Salam 👋