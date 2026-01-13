---
title: "Swift Dependency Injection menggunakan Needle"
date: 2021-02-20T23:17:44+07:00
categories:
- Notes
tags: [development, swift]
keywords: [swift, iOS, dependency injection, depedensi, di, needle, needleDI, tutorial, framework, xcode, xcode project, library]
draft: false
thumbnailImage: https://images.unsplash.com/photo-1502217915754-8272dd84a805
#thumbnailImagePosition: "top"
coverImage: https://images.unsplash.com/photo-1502217915754-8272dd84a805
---

Mungkin udah sering denger dengan yang namanya `dependency injection`, teknik yang memungkinkan sebuah objek menerima objek-objek lain yang diperlukan. objek-objek ini lah yang biasanya disebut dependency atau bahasa Indonesia-nya dependensi 🙄️.

<!--more-->

Untuk sederhananya mungkin bisa dilihat diagram berikut,

![flow][img_flow]

Anggap ada sebuah aplikasi e-commerce yang bernama Tokopaedi. Setelah Login, Tokopaedi menampilkan Dashboard, di dalam Dashboard ada proses mendapatkan object `UserData` yang diperlukan salah satunya oleh Product Detail untuk mengecek apakah produk tersebut sudah ada di cart atau wish list. Tapi dari Dashboard ada 3 kemungkinan untuk menuju ke Product Detail, yaitu:

- Melalui Categories, lalu ke Product List, baru ke Product Detail.
- Melalui Product List, lalu ke Product Detail.
- Atau user langsung membuka Product Detail dari Dashboard.

Dari contoh kasus di atas, bisa dibilang kalau Product Detail memiliki ketergantungan atau dependensi kepada Dashboard, karena memang semua data/object `UserData` diproses di Dashboard.

> "Gimana kalau proses untuk mendapatkan `UserData` itu dipindain aja ke Product List ?"

![Ayu TingTing][meme_ayu]

Tidak semudah itu Ferguso. Karena ternyata object `UserData` bisa jadi juga digunakan di View / ViewController lain yang tidak didefinisikan didiagram di atas.

> "Yaudah passing aja service nya"

Ya emang yang bener itu "Pass the service, not the data". Kalau dengan cara tradisional, berarti si Categories sama Product List bakal tau tentang si `UserData` dan punya tanggung jawab untuk passing lagi ke Product Detail walaupun yang dipassing itu bentuknya service dan bukan data, lagipula ada kemungkinan `UserData` bisa jadi 'kesenggol' di Categories ataupun Product List dan hal ini membuat `UserData` ketika nyampe di Product Detail berubah.

> "Uhm, Singleton?"

![Stop It!][meme_stop_it]

Dengan menggunakan teknik dependency injection cara tradisional diatas bisa dihindari, karena dengan dependency injection proses transfer dari Dashboard ke Product List akan dilakukan didalam si framework itu sendiri tanpa menyentuh View / ViewController/ proses lainnya.
Anggaplah si dependency injection ini sebagai middleman/system yang berperan me-mapping dari dan kemana data-data yang dependen di alirkan.

Untuk platform iOS sendiri ada beberapa framework untuk dependency injection, contohnya [Swinject][link_swinject], [Cleanse][link_cleanse], [Weaver][link_weaver], [Needle][link_needle], dkk.

# Needle DI

![Needle DI][img_needle_prompt]

Dari ringkasan github page digambar atas kelebihan yang ditawarkan oleh si Needle ini kasarnya antara lain,

## Struktur DI yang hierarchical

Needle me-encourage atau menganjurkan untuk membuat sebuah struktur DI yang hierarchical atau ada urutan tingkat didalamnya. Hal ini membuat struktur DI yang kita implementasikan selaras dengan flow aplikasi Tokopaedi, dan secara tidak langsung mempermudah apabila ada dev lain yang lagi mempelajari atau mengembakan aplikasi Tokopaedi ini.

## Compile time safe

Needle juga berani ngasih jaminan, kalo setiap dependency yang belum di deklarasi tapi tau-tau secara ghoib udah dipake oleh object lain maka project tersebut tidak akan bisa di compile. Ini bisa mengurangi potensi 'kelupaan' yang kerap terjadi di development.

Dependency Injection bisa dilakukan baik di runtime ataupun compile time, salah satu framework yang melakukan nya saat runtime adalah Swinject. Sebelum menggunakan dependencynya, dev harus mendaftarkan dependency tersebut terlebih dahulu, baru kemudian akan diinject ketika runtime, namun yang _aga kureng menurut kite nih_, Swinject ini ga ada compile time checknya (ya karena emang di runtime).

### Contoh runtime Swinject

Class dan protocol diambil langsung dari [playground][link_swinject_playground] yang ada di repository swinject, terus container untuk class Animal diilangin / dicomment.

![Swinject][img_swinject_success]

Cuss compile, and... success ye kan...

Tapi pas di run...

![Swinject][img_swinject_error]

Fatal error dong..
Ya karena tadi, ga ada checker di compile timenya.

Nah, si Needle rada beda nih, dia tetep nginject nya saat compile, tapi dia jg ada checker nya yang jagain supaya dependency yang belum terdaftar ga bisa dipake oleh object yang memerlukan dependency itu.

Oleh karena itu, Needle punya 2 hal yang perlu dipersiapkan,

1. Code Generator untuk menggenerator (pastinya) DI Graph
2. Si needle framework itu sendiri.

Code Generator nya bisa diinstall menggunakan [Homebrew][link_homebrew] ataupun [Carthage][link_carthage], sementara Needle frameworknya sendiri udah bisa disemua dependency manager macam [cocoapods][link_cocoapod], [spm][link_spm], dan carthage pastinya.

## Example

Hanya ada satu class utama yang perlu diperhatikan dari framework Needle ini, yaitu [Components][link_component], si component ini yang menghubungkan satu component (scope) dengan component yang lain dan pada akhirnya berbentuk seperti pohon, dimana kita yang menentukan siapa yang jadi parent dan siapa yang jadi child nya. Component ini pula tempat kita mendaftarkan dependency yang diperlukan untuk component/scope dibawahnya. Atau singkatnya, Component ini adalah container untuk setiap dependency yang akan digunakan oleh object yang membutuhkannya.

Sebelum diliat contohnya, mungkin bisa kak diliat-liat dulu [repo untuk contohnya][link_example].

Untuk contoh ini Needle Generator nya diinstall menggunakan [homebrew](https://github.com/uber/needle#using-homebrew), dan Needle framework nya menggunakan spm.

Dicontoh aplikasi tokopaedi di atas, scope nya hanya dari Dashboard ya, jadi diasumsikan user sudah login. Dan `UserData` yang didemoin cuma string yang akan dikirim dari Dashboard ke Product Detail.

Dengan demikian berarti ada 4 Modules (Dashboard, Categories, Product List, Product Detail) tiap modules/ViewController akan punya masing-masing 1 Component.

Apa saja Componentnya? Mari kita saksikan penelusuran berikut.

### Dashboard

Dashboard ini akan menjadi _root view_, dan karena Dashboard _root view_ maka otomatis dan pastinya Dashboard ga akan perlu dependency apa-apa, Needle udah menyediakan satu class untuk case ini `BootstrapComponent`. Ya kalo dibuka si isinya wrapper untuk `Component<EmptyDependency>` yang `parent`-nya di set utk fatalError.

Begini kira-kira deklarasi Component untuk Dashboard alias _root view_

```swift
import NeedleFoundation
import UIKit

class DashboardComponent: BootstrapComponent {
    
    var userData: UserData {
        return mutableUserData
    }
    
    var mutableUserData: MutableUserData {
        return shared {
            UserDataImplementor()
        }
    }
    
    var dashboardViewController: UIViewController {
        return DashboardViewController(userData: mutableUserData, productDetailBuilder: productDetailComponent, productListBuilder: productListComponent, categoriesBuilder: categories)
    }
    
    var categories: CategoriesComponent {
        return CategoriesComponent(parent: self)
    }
    
    var productListComponent: ProductListComponent {
        return ProductListComponent(parent: self)
    }
    
    var productDetailComponent: ProductDetailComponent {
        return ProductDetailComponent(parent: self)
    }
    
}
```

Udah ada gambaran belum? sedikit-sedikit lah ya, dari deklarasi class diatas mungkin kita bisa berasumsi kalau Dashboard atau DashboardComponent ini adalah parent dari Categories, ProductList, dan Product Detail, oleh karena itu memungkinkan untuk bernavigasi dari Dashboard ke anak-anaknya (Categories, ProductList, dan Product Detail).

Dan variable userData yang ingin kita passing ke Product Detail, value userData didapatkan dari proses `UserDataImplementor()` yang isi dalemannya bisa dilihat langsung aja di [reponya](https://github.com/cizzu/NeedleDIMVC/blob/3c922d5a640db3ee818e78395f8a0310aa0a1231/NeedleMVC/Stream/UserData.swift#L18).

Keyword `shared` adalah utility yang disediakan oleh Needle framework untuk mengembalikan object yang sama setiap kali variable tersebut diakses, dan lifetime variable tersebut mengikuti lifetime dari componentnya.

### Categories

Dengan scope contoh kasus aplikasi tokopaedi diatas asumsi yang diberikan untuk Categories tidak akan punya dependensi apa-apa dari manapun, oleh karena itu bisa digambarkan kira-kira untuk Component dari Categories akan seperti berikut,

```swift
import NeedleFoundation
import UIKit

protocol CategoriesBuilder {
    var categoriesViewController: UIViewController { get }
}

class CategoriesComponent: Component<EmptyDependency>, CategoriesBuilder {
    
    var categoriesViewController: UIViewController {
        return CategoriesViewController(productListBuilder: productListComponent)
    }
    
    var productListComponent: ProductListComponent {
        return ProductListComponent(parent: self)
    }
}

```

Nah nah nah, sekarang tanpa dijelasin lagi mestinya udah ada bayangan ya? iya betul si Categories/Categories Component ini punya child Product List, oleh karena itu memungkin kan flow nya untuk membuka Product List dari Categories.

### Product List

Karena Product LIst dengan asumsi yang sama seperti Categories, maka deklarasi codenya mirip-mirip Categories sih, seperti ini

```swift
import NeedleFoundation
import UIKit

protocol ProductListBuilder {
    var productListViewController: UIViewController { get }
}

class ProductListComponent: Component<EmptyDependency>, ProductListBuilder {
    
    var productListViewController: UIViewController {
        return ProductListViewController(productDetailBuilder: productDetailComponent)
    }
    
    var productDetailComponent: ProductDetailComponent {
        return ProductDetailComponent(parent: self)
    }
}

```

Nah kan nah kan, udah makin ngerti ya hierarical nya needle kaya gimana? iya bener, si Product List adalah parent dari Product Detail, sudah pasti flow nya membuka si Product Detail.

Dan sekarang jagoannya nih,

### Product Detail

Nah disini lah perjalanan nya berakhir, gimana ya kira-kira Component yang perlu dependency?

Yak Betool `Component<Dependency-nya>`

Jadi implementasinya begini,

```swift
import NeedleFoundation
import UIKit

protocol ProductDetailDependency: Dependency {
    var userData: UserData { get }
}

protocol ProductDetailBuilder {
    var productDetailViewController: UIViewController { get }
}

class ProductDetailComponent: Component<ProductDetailDependency>, ProductDetailBuilder {
    var productDetailViewController: UIViewController {
        return ProductDetailViewController(userData: dependency.userData)
    }
}
```

Gampang ternyata, hanya perlu adopsi dari protocol Dependency 😲️.

Dan karena Dashboard udah punya object userData jadi di protocol `ProductDetailDependecy` bisa digunakan nama object yang sama, sekarang bagaimana kalau dashboard ga punya dan scope nya hanya untuk Product Detail aja?

Karena ini protocol based, dan ga ngiket ke class apa-apa, ya tinggal di extend saja si Dashboard nya, jadi kira-kira begini

```swift
import NeedleFoundation
import UIKit

class DashboardComponent: BootstrapComponent {
        
    var mutableUserData: MutableUserData {
        return shared {
            UserDataImplementor()
        }
    }
    
    var dashboardViewController: UIViewController {
        return DashboardViewController(userData: mutableUserData, productDetailBuilder: productDetailComponent, productListBuilder: productListComponent, categoriesBuilder: categories)
    }
    
    var categories: CategoriesComponent {
        return CategoriesComponent(parent: self)
    }
    
    var productListComponent: ProductListComponent {
        return ProductListComponent(parent: self)
    }
    
    var productDetailComponent: ProductDetailComponent {
        return ProductDetailComponent(parent: self)
    }
    
}

extension DashboardComponent: ProductDetailDependency {
    var userData: UserData {
        return mutableUserData
    }
}
```

> Terus mana buktinya kalo ini compile time safe?

Ya apus aja cb itu variable `userData` dari Dashboard

![Needle Fail][img_needle_fail]

Jadi ketauan kan kalau si Product Detail menggunakan dependency yang fana alias ghoib, pesen errornya begini nih

![Needle Error Message][img_needle_error_message]

![that's more like it][meme_more_like_it]

## Kesimpulan

Contoh yang dibuat mungkin terlalu sederhana ya, tapi semoga kalian dapet lah point-point nya, walaupun sedikit cringe dengan gif-gif yang norak dan bahasa yang ga jelas, tapi yaaaa~ semoga kalian mengerti lah.

Intinya dengan menggunakan Framework Needle ini kita dapat 2 bonus point selain dependency injectionnya sendiri, yaitu __Flow/hierarchy dari aplikasi tergambar dengan jelas__ yang berguna untuk menghindari observable-observable yang kadang bisa tau-tau datang dari mana aja, dan juga akan sangat membantu ketika akan membuat Unit Test. Karena untuk setiap ViewController kita bisa mem-Mock dependency-nya, serta __Compile Time Safety__ yang menjamin bahwa setiap dependency yang akan digunakan sudah ada dan siap digunakan ketika di runtime.

[//]: # (Link Placeholder)
[link_swinject]: https://github.com/Swinject/Swinject
[link_needle]: https://github.com/uber/needle
[link_weaver]: https://github.com/scribd/Weaver
[link_cleanse]: https://github.com/square/Cleanse
[link_swinject_playground]: https://github.com/Swinject/Swinject/blob/master/Sample-iOS.playground/Contents.swift
[link_homebrew]: https://github.com/Homebrew/brew
[link_carthage]: https://github.com/Carthage/Carthage
[link_cocoapod]: https://github.com/CocoaPods/CocoaPods
[link_spm]: https://github.com/apple/swift-package-manager
[link_example]: https://github.com/cizzu/NeedleDIMVC
[link_component]: https://github.com/uber/needle/blob/master/API.md#components
[img_flow]: https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/v1613887272/dependency_ljfjjq.png
[img_needle_prompt]: https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/v1613888430/needleDI_xcclbm.png
[img_swinject_success]: https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/v1613890903/download_vcotzm.jpg
[img_swinject_error]: https://res.cloudinary.com/tendabiru/image/upload/v1613903941/download_eizzuk.jpg
[img_needle_fail]: https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/v1616226416/needle_fail_zl3t3t.png
[img_needle_error_message]: https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/v1616226569/needle_error_message_z2ym8h.png
[meme_stop_it]: https://media.giphy.com/media/l4Ki2obCyAQS5WhFe/giphy.gif
[meme_ayu]: https://media.giphy.com/media/3o6nUNJU6hES2L8gs8/giphy.gif
[meme_more_like_it]: https://media.giphy.com/media/l1JohfatfAXIGNLos/giphy.gif