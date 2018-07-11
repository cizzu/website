+++
author = "Afriyandi Setiawan"
date = 2018-01-02T02:24:00Z
description = ""
draft = true
slug = "setup-dns-proxy-di-raspberry-pi"
title = "Setup SAMBA, Deluge dan Time Machine Backup pada raspberry pi"

+++

Beberapa waktu yang lalu setelah tahun lalu, karena lumayan banyak libur dan tidak kemana-mana jadinya suka nyari kesibukan sendiri yaitu salah satunya mengoprek raspbery pi yang lama ngejogrok di ujung lemari.

Awalnya bingung, mau dibikin apa ya tapi kemudian perhatian teralihkan ke xiaomi mi box dan inget kalo netflix di blok sama provider ISP. Karena memang punya akun netflix yang di share bareng temen, dan sering cari-cari artikel atau bahan perdebatan yang bagus di reddit (yang juga di blok sama provider ISP) maka kepikiran untuk install DNS-Proxy.

-- *Sayangnya ketika postingan ini dibuat DNS-Proxy dihapus sama yang punya repo dan berubah menjadi versi 2 yang berbeda sama sekali, Jadi pembahasan tentang DNS-Proxy dihapus dan mungkin nanti jadi pembahasan sendiri* --.

Setelah DNS-Proxy terinstall, install apa lagi ya... kemudian pandangan mata teralihkan ke hdd yang sedang nempel di mac 💡 kenapa ga sekalian dibuat samba server aja? pake hdd external kemudian setup install deluge daemon, jadi torrentbox dan NAS(-NAS-an) pribadi, cari torrent load, tinggal keluar balik lagi siap nonton deh via [vlc player](https://www.videolan.org/vlc/index.html). Sekalian setup Time Machine yang bisa diakses via LAN.

Dengan anggapan raspberry telah siap dan terinstall [raspbian stretch lite](https://www.raspberrypi.org/downloads/raspbian/). Saya menggunakan ssh untuk meremote raspberry karena memang saya tidak mensetting monitor. Lalu bagaimana setup tanpa monitor? well, saya setup mmc yang akan digunakan pada raspberry menggunakan [Etcher](https://etcher.io/) kemudian dengan menggunakan [qemu](https://www.qemu.org/) saya setup ssh, jadi ketika mmc dimasukan ke dalam raspberry ssh sudah tersetup dan siap digunakan.


Oke, setelah login menggunakan ssh, install samba dan deluge menggunakan `aptitude` dengan perintah

```bash
sudo aptitude install deluged deluge-web samba
```

Diperhatikan, yang diinstall adalah `deluged` **bukan** `deluge`. Setelah selesai install, yang pertama saya lakukan adalah mensetup hdd external dan memformatnya dengan format ext4 lalu menset auto amount dengan merubuah `/etc/fstab` dengan `sudo vim /etc/fstab` dan menambahkan satu baris seperti berikut pada file tersebut

```bash
UUID=11ecb9b3-7ae5-4ae0-9b1b-d2739b61157f /media/lugia ext4 rw 0 1
```

`UUID` didapatkan dengan perintah `sudo blkid`. `/media/lugia` adalah directory yang sebelumnya saya buat. Kemudian saya menambahkan satu user tanpa login dengan nama `shareUser` menggunakan perintah 

```bash
sudo useradd -M -s /sbin/nologin shareUser
```

kemudian menambahkannya ke samba, berikan password kemudian aktifkan dengan perintah seperti berikut

```bash
sudo smbpasswd -a shareUser
#masukan password, kemudian
sudo smbpasswd -e shareUser
```

kemudian

