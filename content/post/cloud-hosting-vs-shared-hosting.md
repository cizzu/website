---
date: 2017-05-25T08:55:54Z
description: "Membandingkan Antara Cloud Hosting dan Shared Hosting. Dan mengapa lebih baik menggunakan Cloud Hosting ketimbang Shared Hosting"
draft: false
slug: "cloud-hosting-vs-shared-hosting"
title: "Cloud Hosting vs Shared Hosting"
categories:
- opini
- review
tags: 
- web
keywords: [opini, cloud, hosting, cloud hosting, shared hosting, vps]
thumbnailImage: https://source.unsplash.com/klWUhr-wPJ8/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/klWUhr-wPJ8/

---

Seperti yang saya jabarkan pada page [about](https://www.tendabiru.net/about/) bahwa [tendabiru.net](https://goo.gl/tZDjPp) ini sebelumnya adalah sebuah forum untuk komunitas kampus penulis. Karena waktu itu kaskus sedang besar-besarnya sehingga muncul ide untuk membuat forum komunitas sendiri dari beberapa teman.
<!--more-->
<!--toc-->

Awalnya lumayan, forum tersebut lumayan aktif. Tapi dikarenakan pada waktu itu internet tidak semurah sekarang (walaupun sekarang *ga* murah-murah amat sih), dan akses internet sebagaian besar dilakukan hanya dari warnet atau menggunakan modem pada PC karena smartphone masih barang mewah pada zaman itu, sehingga lambat laun forum tersebut menjadi sepi, tidak aktif dan kemudian mati 💔.

[tendabiru.net](https://www.tendabiru.net) yang sekarang pun juga sudah melakukan banyak transformasi, mulai dari menggunakan [wordpress](https://wordpress.org), hingga sekarang menggunakan [ghost](https://ghost.org) sebagai engine blog nya. Dan tidak menutup kemungkinan berganti lagi di kemudian hari. [Tendabiru.net](https://goo.gl/tZDjPp) sendiri sudah sering menghapus postingannya, dan history dari [tendabiru.net](https://goo.gl/tZDjPp) bisa dilihat di [wayback machine](https://web.archive.org/web/*/tendabiru.net).

Oke, lalu hubungan dengan Cloud Hosting vs Shared hosting seperti judul di atas apa? Awalnya ketika [tendabiru](https://goo.gl/tZDjPp) masih menjadi forum yang menggunakan [phpbb](https://www.phpbb.com/) semua di taruh di shared hosting, dan memang pada umumnya shared hosting support php secara default. Kemudian ketika menjadi blog pribadi penulis [tendabiru.net](https://goo.gl/tZDjPp) menggunakan wordpress yang masih mengunakan PHP dan di support oleh shared hosting.

Mengunakan shared hosting memang enak, tinggal login ke cPanel, menggunakan webbase filemanager nya ataupun ftp sudah bisa upload-upload. Tidak usah pusing-pusing *mikirin* konfigurasi server, instal ini-itu, tinggal upload, install wordpress/aplikasinya kemudian jalan.

# Keterbatasan Shared Hosting

Tapi keterbatasan shared hosting membuat ketidakpuasan tersendiri bagi penulis, misal seperti apabila ada "tetangga" yang menggunakan resource secara berlebihan maka performance effect juga dialami oleh yang lain. Apabila membuat aplikasi lain yang berbasis selain PHP, hal ini hampir mustahil dilakukan di shared hosting. Walaupun ada beberapa shared hosting yang support cli, tapi hal ini masih tetap terbatas secara konfigurasi. Kadang, beberapa shared hosting juga membatasi extension-extension php tertentu. Hal ini cukup membuat pusing ketika satu saat client yang membutuhkan "ritual" khusus pada settingan non-default dari bahasa pemrograman yang digunakan.

![Pusing](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/cdo7hxzhp48ae2rnoskx.jpg)

Pada waktu itu sendiri, harga Cloud Hosting memang masih sangat mahal kisaran $50 per bulannya. Namun, beberapa tahun kebelakang harga Cloud Hosting sudah amat sangat terjangkau bagi orang kebanyakan, mulai dari $2.5 yang termurah sampai $100an untuk resource yang besar.

# Kelebihan Cloud Hosting

Sekarang apa kelebihan Cloud Hosting? Cloud Hosting memang tidak semudah shared hosting, tapi memiliki beberapa kelebihan, diantaranya adalah *full control* terhadap resource yang telah dialokasikan. Install apapun yang kita inginkan pada Cloud Hosting merupakan sesuatu hal yang wajar, dan umum. OpenVPN untuk tunneling? aplikasi web based/API yang menggunakan node js? python? go? atau bahasa lainnya?, membuat aplikasi untuk image recognition? kecerdasan buatan? menaruh file sebagai cloud pribadi? server git pribadi? dll bisa dilakukan dengan Cloud Hosting. Tinggal install aplikasi beserta library pendukungnya kemudian konfigurasi.

Dengan Cloud Hosting user juga bisa mengubah konfigurasi default dari suatu parameter bahasa pemrograman/aplikasi tertentu yang tidak dapat dilakukan oleh shared hosting. Hal ini memungkinkan untuk optimalisasi dan efisiensi sebuah aplikasi/program tertentu.

Cloud Hosting pada dasarnya sama seperti VPS, hanya istilah dan terminologi dasarnya yang sedikit berbeda.

![Cloud Hosting](https://res.cloudinary.com/tendabiru/image/upload/c_scale,w_0.3/v1495703272/raqeutzowkrcfexjrmqt.jpg#center)

# Daftar Cloud Hosting

Berikut adalah beberapa Cloud Hosting yang pernah penulis gunakan dan rekomendasikan.

## 1. [Linode](https://goo.gl/tZDjPp)

[![linode](https://www.linode.com/media/images/logos/standard/light/linode-logo_standard_light_large.png)](https://goo.gl/tZDjPp) 

Cloud Hosting dengan harga miring yang pertama kali penulis tahu adalah [linode](https://goo.gl/tZDjPp). Pertama kali didirikan pada tahun 2011, [linode](https://goo.gl/tZDjPp) menggunakan XEN sebagai virtualizationnya, kemudian pada tahun 2015 [linode](https://goo.gl/tZDjPp4) pindah ke KVM. [Linode](https://goo.gl/tZDjPp) merupakan salah satu pencetus Cloud Hosting murah dengan system unmanaged pertama. unmanaged berarti [linode](https://goo.gl/tZDjPp) tidak bertanggung jawab atas maintenance maupun pengurusan Cloud Hosting yang kita miliki.

## 2. [DigitalOcean](https://goo.gl/5itdve)

[![DigitalOcean](https://www.digitalocean.com/assets/media/logos-badges/png/DO_Logo_Vertical_Blue-6321464d.png#center)
](https://goo.gl/5itdve)

[DigitalOcean](https://goo.gl/5itdve) pertama kali muncul sekitaran tahun 2014. [DigitalOcean](https://goo.gl/5itdve) tampil kepermukaan dengan sangat menawan, tampilan yang modern, API yang sangat komplit, harga yang murah, dan dukungan untuk komunitas yang sangat baik, [DigitalOcean](https://goo.gl/5itdve) langsung menarik banyak minat pengguna, khususnya para developer.

Dengan dukungan dan dokumentasi yang lumayan komplit serta kemudahan membuat droplet ([DigitalOcean](https://goo.gl/5itdve) menyebut Cloud Hosting nya dengan istilah droplet), [DigitalOcean](https://goo.gl/5itdve) cocok bagi pengguna yang pertama kali menggunakan Cloud Hosting.

## 3. [Vultr](https://goo.gl/Mn2ThW)

[![VULTR](https://www.vultr.com/media/logo_onwhite.png#center)
](https://goo.gl/Mn2ThW) 

[Vultr](https://goo.gl/Mn2ThW) bisa dibilang pendatang baru, namun bukan berarti [Vultr](https://goo.gl/Mn2ThW) bisa dianggap remeh. Beberapa bulan kebelakang [Vultr](https://goo.gl/Mn2ThW) melakukan expansi besar-besaran dengan menambahkan beberapa lokasi Cloud Hosting.

Walaupun secara tampilan [Vultr](https://goo.gl/Mn2ThW) bisa dibilang mengikuti [DigitalOcean](https://goo.gl/5itdve), tapi [Vultr](https://goo.gl/Mn2ThW) menambahkan beberap fitur yang tidak ada di [DigitalOcean](https://goo.gl/5itdve) seperti firewall, snapshot yang gratis, dan harga yang lebih miring ketimbang [DigitalOcean](https://goo.gl/5itdve) dan [Linode](https://goo.gl/tZDjPp), untuk harga termurahnya hanya $2.5.

Untuk saat ini, tendabiru sendiri menggunakan [Vultr](https://goo.gl/Mn2ThW) sebagai Cloud Hostingnya.

# Kesimpulan

Dari 3 Cloud Hosting di atas, masing-masing memiliki kelebihan tersendiri. Untuk yang baru pertama kali ingin menggunakan Cloud Hosting, penulis merekomendasikan [DigitalOcean](https://goo.gl/5itdve), karena dokumentasi dari komunitas yang lumayan komplit, dan deploy droplet yang mudah dan cepat.

Tapi bagi yang ingin Cloud Hosting dengan harga yang jauh lebih *miring*, maka bisa mencoba vultr yang menawarkan harga miring dengan resource yang sama dengan [DigitalOcean](https://goo.gl/5itdve) ataupun [Linode](https://goo.gl/tZDjPp).
