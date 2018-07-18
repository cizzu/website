---
title: "Migrasi Dari Ghost"
date: 2018-07-11T09:12:22+07:00
categories:
- opini
tags:
- web
- hugo
keywords: [hugo, website, ghost, jekyll, static site, static page, html, static site generator]
draft: false
thumbnailImage: https://source.unsplash.com/fJTqyZMOh18/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/fJTqyZMOh18/

---
Sebelumnya [cizzu.com](http://cizzu.com) menggunakan [ghost](https://ghost.org/) sebagai blog engine-nya, menggunakan server ubuntu yang di host di [Linode](https://goo.gl/tZDjPp), dengan biaya kurang lebih $10 - $12 per bulan. Dengan status sekarang yang sudah berkeluarga, rasanya pengeluaran segitu cukup lumayan juga 😅 apalagi servernya memang digunakan hanya untuk [cizzu.com](http://cizzu.com) saja, tanpa ada service atau aplikasi apa-apa lagi.
<!--more-->
<!--toc-->
# Static Web Page

Setelah pikir-pikir, alternatif apa ya yang bisa mengurangi pengeluaran di atas, solusi yang simple, mudah dan perubahan yang tidak terlalu banyak.
Setelah baca sana-sini, sepertinya membuat web yang sifatnya statis ([static web page](https://en.wikipedia.org/wiki/Static_web_page)) bisa menjadi solusinya.

![blog](https://source.unsplash.com/y02jEX_B0O0/1800x620)

Apa itu static web page? Secara singkat, static web page adalah halaman web yang disajikan apa adanya dalam bentuk html. Apa adanya disini berarti halaman web yang disajikan sudah didefinisikan sebelumnya, tidak dinamis seperti web-web pada umumnya. Ini berarti server hanya menyimpan file html saja, tanpa perlu database, dan webserver untuk mengelola datanya.

Dulu, Pada awal perkembangan internet hal ini wajar dilakukan, karena memang belum ada server side processing/scripting seperti PHP, ASP, dll untuk membuat web dinamis. Namun seiring berjalan waktu, web yang kita ketahui sekarang rata-rata adalah web dinamis yang memerlukan web server untuk mengelola datanya.

Static web page bukan berarti tanpa server, tapi hanya perlu server dengan spesifikasi *yang bisa dibilang* sangat minimal, karena memang tidak diperlukan resource yang digunakan untuk mengelola data tertentu.

Untuk membuat static web page bisa menggunakan berbagai cara, kalau dulu mungkin yang terkenal dreamweaver untuk membuat page html, sekarang ada yang namanya static site generator. semacam framework yang digunakan untuk menggenerate file html yang dihasilkan dari format-format text file tertentu. Framework ini juga dapat menggenerate file-file pendukung lainnya seperti javascript, dan css.

---

# Migrasi dari ghost

Pada awalnya sempat mencoba untuk menggunakan [Buster](https://github.com/axitkhurana/buster/) yang dapat menarik static file dari instalasi ghost, tapi development yang sudah lama tidak diupdate menjadikan niatan itu hilang.

Ada beberapa pilihan static site generator di [staticgen](https://www.staticgen.com/), antara lain  [Hugo](https://gohugo.io/), [Jekyll](https://jekyllrb.com/), [Next](https://nextjs.org/learn/), [Hexo](https://hexo.io/), dll. Semuanya merupakan engine/framework yang menggunakan [markdown](https://en.wikipedia.org/wiki/Markdown) untuk menggenerate konten utamanya, theme (dengan engine tertentu), untuk kemudian dirakit dan dijahit menjadi satu static website html tertentu.

Dari ke 4 contoh di atas,  2 diantaranya yang menarik perhatian yaitu, [Jekyll](https://jekyllrb.com/) dan [Hugo](https://gohugo.io/) .

## Jekyll

![Jekyll](https://res.cloudinary.com/tendabiru/image/upload/c_scale,w_450/v1531379452/jekyll_wsxtzv.png)

Jekyll dicetuskan pertama kali pada tahun 2008 oleh pendiri [github](https://github.com) Tom Preston-Werner, yang menggunakan bahasa [ruby](https://www.ruby-lang.org/en/) untuk generatornya dan menggunakan [liguid engine](https://shopify.github.io/liquid/) milik [shopify](https://www.shopify.com/) untuk theme engine nya. Jekyll bisa dibilang sebagai penyebab utama static site menjadi populer.

Jekyll memiliki dukungan thema yang banyak, mulai dari yang gratis sampai premium yang berbayar. Jekyll memiliki dukungan integrasi dengan github pages yang sangat baik, sehingga menyebabkan Jekyll static site generator paling populer dengan 34k bintang di [github](https://github.com/jekyll/jekyll).

### pro

* template engine yang sederhana.

* theme yang banyak.

* integrasi [github pages](https://pages.github.com/).

* mendukung plugin.

### con

* Bedasarkan beberapa test, mengenerate static site menggunakan Jekyll termasuk lambat.
* perlu maintain versi ruby dan plugin-pluginnya.

## Hugo

![Hugo](https://raw.githubusercontent.com/gohugoio/hugoDocs/master/static/img/hugo-logo.png)

Hugo, sama seperti Jekyll, merupakan framework untuk menggenerate  static site. Bedanya, hugo menggunakan bahasa [go](https://golang.org/). Dicetus pertama kali pada tahun 2013 oleh Steve Francia and Bjørn Erik Pedersen. Untuk theme menggunakan [Go’s template package](https://golang.org/pkg/html/template/).

Hugo merupakan static site generator nomer 2 yang populer, dan memiliki 27k bintang di [github](https://github.com/gohugoio/hugo). Hugo juga memiliki library [theme yang beragam](http://themes.gohugo.io/), namun sejauh ini belum ditemukan theme yang premium atau berbayar.

### pro

* build time yang sangat cepat.

* fungsi-fungsi bawaan yang komplit.

* support multi bahasa.

* tidak perlu maintenace bahasa go, karena sudah menjadi satu executable file tersendiri (apabila menginstall executable-nya).

### con

* tidak ada plugin

---

Setelah timbang-timbang, dari 2 framework di atas, akhirnya pilihan jatuh kepada [hugo](https://gohugo.io/). 

Hugo menjadi pilihan karena memang pada dasarnya penulis lebih nyaman bekerja menggunakan file yang sudah menjadi executable, jadi tidak perlu me-maintain source/file-file pendukungnya, instalasi hugo bisa menggunakan [homebrew](https://brew.sh/) dan hanya perlu memberikan perintah `brew install hugo`.

Postingan-postingan sebelumnya ketika masih menggunakan ghost pun dapat di export dengan mudah menggunakan aplikasi go [ghostToHugo](https://github.com/jbarone/ghostToHugo) yang otomatis menggenerate semua file markdown termasuk front-matter beserta metanya.

Untuk themes, yang digunakan adalah [hugo-tranquilpeak-theme](https://github.com/kakawait/hugo-tranquilpeak-theme) milik [kakawait](https://github.com/kakawait) yang sudah di[modifikasi](https://github.com/aphe/hugo-tranquilpeak-theme/tree/cizzu_com) sebelumnya.

Itulah sedikit banyak mengenai update cizzu, yang sebelumnya pernah menggunakan [wordpress](https://wordpress.org/), [ghost](https://ghost.org/), dan pada akhirnya untuk menghemat budget menggunakan static site generator.

Untuk detail teknikalnya akan saya jabarkan pada post berikutnya.

Salam, Afriyandi Setiawan.