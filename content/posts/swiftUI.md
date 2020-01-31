---
title: "SwiftUI Perception"
date: 2019-06-23T00:05:19+07:00
categories:
- opini
- programming
tags:
- development
- swift
keywords:
- tech
draft: false
thumbnailImage: https://source.unsplash.com/4-iZ147pSAE/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/_4815u_ACqQ/
---

Hi, wwdc 2019 baru aja berlalu beberapa waktu yang lalu. Salah satu yang menarik perhatian adalah [SwiftUI](https://developer.apple.com/xcode/swiftui/). Dan dalam post kali ini akan coba dijelaskan _perception_ dari SwiftUI.

<!--more-->

### Disclaimer
+ Post ini kemungkinan bakalan banyak berubah. ya soalnya masih beta.
+ Ini hanya _perception_ dan bukan _insight_, dokumen lengkapnya belum dibaca.
+ Mencoba untuk tidak googling.

Kalo mencatut langsung dari situsnya, dijelaskan bahwa 

> SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs. With a declarative Swift syntax that’s easy to read and natural to write, SwiftUI works seamlessly with new Xcode design tools to keep your code and design perfectly in sync. Automatic support for Dynamic Type, Dark Mode, localization, and accessibility means your first line of SwiftUI code is already the most powerful UI code you’ve ever written.

Ya bisa diartiin sendiri lah ya, `SwiftUI` ini salah satu _set of tools_ dan API untuk "mendikte" UI dengan cara deklaratif. Mungkin mirip-mirip widget di flutter kali ya (CMIIW).

![blog](https://source.unsplash.com/OXkUz1Dp-4g/1800)

SwiftUI ini juga mendukung semua ekosistemnya Apple (berharap bisa mendukung android, web, dll juga kelak), jadi paling engga bisalah _single codebase_ untuk UInya.

Pertama-tama, perlu diketahui SwiftUI itu beda ya dengan `UIKit`. Kalo pendapat pribadi sih, `SwiftUI` ini lebih ke turunan dari `UIView` (ya walaupun UIView juga objectnya si `UIKit`) dan memanfaatkan ke _powerfull_-an si swift. Oh iya, hal ini juga bisa terwujud karena kekuatan dari [combine](https://developer.apple.com/documentation/combine).

# KickStart

Untuk menjajal kecanggihan dan keupdatean swiftUI ini, diperlukan [macOS Catalina](https://www.apple.com/macos/catalina-preview/) dan XCode beta 11.0, untuk mendapatkan hasil yang sama kaya dipost kali ini, pake Xcode beta 11.0 beta 2. Tambahan, untuk xcode beta 11.0 ini ada feature yang mantab jiwa, yaitu side preview seperti yang ada di sublime 👌 maknyus...

Klik new file > swiftUI, maka akan tampil opsi untuk swiftUI.
![SwiftUI](https://res.cloudinary.com/tendabiru/image/upload/c_scale,q_20,w_1000/v1561226192/CCbDNdYHnD_ueesxy.png)

Awalnya coba untuk integrasi di existing project-nya [kitabisa](https://apps.apple.com/id/app/kitabisa/id1458307938) #orangbaik tapi setelah beberapa menit dan kipas yang berputar dengan kencangnya, percobaan tersebut berakhir dengan gagal untuk menampilkan preview dari `SwiftUI` seperti yang ditampilkan di video wwdc.

![failed](https://res.cloudinary.com/tendabiru/image/upload/v1561357257/khiWahyFN7_fvpotk.png)

Akhirnya setelah beberapa kali coba, dan gagal terus memutuskan untuk membuat project dummy saja.

Namun, ada yang berbeda juga ketika kita akan membuat project di xcode 11 beta ini, yaitu opsi untuk menggunakan `SwiftUI` seperti gambar di bawa

![Opsi Swift UI](https://res.cloudinary.com/tendabiru/image/upload/v1561357307/zKtTeplGTN_upin1h.png)

Tapi perlu diingat, dengan memilih opsi tersebut maka project akan berbasiskan pada `SwiftUI` bukan `UIKit`. Jadi susunannya pun berbeda. Ada file SceneDelegate.swift disitu, apabila UIKit memiliki UIViewController Lifecycle, pada `SwiftUI` sepertinya menggunakan apa yang disebut UISceneSession Lifecycle.

Dikarenakan saya belum mengetahui mendalam tentang hal tersebut, saya kemudian berfikir mungkin ga gabungkan `UIKit` dengan `SwiftUI`? Coba buat project baru, tapi ga usah pilih opsi diatas.

![Scene delegate](https://res.cloudinary.com/tendabiru/image/upload/v1561357339/gkDmTj7koO_f0clup.png)

Oh, OK sepertinya SceneDelegate.swift ini akan jadi hal baru nantinya, karena create project biasa pun file tersebut masih ada.

Oke, kita coba tambahkan file `SwiftUI` ke dalam default UIViewController yaitu ViewController.swift

![Preview](https://res.cloudinary.com/tendabiru/image/upload/v1561357367/9b8tunfyN6_plikm7.png)

Nah, berhasil preview pun tampak.

Kalau diliat dari APInya, sepertinya semua dideklarasi sebagai struct a.k.a passing value type 👍 cool.

Ok, preview udah jalan, sekarang gimana masukinnya ke dalam `UIViewController`?

Hm, karena `SwiftUI` turunannya `UIView`, gimana kalo coba langsung di addsubview?

![addSubView](https://res.cloudinary.com/tendabiru/image/upload/v1561357389/BtjGmTN75G_c9nf4z.png)

Hm, ternyata error lur. Iyalah, kan `SwiftUI` struct, struct ga bisa punya turunan, dan `View` yang ada di file Content.swift bukan `UIView` tapi protocol `View`.

Jadi gimana integrasinya?

Ok, searching-searching di file API-nya menggunakan keyword `UIViewController`, ada tiga yang menarik yaitu `UIHostingController`, `UIViewControllerRepresentable` dan `UIViewRepresentable`.

UIHostingController sepertinya jelas, turunan dari `UIViewController`, jadi bisa secara mudah diintegrasi ke projek berbasis UIKit. Bagaimana dengan `UIViewControllerRepresentable` & `UIViewRepresentable`? ini protocol, berarti harus ada implementasinya.

Coba langsung implement di Constant, error dong.

![UIViewControllerRepresentable](https://res.cloudinary.com/tendabiru/image/upload/v1561357406/eCOzLPFTcU_eizta5.png)

Hm, ok skip dulu 🤣 lumayan males baca-baca bawahnya. Kita liat satu lagi yaitu `UIHostingController` yang merupakan sebuah class, jadi jelas lah ya. Oke coba implement di ViewController.swift

Pertama harus import SwiftUI dulu, init contentView nya, ikat dengan UIHostingController, kasih frame. done, good to go.

coba dijalankan...

![screenshot](https://res.cloudinary.com/tendabiru/image/upload/v1561357419/i3t92NAL5H_zllhn5.png)

## Kesimpulan
+ SwiftUI memang benar-benar baru, dan tidak bergantung kepada UIKit sama sekali.
+ Integrasi dengan existing project masih sulit.
+ Pengimplementasian full swift, dan kayanya bisa diimplementasi ke platform lain selama masih pake swift.
+ Masih banyak error di beta ini.
+ post bakalan di update 
