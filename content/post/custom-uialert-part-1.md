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

Tidak seperti `UIAlertView` yang merupakan turunan dari `UIView`, `UIALertController` merupakan turunan dari `UIViewController` sehingga mempunyai beberapa kelebihan yang tidak dimiliki oleh `UIView`. Seperti bagaimana alert tersebut ditampilkan, bisa memiliki beberapa `UIView` dan custom view di dalamnya.

Tapi kali ini saya tidak akan membahas perbedaan dari keduanya, tapi lebih bagaimana membuat sebuah class extension dari class `UIViewController`.

![upgrade](https://source.unsplash.com/Te48TPzdcU8/1800x620)

