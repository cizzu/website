---
title: "Custom UIAlert - Part 1"
date: 2018-12-03T13:32:12+07:00
categories:
- tutorial
tags:
- development
- swift
keywords: [swift, uialert, uialertcontroller, custom uialert, ios]
draft: true
thumbnailImage: https://source.unsplash.com/oMqswmrie4Y/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/oMqswmrie4Y/
---

Setelah hampir 6 bulan blog ini tidak di *tengokin*, akhirnya curi-curi waktu luang buat update themes dan isinya.

Masih seperti sebelum-sebelumnya, kemampuan menulis yang rendah membuat saya bingung mau update apa. Terlebih jumlah postingan juga segitu-segitu aja 😅.

Tapi akhirnya kepikiran untuk membagikan tentang satu class yang beberapa projek kebelakang sering digunakan, yaitu `UIAlertController`.
<!--more-->

Sebelum `UIALertController` ada, pada platform iOS menggunakan class dengan nama `UIAlertView` untuk urusan alert. Namun, pada iOS 8 dibuat deprecated oleh Apple dan digantikan dengan `UIAlertController` yang sekaligus menggantikan class `UIActionSheet`. 

![upgrade][image-1]

### Cara Standart
Tidak seperti `UIAlertView` yang merupakan turunan dari `UIView`, `UIALertController` merupakan turunan dari `UIViewController` sehingga mempunyai beberapa kelebihan yang tidak dimiliki oleh `UIView`. Seperti bagaimana alert tersebut ditampilkan, bisa memiliki beberapa subview dan custom view di dalamnya. Serta dapat menggunakan file `.xib`.

Buat project baru dengan nama `customAlert` pada bagian [ viewDidLoad() ][1] tambahkan baris kde berikut,

```swift
let alert = UIAlertController(title: "Alert", message: "Hi", preferredStyle: .alert)
```

Untuk memanggil `UIALertController` pun sama, yaitu dengan memanggil [present(\_:animated:completion:)][2],

```swift
self.present(alert, animated: true) {}
```

Coba jalankan di simulator, maka dipastikan tidak akan tampil apa-apa 😂 dan malah akan ada pesan di console window sebelah kanan bawah seperti berikut,

> Warning: Attempt to present \<UIAlertController: 0x7fe5fa823400\> on \<customAlert.ViewController: 0x7fe5fa608520\> whose view is not in the window hierarchy!

hal ini dikarenakan code berjalan ketika view dari class ViewController belum di load ke dalam window hierarchy (translate aja kalo ga percaya), dengan artian view dari class tersebut masih berupa buffer memory.

Pindahkan 2 baris code tersebut ke method [ viewDidAppear(_:)][3], lalu jalankan ulang maka akan didapatkan alert seperti berikut,

![][image-2]

Tapi seperti halnya 

[1]:	https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload "viewDidLoad()"
[2]:	https://developer.apple.com/documentation/uikit/uiviewcontroller/1621380-present
[3]:	https://developer.apple.com/documentation/uikit/uiviewcontroller/1621423-viewdidappear

[image-1]:	https://source.unsplash.com/Te48TPzdcU8/1800x620
[image-2]:	/images/custom-uialert-part-1/B1SW9IDKcv.png