---
author: "Afriyandi Setiawan"
title: "Aplikasi Dengan Touch ID iOS - part 1"
description: "Membuat aplikasi dengan feature touch ID yang ada pada iPhone/iPad atau menggunakan passcode - part 1"
date: 2017-06-10T14:46:39Z
draft: false
slug: "aplikasi-dengan-touchid-ios-part-1"
categories: 
- Notes
tags: 
- development
- swift
keyword: [swift, iOS, apple, iPhone, touchID, xcode, xcode project]
thumbnailImagePosition: "top"
thumbnailImage: //res.cloudinary.com/tendabiru/image/upload/q_auto:good/jckmr55tuxygcxqngkni.jpg
coverImage: //res.cloudinary.com/tendabiru/image/upload/q_auto:good/jckmr55tuxygcxqngkni.jpg

---

Sebelum memulai postingan berikut, ada baiknya untuk mengetahui apa itu touch ID. Mungkin yang hari-harinya sudah terbiasa menggunakan iPhone, telah mengenal dan menggunakan touch ID ini, touch ID sendiri adalah feature pengenalan sidik jari yang dikembangkan dan dikeluarkan oleh [Apple](https://en.wikipedia.org/wiki/Apple_Inc.) dan menjadi standart atau feature yang harus ada pada iPhone dimulai dari iPhone 5S, kemudian berkembang juga ke Macbook Pro pada akhir 2016.
<!-- more -->

Touch ID pada iPhone/iPad dibuat menjadi satu dengan home button menggunakan bahan crystal saphire yang tidak mudah tergores, serta dilengkapi dengan ring stainless yang dapat mendetek jari tanpa perlu menekannya.

Touch ID juga dapat menggunakan passcode, semacam keypad yang dapat menerima inputan pin apabila beberapa kali jari yang terdaftar tidak dapat dikenali oleh sensor touch ID.

# Inisialisasi Project

Agar aplikasi dapat menggunakan feature touch ID ini, API atau framework yang digunakan adalah `LocalAuthentication`.

Tujuan akhir dari aplikasi ini nantinya,

1. Aplikasi dapat menentukan apakah ingin menggunakan feature touch ID atau tidak.
2. Apabila feature touch ID dinyalakan, maka user harus mengatur pin yang digunakan sebagai backup apabila touch ID gagal.
3. Setelah feature touch ID aktif, Aplikasi ketika berjalan akan meminta autentikasi dengan menggunakan touch ID. 
4. Apabila touch ID gagal, maka user akan dihadapkan pada halaman passcode yang sebelumnya telah ditentukan pin-nya.
5. Untuk device yang tidak memiliki touch ID maka secara default akan dihadapkan pada halaman passcode.

Untuk memulai, seperti biasa buat project baru pada xcode.

![add project](https://res.cloudinary.com/tendabiru/image/upload/q_20,w_0.6/v1491643513/jslkbnzykeigvpudgypc.png)

Saya akan menggunakan file-file default yang telah disediakan oleh xcode, oleh karena itu buka file `ViewController.swift` kemudian import framework `LocalAuthentication` pada file tersebut.

```swift
import LocalAuthentication
```

Setelah itu, design sebuah switch dan label untuk mengaktifkan touchID pada `Main.storyboard`

![switch activate touch id](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/g4miaolyqj9ppdo2awdf.png#center)

Kemudian, tentukan action untuk switch tersebut dengan memilih `assistant editor` terlebih dahulu, kemudian tahan tombol `ctrl` kemudian klik dan tarik switch tersebut ke fiel `ViewController.swift` yang terbuka di sebelah kanan

![add action to switch](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/gbre8k9slqidnz4ysbsa.png#center)

Pilih action pada opsi Connection, kemudian beri nama function-nya dengan `onActivate`.

![add action name](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/p7tyfcgdpjuqctqztdtt.png#center)

Setting Switch value menjadi off pada attribute inspector.

![switch off default](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/eceqymfpapp9odh4mqu8.png#center)

Langkah berikutnya, tambahkan satu buah ViewController lagi yang akan kita gunakan sebagai halaman passcode untuk mengatur pin.

![new viewcontroller](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/bl9sllyhlgqokdkdtubg.png)

Tambahkan class viewcontroller baru dengan membuat file swift, yang nantinya bertanggung jawab terhadap ViewContoller yang tadi ditambahkan. Saya akan memberi nama class itu dengan `PassCodeViewController`

![new file](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/jrxh2rnmrwp9vr9yvajb.png)

Kembali lagi ke storyboard, pilih view controller baru tersebut kemudian pada bagian identity inspector ganti menjadi class yang sebelumnya telah dibuat

![passcode view controller](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/zmevqkznjzsspdmv7zkz.png#center)

Tambahkan 9 button untuk masing-masing angka yang akan digunakan sebagai input pin, dan satu buat button untuk menghapus atau membatalkan inputan. Dan juga 4 buah view yang digunakan sebagai indicator masukan angka. Untuk hal ini saya akan menggunakan bantuan `Stack View`.

Dan kemudian kira-kira demikian hasil akhirnya,

![passcode view](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/wmpdcspfhz5klaf11xwa.png#center)

Untuk membuat frame pada buttonnya maka saya akan membuat extension pada UIButton seperti berikut

```swift
extension UIButton {

    @IBInspectable var cornerRadius: CGFloat {
        get {
            return layer.cornerRadius
        }
        set {
            layer.cornerRadius = newValue
            layer.masksToBounds = newValue > 0
        }
    }

    @IBInspectable var borderWidth: CGFloat {
        get {
            return layer.borderWidth
        }
        set {
            layer.borderWidth = newValue
        }
    }

    @IBInspectable var borderColor: UIColor? {
        get {
            return UIColor(cgColor: layer.borderColor!)
        }
        set {
            layer.borderColor = newValue?.cgColor
        }
    }
}
```

Maka pada bagian Attributes inspector akan muncul tambahan sebagai berikut

![storyboard extension](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/xqvombs1b1g4wf113mzf.png#center)

Setting `Corner Radius` dengan angka berapapun tapi kurang dari ukuran button. Dan tentukan `Border Width`  serta `Border Color`nya.

Selanjutnya, buat segue antara `ViewController` dan `PassCodeViewController` dengan type `show detail`.

![show detail](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/fbekrd9whibgzlwvbl6m.png)

![](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/ytq5vs19q1vdirgmiupe.png#center)

Beri nama pada segue tersebut dengan `goToPassCode`, Selanjutnya, kembali lagi ke `ViewController.swift` pada tambahkan satu function berikut

```swift
@IBAction func unwindToMain(segue: UIStoryboardSegue) {}
```

Function tersebut adalah function unwind yang digunakan untuk kembali ke viewcontroller sebelumnya setelah segue.

Untuk mengaktifkan unwind segue tersebut, kembali ke `Main.storyboard`, pada `PassCodeViewController` tahan ctrl kemudian tarik ke bagian Exit, lalu pilih `unwindToMainWithSegue`. Beri nama identifiernya dengan `unwindToMain`.

![drag to exit](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/yq7tkrjyursysi3pgfl5.png)

![choose unwind](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/cg6jk6alw4unkddozbki.png#center)

![give identifier](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/utg4k84lwxgrlex1g7w5.png#center)

Kembali ke `ViewController.swift` pada function `onActivate` jalankan segue dengan code berikut

```swift
@IBAction func onActivate(_ sender: UISwitch) {
    self.performSegue(withIdentifier: "goToPassCode", sender: self)
}
```

Coba jalankan pada simulator. Untuk fungsi segue, akan dijabarkan berikutnya

![preview segue](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/uufv0pysmdro0elzvcbb.gif#center)

# Setting Pass Code

Pada file `PassCodeViewController`, tambahkan satu variable boolean yang menentukan apakah passcode yang muncul adalah untuk setting atau untuk memverifikasi. Hal ini guna mengurangi redudansi dan penghematan `ViewController` sehingga satu ViewController yang mempunyai fungsi tidak jauh berbeda dapat digunakan berulang kali, dan perubahan hanya dilakukan pada saat runtime saja.

```swift
var isSetting:Bool?
```

Mengapa optional? untuk mempersingkat ketika pengecekan value saja.

Kembali ke `ViewController.swift` tambahkan function yang akan panggil sebelum segue berjalan, yaitu function `prepare(for segue: UIStoryboardSegue, sender: Any?)`. Dari variable segue kita bisa mendapatkan destination class segue-nya, yaitu `PassCodeViewController` dan mempassing/merubah value variable isSetting yang ada pada class tersebut. Dalam notasi code dijabarkan sebagai berikut

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if let dest = segue.destination as? PassCodeViewController {
        dest.isSetting = true
    }
}
```

Dengan begitu, ketika class `PassCodeViewController` ter-load maka variable `isSetting` sudah mempunyai value `true`.

Oke, class `ViewController` untuk saat ini sudah selesai. Buka kembali `Main.storyboard` dan buat fungsi untuk action apabila tombol angka ditekan. Masing-masing tombol dapat diwakili oleh satu function tersebut. Kemudian satu function untuk button delete.

![](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/mvfskmzzoezxwufkhmqd.png#center)

![](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.6/znspuwxyrcqachkkjm9y.png#center)

```swift
@IBAction func onButtonTap(_ sender: UIButton) {
}

@IBAction func onUndoTap(_ sender: UIButton) {
}
```

Demikian untuk posting kali ini, untuk selanjutnya akan dijabarkan pada post berikutnya, stay tuned 😉 
