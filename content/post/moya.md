---
author: "Afriyandi Setiawan"
title: "Moya"
description: "Pengenalan, tutorial serta penggunaan moya"
categories:
- development
- tutorial
date: 2018-01-24T16:13:21Z
draft: false
slug: "moya"
tags:
- swift
- xcode
- iOS
thumbnailImage: https://source.unsplash.com/Z3ownETsdNQ/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/Z3ownETsdNQ/
---

Halo, lumayan lama juga tidak mengupdate web ini. Selain kesibukan sehari-hari dan persiapan nikah, juga kadang aga males nerusin tulisan-tulisan yang ada di draft (ada sekitar 10an draft yang ga diterusin, yang kadang dikarenakan males, ataupun sudah out of date).
<!--more-->

Untuk kali ini, akan coba membahas mengenai [Moya](https://github.com/Moya/Moya). Dan bagaimana biasanya saya menggunakan Moya pada project-project iOS. Secara singkat, moya bisa dijabarkan sebagai abstraksi untuk urusan koneksi ke server atau backend atau apapun yang *nyebrang-nyebrangin* data via internet ataupun intranet pada sebuah aplikasi iOS. Jadi pada prakteknya developer *tinggal* membuat sebuah abstraksi atau skeleton atau protocol yang merupakan turunan protocol tertentu dari salah satu protocol-nya Moya. Kedengerannya ribet? emang iya, tapi kalo udah biasa simple dan *ngebantu banget*.

<!-- toc -->

# Pra Moya

Dulu, ketika masih menggunakan objective-c sebagai bahasa utama dalam development selalu menggunakan [AFNetworking](https://github.com/AFNetworking/AFNetworking) untuk urusan koneksi, untuk mempermudah dibuat 'wrapper'-nya berupa pod yang mengelompokan task-task tertentu (misal post, get, upload, download, dll). Pun ketika beralih ke swift dan menggunakan [Alamofire](https://github.com/Alamofire/Alamofire) juga sama, wrapper yang pernah saya karang-karang sendiri sebelum kenal moya saya beri nama [pheConnector](https://github.com/aphe/pheConnector) dan bisa dilihat di github. Oh iya, untuk pheConnector yang di publish pada github juga menggunakan [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON) untuk mempermudah memproses JSON.

# Moya

Sekarang, setelah kenal Moya, project-project belakangan lebih banyak menggunakan Moya ketimbang pheConnector karangan saya sendiri. Karena memang Moya lebih rapih secara struktur, dan Moya juga sudah didukung dan diawasi serta diperbaiki oleh banyak manusia (Open source rules 🤘). Ya pada intinya apalah si aphe ini, cuma serpihan debu processor 😭

Untuk kali ini, saya ingin berbagi bagaimana saya menggunakan Moya pada project-project yang sedang saya kerjakan. Silahkan dikomentari apabila dianggap kurang tepat dan juga kurang bagus.

## Skenario

Anggaplah akan dibuat satu view controller untuk login sebuah aplikasi. [httpbin](http://httpbin.org/) akan digunakan sebagai backend-nya. Buat project baru, setup pod seperti yang pernah saya jabarkan di [post sebelumnya](https://www.cizzu.com/2017/05/28/membuat-framework-menggunakan-pod/) dengan requirement yaiut Moya dan SwiftyJSON.

Buat view sederhana yang berisi kolom username, kolom password dan button sign in seperti di bawah ini

![Tampilan](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/Tampilan.png)

Sebelum melakukan logic pada action yang akan dilakukan (login) Moya perlu setup tertentu. Menurut saya ini yang membuat developer yang tidak terbiasa dengan setup atau abstraksi koneksi beranggapan Moya ini "ribet". Sebetulnya tidak juga, dengan memanfaatkan teknik protocol yang digembor-gemborkan pada developer swift, membuat Moya sangat fleksibel, walaupun kadang memang saya tidak membuat satu abstraksi keseluruhan melainkan membaginya menjadi sesuai dengan logic dan action aplikasi yang dibuat.

Oke, daripada keder dengan bahasa dan istilah lebih enak liat kodenya kan.

Saya akan membuat 2 file swift yaitu `Connector.swift` dan `User.swift` diberi nama `User.swift` karena mengasumsikan untuk seluruh abstraksi koneksi untuk logic yang berkaitan dengan 'user' (login, logout, fetch user, dll) menggunakan logic yang ada pada file ini.

Untuk `Connector.swift` merupakan abstraksi global untuk abstraksi-abstraksi lainnya. Contoh, main url dan header, yang secara taktis tidak akan berubah banyak pada pengaplikasiannya. Untuk main url dan header akan saya tempatkan pada `info.plist` sehingga baris pertama dari `Connector.swift` menjadi seperti berikut

```swift
extension Bundle {
    
    var baseURL: URL {
        guard let info = self.infoDictionary,
            let urlString = info["Base URL"] as? String,
            let url = URL(string: urlString) else {
                fatalError("Cannot get base url from info.plist")
        }
        return url
    }
    
    var AppKey: String {
        guard let info = self.infoDictionary,
            let appKey = info["App Key"] as? String else {
                fatalError("Cannot get App Key from info.plist")
        }
        
        return appKey
    }
}

extension URL {
    static var baseURL: URL {
        return Bundle.main.baseURL
    }
}

func joinDictionary(from arrayDict: Array<Dictionary<String, Any>>) -> Dictionary<String, Any> {
    var some = Dictionary<String, Any>()
    for dict in arrayDict {
        some += dict
    }
    return some
}

func += <K, V> (left: inout [K : V], right: [K : V]) {
    for (k, v) in right {
        left.updateValue(v, forKey: k)
    }
}
```

`AppKey` akan digunakan sebagai header, biasanya *sih* untuk logging di server. function `joinDictionary` dan `+=` untuk menggabungkan beberapa header (apabila nantinya diperlukan header lebih dari satu)

Kemudian ganti `import Foundation` dengan `import Moya` dan buat satu protocol baru yang merupakan turunan dari protocol `TargetType` milik Moya. Dan buat extension dari protocol tersebut untuk mengisi `baseURL` dan `headers` secara default sehingga pada logic tidak diperlukan lagi menambahkan variable tersebut (asumsi baseURL dan headers tidak berubah-ubah untuk masing-masing logic). Kali ini nama protocol yang saya gunakan `CustomTarget`

```swift
import Moya

protocol CustomTarget: TargetType {
}

extension CustomTarget {
    var baseURL: URL {
        return Bundle.main.baseURL
    }
    
    var headers: [String : String]? {
        return joinDictionary(from: [
            ["App-key": Bundle.main.AppKey],
            ["User-Agent": "\(Bundle.main.infoDictionary?["CFBundleShortVersionString"] as! String) (app for \(UIDevice.current.model); iOS version \(UIDevice.current.systemVersion)) ~ phe"]
            ]) as? [String: String]
    }
}


```

Oke, untuk selanjutnya import `Moya` dan `SwiftyJSON` pada file `User.swift` Kemudian buat sebuah enum untuk logic-logic yang diperlukan pada Action user (untuk kali ini saya hanya mencontohkan login). Kenapa menggunakan enum dan bukan struct atau class atau variable biasa saja? nanti akan dijelaskan di bawah. Nama enum yang saya gunakan adalah `UserServices`

```swift
import Moya
import SwiftyJSON

enum UserServices {
    case login(username:String, password: String)
}
```

Kemudian, buat extension dari enum tersebut yang merupakan turunan dari `CustomTarget` maka xcode pun akan protes karena variable-variable yang diturunkan dari `TargetType` Moya tidak sepenuhnya diimplementasi pada extension tersebut, klik tanda bulatan merah dan xcode akan menambakan bedasarkan *stubs* yang telah dideklarasi pada protocol `TargetType`.

Rubah template tersebut sehingga menjadi sebagai berikut.

```swift
extension UserServices: CustomTarget {
    var path: String {
        switch self {
        case .login:
            return "post"
        }
    }
    
    var method: Moya.Method {
        switch self {
        case .login:
            return .post
        }
    }
    
    var sampleData: Data {
        return Data()
    }
    
    var task: Task {
        switch self {
        case .login(let username, let password):
            return .requestParameters(parameters: ["username": username, "password": password], encoding: URLEncoding.default)
        }
    }
}
```

Pada bagian

```swift
    var path: String {
        switch self {
        case .login:
            return "post"
        }
    }
```

Merupakan URL akhir dari API atau alamat yang dituju. Karena untuk backend yang digunakan adalah [httpbin](http://httpbin.org) maka untuk method post URL yang dituju adalah `post`, kemudian `var method` method untuk logic tersebut (post, get, put, dll) `var sampleData` biasanya digunakan untuk unit testing, tapi untuk unit testing Moya akan saya jabarkan pada post berikutnya. Dan `var task` merupakan tempat pembuatan data atau parameter apa saja yang akan dikirim ke server.

Dari baris script di atas, dapat dilihat dengan menggunakan enum, apabila ada penambahan logic pada User, maka tinggal ditambahkan pada enum tersebut, sebagai contoh apabila User perlu aktifasi maka pada `UserService` cukup ditambahkan case baru yaitu `activation(username: String)` seperti berikut

```swift
enum UserServices {
    case login(username:String, password: String)
    case activation(username: String)
}
```

Dan karena secara sengaja saya tidak menambahakan default pada masing-masing switch-case maka secara otomatis xcode akan protes karena tidak semua case dijabarkan pada switch tersebut.

Kemudian, bagaimana caranya men-triger Moya untuk memanggil Alamofire dan *nembak* API atau URL yang ingin kita tuju? Moya menyediakan satu class untuk hal tersebut, yaitu `MoyaProvider`, tapi sebelum ke sana buat sebuah struct pada `User.swift` sehingga ketika perlu memanggil logic hanya perlu memanggil struct tersebut,

```swift
struct userDo {
    static let provider = MoyaProvider<UserServices>()
    
    static func request(
        target: UserServices,
        success successCallback: @escaping (JSON) -> Void,
        error errorCallback: @escaping (_ statusCode: Error) -> Void,
        failure failureCallback: @escaping (MoyaError) -> Void,
        finish isFinish: @escaping () -> Void
        ) {
        provider.request(target) { (_response) in
            defer {
                isFinish()
            }
            switch _response {
            case .success(let result):
                do {
                    let _ = try result.filterSuccessfulStatusCodes()
                    let json = try JSON(result.mapJSON())
                    successCallback(json)
                } catch let err {
                    errorCallback(err)
                }
            case .failure(let err):
                failureCallback(err)
            }
        }
    }
}
```

Karena untuk koneksi biasanya tidak diperlukan feedback apakah fungsi tersebut telah selesai dieksekusi maka digunakan system callback yang menjalakan perintah asynchronous.

Dengan sedikit tweak fungsi di atas bisa dibuat lebih menarik dan reusable seperti berikut.

Pada file `Connector.swift` tambahkan protocol seperti berikut

```swift
protocol Request {
    associatedtype T
    associatedtype callBackType
    func request(target: T,
                 success successCallback: @escaping (callBackType) -> Void,
                 error errorCallback: @escaping (_ statusCode: Error) -> Void,
                 failure failureCallback: @escaping (MoyaError) -> Void,
                 finish isFinish: @escaping () -> Void)
}
```

lalu pada `User.swift` dirubah menjadi sebagai berikut

```swift
struct userDo: Request {
    typealias T = UserServices
    typealias callBackType = JSON
    
    let provider = MoyaProvider<UserServices>()
    
    func request(target: UserServices,
                 success successCallback: @escaping (JSON) -> Void,
                 error errorCallback: @escaping (Error) -> Void,
                 failure failureCallback: @escaping (MoyaError) -> Void, finish isFinish: @escaping () -> Void) {
        self.provider.request(target) { (_response) in
            defer {
                isFinish()
            }
            switch _response {
            case .success(let result):
                do {
                    let _ = try result.filterSuccessfulStatusCodes()
                    let json = try JSON(result.mapJSON())
                    successCallback(json)
                } catch let err {
                    errorCallback(err)
                }
            case .failure(let err):
                failureCallback(err)
            }
        }
    }
}
```

Hal ini akan mempermudah pembacaan dan pengelompokan tiap abstraksi untuk masing-masing abstraksi apabila bertambah.

Oke, kembali ke file `ViewController.swift` yang sebelumnya telah dihubungkan masing-masing untuk UITextfield dan button actionnya. Untuk hal ini saya menggunakan nama fungsi `doLogin` untuk button action dan uname serta pswd untuk uitexfield username dan password, sehingga dapat dijabarkan sebagai berikut.

```swift
    @IBAction func doLogin(_ sender: UIButton) {
        let (username, password) = (uname.text, pswd.text)
        userDo().request(target: .login(username: username ?? "empty", password: password ?? "empty"),
                         success: { (val) in
                            print(val) },
                         error: { (err) in
                            print(err) },
                         failure: { (err) in
                            print(err)
        }) {}
    }
```

Saya tidak melakukan pengecekan pada textfield. Apabila ingin melakukan textfield secara otomatis bisa membacanya kembali di [postingan sebelumnya](https://www.cizzu.com/2017/05/21/ios-login-form-dengan-validasi/), oleh karena itu apabila textfield nil maka saya unwarp dengan string `empty` (sebenernya bisa dengan force unwarp dengan menggunakan `!` tapi saya pribadi tidak begitu suka). 

Oke, terakhir adalah dengan menambahakan property list `Base URL` dan `App Key` pada file `Info.plist`

![info.plist](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/info.plist.png)

Maka apabila pada username saya isi `sakura` dan password saya isi `minamoto` maka balikan dari httpbin yang di print out pada console seperti berikut,

```json
{
	"files": {

	},
	"origin": "*IP Mu*",
	"data": "",
	"headers": {
		"Content-Type": "application\/x-www-form-urlencoded; charset=utf-8",
		"Connection": "close",
		"Host": "httpbin.org",
		"App-Key": "cizzu",
		"Accept": "*\/*",
		"Content-Length": "33",
		"User-Agent": "1.0 (app for iPhone; iOS version 11.2.2) ~ phe",
		"Accept-Language": "en-US;q=1.0, id-US;q=0.9, de-US;q=0.8, ja-JP;q=0.7",
		"Accept-Encoding": "gzip;q=1.0, compress;q=0.5"
	},
	"json": null,
	"form": {
		"username": "sakura",
		"password": "minamoto"
	},
	"args": {

	},
	"url": "https:\/\/httpbin.org\/post"
}
```

Bisa dilihat diatas data yang di-post dalam bentuk form dan bukan json, untuk mengganti supaya Moya mengirim dalam format JSON maka tinggal rubah `var task` pada `UserServices` bagian `encoding` menjadi `JSONEncoding.default`.

```swift
    var task: Task {
        switch self {
        case .login(let username, let password):
            return .requestParameters(parameters: ["username": username, "password": password], encoding: JSONEncoding.default)
        }
    }
```

Yap, demikian kiranya post kali ini, pada post berikutnya saya akan coba menjabarkan unit test pada Moya. Semoga bermanfaat.

Source untuk post ini dapat dilihat di github cizzu pada [link berikut ini](https://github.com/cizzu/Moya-Tutorial)

Terima kasih.