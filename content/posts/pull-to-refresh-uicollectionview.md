---
date: 2016-04-17T20:34:00Z
description: "aplikasi yang dapat menambah satu jumlah cell setiap refresh dengan cara ditarik ke bawah atau gesture pull. "
draft: false
slug: "pull-to-refresh-uicollectionview"
categories:
- Notes
tags: [development, swift]
keywords: [swift, iOS, pull to refresh, tutorial, apple, xcode]
title: "Pull To Refresh UICollectionView"
thumbnailImage: https://source.unsplash.com/gBnHMsAOWrs/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/gBnHMsAOWrs/
---


Setelah lumayan lama tidak mengupdate blog, pada kesempatan ini saya akan berbagi mengenai bagaimana membuat aplikasi iOS yang dapat merefresh isinya dengan gesture pull atau tarik ke bawah.
<!--more-->

# Mukadimah

Masih seperti bahasan-bahasan sebelumnya, aplikasi ini masih menggunakan swift sebagai bahasa pemrogramannya. Nantinya aplikasi masih akan digunakan pada postingan-postingan berikutnya dengan materi pembahasan yang berbeda.

Komponen-komponen yang diperlukan untuk membuat aplikasi ini adalah,

- Sesuai dengan judul, `UICollectionView` beserta turunan-turunannya.
- `UILabel`.
- Dan yang terakhir adalah, `UIRefreshControl`.

Versi xcode yang saya gunakan adalah versi 7.3.

Tujuan akhir dari post ini adalah membuat aplikasi yang dapat menambah satu jumlah cell setiap refresh dengan cara ditarik ke bawah atau gesture pull. Source dan project nantinya bisa didownload [disini](https://github.com/aphe/pullToRefresh/archive/master.zip). Tapi untuk penjelasan lebih mendalam silahkan baca terlebih dahulu.

# Langkah Awal: – Mempersiapkan storyboard

Buat project baru pada xcode, pilih Single View Application, beri nama sesuai dengan keinginan (untuk bahasan kali ini saya memberi nama projectnya pullToRefresh), kemudian simpan.

Maka pada layar project akan didapatkan file ViewController.swift, AppDelegate.swift, Main.storyboard, dan file-file pendukung lainnya.

![Main.storyboard](https://res.cloudinary.com/tendabiru/image/upload/v1491644923/rvdd5difqb3qu4mbzqbd.png)

Buka file Main.storyboard kemudian masukan collection view dengan cara pilih object library yang berada di pojok kanan bawah lalu cari collection view, kemudian  klik dan tarik ke view yang ada di View Controller Scene.

![collectionview](https://res.cloudinary.com/tendabiru/image/upload/v1491644936/zh0xjjeum2depnzwkbrl.png)

Langkah berikutnya buka file ViewController.swift, kemudian tambahkan line berikut.

```swift
@IBOutlet weak var theCollection: UICollectionView!
```

Baris diatas menambahkan outlet reference untuk collectionview yang ada di storyboard, untuk kemudian bisa digunakan ketika aplikasi berjalan (runtime).

Buka kembali Main.storyboard. Pilih View, kemudian pilih Collection View. Pada bagian connection inspector di pojok kanan atas, klik kemudian tarik tanda + yang ada dibagian datasource ke View Controller yang berada dibawah View Controller Scene.

![pullReference](https://res.cloudinary.com/tendabiru/image/upload/v1491644965/p7nspbf5okzxsyghhrlt.png)

Lakukan hal yang sama untuk delegate dan New Referencing Outlet, bedanya ketika pada New Referencing Outlet akan muncul pop-up yang berisi theCollection yang merupakan outlet reference yang sebelumnya dinotasikan pada ViewController.swift

Agar collection yang ada diaplikasi dapat tampil dengan baik dan benar pada device, maka diperlukan settingan constraint pada collectionview. Untuk melakukannya cukup dengan menahan tombol ctrl kemudian klik dan tarik ke view yang ada di viewcontroller. Kemudian pilih Center Horizontally in Container, ulangi dan pilih Center Vertically in Container, Equal Widths, dan Equal Heights.

![equal](https://res.cloudinary.com/tendabiru/image/upload/v1491644977/mlbjb7i6kdrpui8tt874.png)

Pilih icon yang menyerupai segitiga pada kanan bawah, kemudian pilih update frame.

Masih pada Main.storyboard, pilih Collection view kemudian pada bagian size inspector (sebelah connection inspectore), sesuaikan ukuran cell menjadi 100×100.

![size inspector](https://res.cloudinary.com/tendabiru/image/upload/v1491644988/wjwuafixxtje39ce40py.png)

Hal terakhir yang dilakukan sebelum ke langkah berikutnya adalah memberikan cell identifier pada collection view.

Pilih Collection View Cell, kemudian pada kolom identifier yang berada di attribute inspector isi dengan cell (atau sesuaikan dengan yang diinginkan, tapi ingat hal ini berhubungan dengan langkah berikutnya).

![cell identifier](https://res.cloudinary.com/tendabiru/image/upload/v1491644997/ebnvsmzubodb1pxopxct.png)

Tambahkan label ke dalam cell yang berada, pada collection view dengan cara menarik label dari object library ke dalam cell. ctrl + klik dan drag dari label ke bagian cell, lalu pilih Center Horizontally in Container,  dan Center Vertically in Container.

# Langkah Kedua: – Menyisipkan Kode

Setelah melakukan semua langkah pada langkah sebelumnya, langkah berikutnya adalah menyisipkan kode. Saya memerlukan 2 file swift baru yang akan saya beri nama ViewControllerEXT.swift dan CellCollection.swift yang masing-masing memiliki nama class yang sama dengan nama filenya.

File ViewControllerEXT.swift berisi turunan (extension) dari class ViewController. Sementara class CellCollection merupakan custom class yang akan me-overide class bawaan, yaitu UICollectionViewCell.

File ViewControllerEXT.swift juga merupakan tempat deklarasi untuk delegate dan datasource protocol dari UICollectionView yang berada dalam ViewController. Apa itu delegate protocol dan datasource protocol? UICollectionView mempunyai beberapa protocol yang dapat digunakan oleh class lain, tanpa harus merupakan keturunan (inheritance) langsung dari UICollectionView. Jadi class ViewController akan menurunkan protocol UICollectionViewDataSource dan protocol UICOllectionViewDelegate.

Sebenarnya tanpa file yang terpisah pun, pada ViewController juga dapat dideklarasikan delegate dan datasource, namun saya lebih menyukai untuk masing-masing file mempunyai fungsinya sendiri-sendiri, walaupun masih dalam satu koridor class yang sama. Hal ini guna memudahkan ketika me-trace atau mencari kesalahan ketika eksekusi program, ataupun penambahan fitur.

Pilih file ViewControllerEXT.swift, apabila belum me-import UIKit, tambahkan import UIKit pada bagian awal. Untuk mendeklarasikan delegate dan datasource protocol dari UICollectionView sisipkan baris berikut

```swift
import UIKit 
extension ViewController: UICollectionViewDataSource, UICollectionViewDelegate {
	
}
```

Perhatikan, xcode akan memberitahu bahwa ada error `does not conform protocol UICOllectionViewDataSource` mengapa? karena pada UICollectionViewDataSource mewajibkan 2 function yang harus ada pada turunannya yaitu

```swift
public func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int
```
dan
```swift
public func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell
```
Sebelum menyisipkan 2 function tersebut pada ViewControllerEXT.swift, beralih ke file CellCollection.swift, ada hal yang perlu kita lakukan dahulu. Seperti diungkapkan sebelumnya, CellCollection.swift berisi class CellCollection yang merupakan turunan dari UICollectionViewCell.

Sama seperti protocol, untuk mendeklarasikan turunan class juga menggunakan syntax yang sama.

```swift
import UIKit
class CollectionCell: UICollectionViewCell {
	
}
```

Tambahkan outlet reference label yang ada pada cell, sehingga nanti ketika runtime label tersebut dapat dirubah-ubah.

```swift
import UIKit

class CollectionCell: UICollectionViewCell {
    @IBOutlet weak var cellNumber: UILabel! 
}
```

buka kembali Main.storyboard, pilih cell. Pada bagian identity inspector, pada kolom custom class isikan dengan custom class yang sebelumnya kita buat, yaitu CellCollection, tekan enter.

![custom class]()

Pada Connection Inspectore tarik outlet ke CollectionCell dan pilih cellNumber.

Kembali lagi ke file ViewControllerEXT.swift, sisipkan 2 function yang diperlukan oleh UICollectionViewDataSource seperti berikut,

```swift
import UIKit
extension ViewController: UICollectionViewDataSource, UICollectionViewDelegate {

    func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 1 
    }
    
    func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
        let collCell:CollectionCell = collectionView.dequeueReusableCellWithReuseIdentifier("cell", forIndexPath: indexPath) as! CollectionCell
        collCell.cellNumber.text = "Cell #\(String(indexPath.item + 1))"
        return collCell
    }
}
```
Coba untuk me-build (Cmd + B), apabila langkah-langkah di atas dilakukan dengan benar, maka tidak akan terdapat error atau warning apapun.

pada class ViewController, tambahkan satu variable cellCount dengan type Int, yang digunakan sebagai jumlah cell yang ada, dengan nilai awal 1. Jadi, nantinya apabila di refresh, jumlah cell akan bertambah satu. Selain itu juga perlu instance dari class UIRefreshControl yang diberi nama variable refreshControl.

```swift
import UIKit
class ViewController: UIViewController {
    
    @IBOutlet weak var theCollection: UICollectionView!
    let refreshController = UIRefreshControl()
    var cellCount:Int = 1

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib. 
     }

     override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

Perubahan juga perlu dilakukan pada extension ViewController, khususnya pada bagian `func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int`, yang masih di hardcode angka 1 untuk selanjutnya diganti dengan variable cellCount.

coba untuk run, apabila collection tidak bisa di gesture pull atau ditarik-tarik maka buka Main.storyboard pilih Collection VIew, pada bagian attributes inspector pilih Bounce Vertically. Karena apabila option ini tidak dipilih, jika jumlah cell hanya sedikit dan ada ada cell paling atau atau paling bawah, maka tidak bisa di scroll atau pull.

![Bounce Vertically](https://res.cloudinary.com/tendabiru/image/upload/v1491645020/yx4gcbxuranpulfrvi8b.png)

coba jalankan kembali, maka dengan satu cell pun pull bisa dilakukan.

# Langkah Terakhir: – Memasukan Refresh Control

Pada class ViewController bagian viewdidload sisipkan code berikut,
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    refreshController.tintColor = UIColor.lightGrayColor()
    let color = UIColor.lightGrayColor()
    let attr = [NSForegroundColorAttributeName:color]
    let title = NSAttributedString(string: "Add New Cell", attributes: attr)
    refreshController.attributedTitle = title
    theCollection.addSubview(refreshController)
}
```
Coba kembali jalankan dengan perintah cmd+R.

Loh, setelah dicoba kok ga selesai2 muter-muternya?

Iya, pada UIRefreshControl ada 2 function utama, yaitu `beginRefreshing()` dan `endRefreshing()`. Sesuai dengan namanya beginRefreshing menandakan bahwa UIRefreshController memulai untuk refresh dan endRefreshing menandakan bahwa proses telah selesai semua.

Untuk itu, diperlukan sebuah function yang dijalankan ketika refresh dimulai. Function itu saya beri nama refresh() dan disisipkan dalam class ViewController

```swift
func refresh() {
    refreshController.beginRefreshing()
    cellCount += 1
    theCollection.reloadData()
    refreshController.endRefreshing()
}
```
Dan untuk memanggilnya ketika proses refresh dimulai, sisipkan baris berikut sebelum `theCollection.addSubview(refreshController)`
```swift
refreshController.addTarget(self, action: #selector(ViewController.refresh), forControlEvents: .ValueChanged)
```
Coba jalankan kembali, apabila benar maka setiap kali ditarik kebawah, maka jumlah cell akan bertambah satu.

Oke, mungkin itu yang bisa saya share kali ini, pada kesempatan berikutnya saya akan mencoba mengembangkan kembali aplikasi ini.

Untuk seluruh source dari project bisa dilihat di [github](https://github.com/cizzu/pullToRefresh)
