---
date: 2017-04-02T05:54:00Z
description: "Membuat localization pada aplikasi iOS yang support dengan tag tertentu seperti <b>bold</b> <i>italic</i>."
draft: false
slug: "ios-localization-menggunakan-tag-tertentu"
categories:
- Notes
tags: 
- development
- swift
keywords: [swift, iOS, tutorial, localization, translate, bahasa]
title: "iOS localization menggunakan tag tertentu"
thumbnailImage: https://images.unsplash.com/photo-1501514799070-290ae1c889fe
thumbnailImagePosition: "top"
coverImage: https://images.unsplash.com/photo-1501514799070-290ae1c889fe
---

# Mukadimah

Pernah ketemu kasus localization app dengan beberapa kata di **bold** atau di *emphasize*?

Untuk HTML mungkin gampang, tinggal tambahin `<b>bold</b>` atau `<i>italic</i>` kalau mau menebalkan atau memiringkan kata tertentu. Tapi bagaimana dengan iOS, dalam kasus ini untuk localization.

Berikut bahasannya, diawal akan dijelaskan secara mendetail mengenai menyiapkan localization atau terjemahan untuk aplikasi, cara menggunakan file localization, dan menggunakan tag-tag tertentu untuk mengubah sebagian dari terjamahan tersebut.
<!--more-->
<!--toc-->

# Langkah Awal: - Mempersiapkan Localization file

Untuk hal ini, saya mempersiapkan satu project dummy, dengan 3 bahasa berbeda (germany, japan, dan Indonesia) dengan base-nya berbahasa inggris.

Untuk case ini saya menggunakan versi xcode terbaru ketika menulis tulisan ini (Version 8.3 (8E162)) dengan menggunakan swift 3.

Seperti biasa, create new project, untuk case ini saya menggunakan Single View Application.

![Create New Project](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643513/jslkbnzykeigvpudgypc.png)

selanjutnya, isi detail application

![Naming Project](https://res.cloudinary.com/tendabiru/image/upload/q_auto/erypzmtdsd6b3dfya1mg.png)

sampai keluar tampilan standart xcode untuk project baru.

![Xcode Project](https://res.cloudinary.com/tendabiru/image/upload/q_auto/ciyiwumpofpq2hw6umn5.png)

lalu pada bagian sebelah menu general pilih project,

![Open Menu for Project](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643549/zqipioj7cxai4dqsnn6f.png)

atau pilih menu yang ada disebelahnya untuk menampilkan hierarchy-nya.

![Show project and targets list](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643556/mwer91d17mphd1athqdz.png)

kemudian pada bagian localization, klik tombol `+` untuk menambahkan file localization yanga baru. Untuk tulisan ini saya menambahkan German (de)

![Adding language](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643569/etzfdfxb3j5tzinjq0h8.png)

ulangi, untuk bahasa Jepang dan bahasa Indonesia.

![Xcode localization](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643581/zkfvotslhkyml9rdkwqh.png)

setelah itu, create new file, dengan type `String File`

![New file Xcode](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643589/lnpaqjhdm1kekjyriiap.png)
![String File](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643595/hzhkiqw9yzeqzlciw116.png)

berikan nama `Localizable` (wajib).

![Localizable File](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643603/bvwkojzterot7pa3x9dy.png)

lalu pada bagian utilities (pojok kanan xcode), cari kolom `localization` lalu klik `localize...`

![Xcode Utilities](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643613/gadberyeftdu7mjtt043.png)

pilih salah satu bahasa, klik localize.

![Confirm](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643715/ret90zzqlmmprcqq5n7o.png)

maka pada bagian utilities akan tampil seperti berikut.

![](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643759/pjckvvnmfhh0tp6nan7o.png)

pilih semua bahasa.

![Check All language](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643788/jwgrahagwyyczowxd1ro.png)

untuk bagian base bisa dihilangkan dengan meng-klik tick pada bagian base

![Moving Language Confirmation](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643800/mxk16d2rnh9oxcymxolc.png)

dan pindahkan ke English (karena base bahasa aplikasi ini bahasa inggris yang nantinya di translate ke bahasa lain).

lalu pilih `Use file` karena memang file ini sudah ada.

![Replace File](https://res.cloudinary.com/tendabiru/image/upload/q_auto/rcwoa7io2mqjogw21yhp.png)

# Langkah Kedua: - Menambahkan label dan terjemahannya.

Pada bagian ini, tambahkan `UILabel` pada bagian tengah storyboard yang isinya secara garis besar menanyakan kabar seseorang contoh "Hello, how are you doing, `Name`?". Kemudian diterjemahkan kedalam bahasa Indonesia, German, dan Jepang, yang mana `Name` adalah variable string yang bisa dirubah, dan nantinya ada beberapa kalimat yang perlu dicetak teba;

Oke, pertama tambahkan `UILabel` kedalam storyboard dengan drag and drop dari Object library.

![Add UILabel](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643827/gmfjwpg186t4tys4s5od.png)

lalu tambahkan outlet ke dalam file ViewController nya dengan cara tahan tombol Ctrl lalu tarik ke ViewController.swift dan beri nama variable nya.

![Add UILabel Outlet](https://res.cloudinary.com/tendabiru/image/upload/c_scale,q_20,w_1000/v1491643834/klzvorpcp3b59sbrxatu.png)

tambahkan constraint pada `UILabel` tersebut dan set `Lines` menjadi 0 sehingga perubahan string dapat dilakukan dengan dinamis.

![Add Constraint](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643836/nd2gasflfz6cj1ylrvhd.png)

untuk mempermudah, tambahkan extension untuk String seperti berikut.

```swift
extension String {

    func localized(_ comment: String = "") -> String {
        return NSLocalizedString(self, tableName: nil, bundle: Bundle.main, value: "", comment: comment)
    }

}
```

lalu tambahkan terjemahan untuk "Hello, how are you doing, `Name`?" pada masing-masing localizable file, dengan menggunakan kata pengganti `hello_name`

lalu, pada file ViewController.swift, di bagian `viewdidload()` sisipkan code berikut.

```swift
    lblHello.text = "hello_name".localized()
```

coba jalankan pada simulator atau device, apabila berjalan dengan benar maka akan muncul seperti berikut,

![First Preview](https://res.cloudinary.com/tendabiru/image/upload/c_scale,h_1349,q_20/v1491643851/v30jkgwpzirp2giafulm.png)

Hm, sepertinya sudah benar, sekarang rubah kode sebelumnya untuk dapat memasukan variable nama.

```swift
    lblHello.text = String(format: "hello_name".localized(), "Jane")
```
apabila berjalan dengan benar maka akan didapat tampilan sebagai berikut,
![Passing Variable](https://res.cloudinary.com/tendabiru/image/upload/c_scale,h_1500,q_20/v1491643859/go2be2qvithmpwhy5yjn.png)

Sekarang, bagaimana caranya apabila bagian nama ingin di **bold**?

Untuk merubah menjadi text bold, pada iOS diperlukan perubahan pada jenis font-nya. Maka diperlukan pengecekan, apakah font tersebut mendukung **bolt** atau tidak.

Untuk mengecek apakah font yang digunakan mendukung **bold** dapat dilakukan dengan menambahkan extension pada `UIFont` seperti berikut.

```swift
extension UIFont {
    var isSupportBolt:Bool {
        get {
            if self.fontDescriptor.withSymbolicTraits(.traitBold) != nil {
                return true
            }
            return false
        }
    }
}
```

Setelah pengecekan, barulah merubah font menggunakan font yang mendukung bolt. Untuk hal itu, tambahkan pada baris terakhir pada code di atas.

```swift
    func withTraits(traits:UIFontDescriptorSymbolicTraits...) -> UIFont {
        let descriptor = self.fontDescriptor
            .withSymbolicTraits(UIFontDescriptorSymbolicTraits(traits))
        return UIFont(descriptor: descriptor!, size: 0)
    }
    
    func bold() -> UIFont {
        return withTraits(traits: .traitBold)
    }

```

Setelah fungsi untuk menebalkan huruf selesai, sekarang waktunya membuat fungsi untuk membaca tag-tag tertentu yang akan kita gunakan pada file Localization.

Berikut adalah fungsinya,

```swift
    func convertTagFor(inputText text: NSMutableAttributedString, withAttribute attribute: Dictionary<String, AnyObject>, startWith start: String, endWith end: String) -> NSMutableAttributedString {
        
        while let str1 = text.string.range(of: start), let str2 = text.string.range(of: end) {
            let hStart = text.string.distance(from: text.string.startIndex, to: str1.lowerBound)
            let hEnd = text.string.distance(from: text.string.index(text.string.startIndex, offsetBy: hStart), to: str1.upperBound)
            let tStart = text.string.distance(from: text.string.startIndex, to: str2.lowerBound)
            let tEnd = text.string.distance(from: text.string.index(text.string.startIndex, offsetBy: tStart), to: str2.upperBound)
            let x = NSMakeRange(hStart + hEnd, tStart - hStart - hEnd)
            let t = NSMakeRange(tStart, tEnd)
            let h = NSMakeRange(hStart, hEnd)
            text.addAttributes(attribute, range: x)
            text.replaceCharacters(in: t, with: "")
            text.replaceCharacters(in: h, with: "")
        }

        return text
    }
```

Satu hal yang mengganggu pada fungsi di atas adalah perubahan dari native swift `Range` ke `NSRange`. Namun hal ini perlu dilakukan, karena perubahan font hanya dapat dilakukan via `NSAttributed​String` dan swift belum punya native type untuk `NSAttributed​String` 🙈.

Terakhir yang perlu diubah pada `viewDidLoad()` adalah

```swift
        lblHello.text = "hello_name".localized()
        if lblHello.font.isSupportBolt {
            let titleAttrs:Dictionary<String,AnyObject> = [NSFontAttributeName: lblHello.font.bold(),
                              NSForegroundColorAttributeName: UIColor.black]
            let g = convertTagFor(inputText: NSMutableAttributedString(string: String(format: "hello_name".localized(), "Jane")), withAttribute: titleAttrs, startWith: "<b>", endWith: "</b>")
            lblHello.attributedText = g
        }
```

Terakhir, tambahkan tag `<b>` dan `</b>` pada localizable file yang ingin ditebalkan.

Dan apabila semua berjalan sebagaimana mestinya, maka akan muncul tampilan seperti berikut, untuk bagian yang di bold pada masing-masing bahasa saya bedakan.

![bahasa Inggris](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643867/emjrpzxaano85m2rydiv.png)
![bahasa German](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643869/jyvnlnvekpeku8tfftmw.png)
![bahasa Jepang](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643876/abha6lcwohycj27atucq.png)
![bahasa Indonesia](https://res.cloudinary.com/tendabiru/image/upload/q_20/v1491643882/uwta904tb8z2csq2vmab.png)


# Penutup

Untuk menggunakan 2 tag yang berbeda sekaligus, maka diperlukan pemanggilan fungsi `convertTagFor(inputText text: NSMutableAttributedString, withAttribute attribute: Dictionary<String, AnyObject>, startWith start: String, endWith end: String)` 2 kali juga, dan berikutnya.

Untuk mengganti bold menjadi italic, atau attribute font lainnya, cukup dengan mengganti `UIFontDescriptorSymbolicTraits` saja pada extension `UIFont`.

Semua file dan source code dari postingan ini dapat dilihat di [Github](https://github.com/cizzu/LocalizationWithTag)

Demikian kiranya post kali ini.

Apabila ada yang ingin disampaikan, ditanyakan, atau ditawarkan, silahkan berkomentar pada kolom komentar di bawah.

nb. untuk translate saya menggunakan [translate.google.com](https://transalate.google.com)
