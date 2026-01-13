---
title: "Duplicacy & Backblaze B2"
date: 2020-09-14T10:23:53+07:00
categories:
- Opini
- Review
tags:
- backup
keywords:
- duplicacy
- backblaze b2
- backup
- storage
- cloud
- cloud storage
- Afriyandi Setiawan
draft: false
#thumbnailImage: https://source.unsplash.com/5yEiCUynJ9w/
#thumbnailImagePosition: "top"
coverImage: https://images.unsplash.com/photo-1551313158-73d016a829ae
---

Beberapa waktu yang lalu, sempet kepikiran untuk punya satu server cloud backup sendiri khususnya untuk project-project lama yang mungkin masih berguna tapi lumayan makan space di macbook (maklum macnya pake storage yang kecil).
<!--more-->
Tapi pada dasarnya males naro di google drive/dropbox yang notabene dipakai untuk file-file pribadi dan kayanya kurang cocok kalo untuk backup file-file yang mungkin jarang dipake, udah gitu harganya ya lumayan lah.

Jadi kepikiran untuk naruh di cloud storage aja yang ga perlu sync (tapi tetep perlu versioning), jadi emang bener-bener buat backup. Dan kalo bisa ada di beberapa cloud storage.

Beberapa provider untuk cloud storage yang kayanya cocok itu antara lain [Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html), [Wasabi](https://wasabi.com/), [Amazon S3](https://aws.amazon.com/s3/), dll. Dari semuanya yang nyangkut di hati emang Backblaze B2, karena ya harganya bisa dibilang paling murah dibanding yang lain, walaupun kalo backupnya 1Tera-an mending pake wasabi karena lebih murah soalnya ga kena charge lagi untuk download/upload.

Untuk post kali ini sebagian orang pasti akan beranggapan penggunaan [Duplicacy](https://duplicacy.com/) dan Backblaze B2 yang akan ditulis disini overkill. Ya walaupun disini cuma dikasih contoh untuk backup project-project pribadi, tapi pada dasarnya bisa diimplikasikan misalnya untuk server backup, database backup, file backup, dll. Dan mungkin juga ga akan dibahas terlalu detail masalah teknikelnya ya, males ngartiin ke bahasa Indonesia-nya, [baca aja sendiri yak](https://github.com/gilbertchen/duplicacy/wiki).

Sebelum memulai, perlu sedikit dijabarin [duplicacy](https://duplicacy.com/) sendiri adalah software yang digunakan untuk backup data dari local computer/machine ke cloud storage provider. Kelebihannya seperti yang diambil dari [repo](https://github.com/gilbertchen/duplicacy/)-nya antara lain:

1. Satu cloud storage bisa digunakan untuk backup beberapa computer (di claim cuma duplicacy yang bisa lakuin ini).
2. Duplicacy menggunakan pendekatan database-less, track-nya pake hash paket filenya.
3. Wuss wuss, walaupun awal backup nya rada lama, tapi update dan prunenya cepet.

![duplicate](https://images.unsplash.com/photo-1589447388175-ac47d31be950?q=80&w=1470&auto=format&fit=crop&w=1800&h=620)

Duplicacy ini sendiri free untuk versi cli-nya, tapi berbayar ($20) untuk versi gui dan web-based nya, dan ga single purchase alias tiap tahun berjalannya bakalan kena charge lagi $5, ini untuk personal use dan first computer, computer berikutnya bakalan kena charge $10 dan $2 pertahunnya, untuk korporat $50.

Kenapa ga pake [duplicati](https://www.duplicati.com/) yang notabene gratis? ga tau kenapa sih, mungkin lebih ke alasan personal aja 🤣️. Well, kalo [baca-baca](https://forum.duplicacy.com/t/duplicacy-demolishes-duplicati-and-rclone-for-large-backups/2483) si duplicacy ini rada lebih oke dibanding duplicati, khususnya untuk backup yang size nya besar. Dan duplicati juga nyimpen "Local Cache" yang kalo kita punya backup ukurannya rada gede ya sama aja boong gitu. Dari pengalaman pribadi waktu pake duplicati juga kaya ada yang kurang sreg aja dari GUI-nya dan sempet nemu error-error gj pas restore. Untuk pengalaman backup pake duplicacy sendiri kurang lebih 5 menit untuk backup 5G-an ke B2 (tergantung koneksi juga). 

Untuk install duplicacy sendiri emang rada ngeselin (kayanya si biar beli GUI/web-nya gitu) yaitu dengan download binary cli nya dari [release repo](https://github.com/gilbertchen/duplicacy/releases/)-nya, terus bisa di symlink-in atau taro di `/usr/local/bin/` aja. Tapi kalo udah pake [Cask](https://github.com/Homebrew/homebrew-cask)-nya dari [Homebrew](https://brew.sh/) bisa langsung install dengan `brew cask install duplicacy` yang juga bakalan install versi GUI (trial 30 hari).

Kalo udah diinstall tinggal meluncur ke folder yang pengen di backup, terus jalanin `duplicacy init <snapshot id> <storage url>`. Misal kalo dalam kasus ini yang dipake itu [Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html) maka command nya jadi `duplicacy init backup-software b2://<bucket name>` dimana bucket name adalah bucket name yang udah dibikin di b2, nanti bakal diminta keyID dan ApplicationKey yang udah dikasih sama si backblaze nya.

Udah deh, tinggal jalanin `duplicacy backup` atau tambain tag biar gampang di carinya terus tinggal deh. Btw, duplicacy ini emang minjem konsepnya git banget sih, makanya disitu jadi daya tarik sendiri.

Untuk nge-filter (exclude/include) sendiri tinggal tambain file di `./duplicacy/filters`, formatnya bisa tinggal pake `+<nama file/folder>` atau `-<nama file/folder>` dengan masing-masing pada line baru, ataupun juga bisa pake regex dengan prefix 'e' (`e:<regex-nya>`).

Untuk restore juga gampang aja sih, tinggal `duplicacy restore -r <revisi-nya>`, biar lebih akurat si biasanya tambain `-hash`.

Untuk nambain storage server lain juga gampang, tinggal tambain `duplicacy add <storage service-nya>` dan untuk list storage servicenya bisa dilihat [disini](https://github.com/gilbertchen/duplicacy/wiki/Storage-Backends). Dan untuk copy dari satu storage service ke yang lain ya tinggal jalanin `duplicacy copy`.

Kalo males add, prune, copy manual bisa juga pake [utilnya](https://github.com/jeffaco/duplicacy-util) yang lumayan ngebantu.

Dan untuk otomatisnya bisa pake [cron](https://en.wikipedia.org/wiki/Cron) ataupun [launchd](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/ScheduledJobs.html) kalo pake mac.

Kalo udah pake duplicacy semestinya versioning yang ada di backblaze juga ga usah dipake lagi. Duplicacy juga udah support kalo misal file yang ingin di backup pengen di enkrip dulu 😉️, ya walaupun sebenernya file yang diupload juga udah berupa chunk-chunk yang rada ga guna kalo diliat langsung satu-satu.