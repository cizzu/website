---
author: "Afriyandi Setiawan"
title: "SourceTree vs GitKraken"
description: "Kelebihan dan Kekurangan yang ada pada SourceTree maupun GitKraken. "
categories: 
- review
- opini
date: 2017-08-13T04:35:00Z
draft: false
slug: "sourcetree-vs-gitkraken"
tags:
keywords:
- sourcetree
- gitkraken
- review
thumbnailImage: https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/xcbpodxcces4y16nhsla.png
thumbnailImagePosition: "top"
coverImage: https://res.cloudinary.com/tendabiru/image/upload/q_auto/xcbpodxcces4y16nhsla.png
---

Untuk yang biasa menggunakan git, mungkin sudah familiar dengan yang namanya [SourceTree](https://www.sourcetreeapp.com/) ataupun [GitKraken](http://). Dua-duanya saya pribadi pakai, dan dua-duanya lumayan membantu ketika ingin melihat atau trace project. Namun dari keduanya juga memiliki kekurangan dan kelebihannya masing-masing.
<!--more-->
<!-- toc -->

# SourceTree
SourceTree adalah salah satu git gui client yang pertama kali saya gunakan. Dikembangkan oleh [Atlassian](http://www.atlassian.com/company) yang juga mengembangkan [Bitbucket](https://bitbucket.org/product) yang merupakan salah satu git server yang bisa diandalkan. SourceTree membawa git-nya sendiri, jadi apabila pada system komputer belum terinstall git tidak akan menjadi masalah, namun apabila user telah install git sebelumnya bisa dipilih apakah menggunakan git yang dibawa oleh SourceTree atau menggunakan git yang telah teinstall pada system. SourceTree juga mendukung git-flow yang juga telah tertanam di dalamnya, serta pastinya git-lfs. 

Selain git, SourceTree juga mendukung Mercurial. User bisa menitegrasikan dengan akun github dan pastinya akun bitbucket. Update dapat dilakukan secara langsung tanpa mendownload ulang dari situsnya lagi (pada Mac) dan diberikan notifikasi apabila versi terbaru telah dirilis.

SourceTree dapat diunduh dan digunakan secara gratis, namun perlu license yang didapatkan dengan mendaftar sebagai user atlassian.

Pada main aplikasinya kita bisa membuat folder/grouping untuk project2 yang sekiranya sama.
![](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/lfktajuevopcxy07ceoa.png#center)
Apabila user telah meintegrasikan dengan akun github ataupun bitbucket maka user dapat melihat langsung semua code yang di host pada akun github atau bitbucket nya pada bagian remote.

Setelah pilih project, maka aplikasi terbagi menjadi 3 bagian, panel sebelah kiri sebagai grouping untuk workspace, remotes, dll. Bagian tengah untuk me-list apabila ada perubahan pada file tertentu, dan sebelah kanan sebagai preview apa saja yang berubah

![](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/ybn6ngozlur7b7m7myrt.png#center)

Apabila ada conflict ketika merge, dapat menggunakan feature bawaaan yang ada pada sourcetree secara interaktif.

Stashing, submodules, dan subtree sangat nyaman digunakan pada SourceTree.

Yang aga mengganggu dengan SourceTree adalah ketika staging file yang hanya menggunakan tanda tick (✓). Terkadang  ketika kurang jeli file/folder yang tidak seharusnya di-commit masuk jadi bagian commit.

# GitKraken
GitKraken sendiri saya temukan ketika saya intens menggunakan linux, karena SourceTree tidak tersedia untuk linux maka ketika itu saya mencari alternatif git gui yang bagus untuk linux.

GitKraken sendiri sangat-sangat eye-catchy dan menarik. Dengan dark theme yang diusungnya, serta kombinasi warna yang pas membuatnya nyaman dimata. Mungkin karena menggunakan Electron sebagai frameworknya  membuat gitkraken sangat interaktif dan fluid. Selain itu GitKraken menggunakan custom git yang (sepertinya) dibuat menggunakan node.js sehingga sama seperti SoureTree, user tidak perlu menginstall git pada systemnya. Namun, hal ini menyebabkan git yang ada pada GitKraken tidak dapat dibuka apabila user sedang intens menggunakan terminal (males buka gui-nya).

GitKraken dapat diintegrasikan dengan Github dan Bitbucket, namun pada versi gratisnya hanya dapat menggunakan satu akun saja.

Staging terbagi 2,yaitu unstaged file dan staged file tidak seperti SourceTree (yang menurut saya) bias.

![](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/t3zjfmvgb7rdmfjf0evi.png)

Pada GitKraken feature yang menarik lainnya adalah merge dapat dilakukan dengan drag & drop branch-nya saja. Commit yang membedakan antara Summary dan Description, sehingga lebih nice kalo dibaca di server.

Dari kelebihan di atas ada beberapa hal yang mengganggu pada GitKraken (selain feature yang ada pada versi berbayar), ketika membuka file tertentu, untuk kembali ke layar history git nya harus menekan tombol silang yang ada di pojok kanan, hal ini lumayan membingungkan ketika awal-awal menggunakan GitKraken karena tampilannya langsung terganti dan apabila memilih historynya tidak langsung berubah kembali menjadi layar history. Apabila punya multiple git server, tidak dapat langsung push kesemua git server sekaligus.

Demikian kiranya yang dapat saya jabarkan bedasarkan pengamalan menggunakan SoureTree dan GitKraken. Pendapat-pendapat di atas sifatnya subjektif, apabila tidak setuju ataupun ingin memberi tambahan silahkan disampaikan melalui kolom komentar di bawah.