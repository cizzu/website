---
categories:
- development
- tutorial
date: 2017-05-20T19:36:00Z
description: "Tutorial bagaimana membuat login form yang dapat memvalidasi username dan password pada aplikasi iOS dengan menggunakan swift disertai dengan source code"
draft: false
slug: "ios-login-form-dengan-validasi"
tags:
- swift
- iOS
title: "iOS Login Form Dengan Validasi"
thumbnailImage: https://source.unsplash.com/ow9uDbA5x8E/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/ow9uDbA5x8E/
---

Pada website-website, umumnya sudah familiar ketika user mengisi form tertentu kemudian menekan tombol enter/tab form field akan pindah ke form field berikutnya. Dan apabila semua form sudah diisi maka ketika menekan enter langsung submit. Namun, pada mobile, hal ini sedikit berbeda. Diperlukan handling tertentu untuk melakukan hal tersebut.
<!--more-->

Untuk kali ini saya akan berbagi bagaimana caranya membuat form yang apabila menekan next pada keyboard akan ke field berikutnya kemudian apabila form telah terisi semua yang telah melalui proses validasi dan dinyatakan benar maka user langsung bisa submit, namun apabila ada field yang kosong atau tidak valid, maka focus akan ke field tersebut. Dan hasil akhirnya dapat dilihat di [repositori berikut](https://github.com/cizzu/ResponsiveTextField).

Seperti biasa, buat project baru, isi detailnya kemudian pilih MainStoryboard.

![MainStoryboard](https://res.cloudinary.com/tendabiru/image/upload/c_scale,q_auto:good,w_800/v1495303309/tn6sbvm8qbaxrzk8oksb.png)

Kemudian masukan 2 buah textfield yang disertai dengan label dan sebuah button dengan cara drag dari Object Library yang berada di pojok kanan bawah.

![Create View](https://res.cloudinary.com/tendabiru/image/upload/c_scale,q_auto:good,w_800/v1495303475/efcf1vdnqypykaldnfru.png)

Untuk mempermudah dan mempercepat, saya menggunakan bantuan stackview dan menempatkan selalu pada tengah layar pada berbagai ukuran device.

![Stack View](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/klstt2ukhzvnxjvtlogx.png)

Coba jalankan di simulator.
<div style="text-align:center">
![Simulator](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.4/kpnouqsqbw0n9k4m5ttb.png)
</div>

Coba tap atau pilih uitextfield. (Apabila simulator tidak mengeluarkan keybaord, maka tekan kombinasi ⌘+Shift+K). Ketikan sesuatu dan tekan Return, maka tidak akan terjadi apa-apa, karena memang belum di handle 😅.

Oke, balik lagi ke MainStoryboard, pilih uitextfield yang pertama. Pada bagian Attribute Inspector, khususnya pada bagian `Return key` rubah menjadi Next pada kolom dropdown disebelahnya. Lakukan hal yang sama pada textfield satunya namun pilih Done bukan Next, sehingga proses transisi menjadi lebih natural.

Dan karena pada contoh ini mengisyaratkan kalau form ini form login, maka pada textfield kedua, yang diasumsikan sebagai password, maka centang bagian Secure Text Entry. Lebih lanjut bisa merubah property-property lainnya.

Coba jalankan di simulator,
<div style="text-align:center">
![Login](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.4/xy0gdk69i7xo6smz8xm5.png)
![Password](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.4/terzmt1qzlwhgeexoazz.png)
</div>

Perbedaan terlihat pada `return` keyboard yang berubah sesuai dengan alur validasi.

Setelah selesai dengan tampilan, maka selanjutnya adalah membuat custom class untuk UITextField (nama class yang digunakan pada post kali ini adalah `ResponsiveTextField`).

Buat dua buah `@IBOutlet` masing-masing untuk text field sesudahnya dan text field sebelumnya untuk kembali apabila belum terisi/tipe data salah.

```swift
@IBOutlet public weak var nextResponderField: UIResponder?
@IBOutlet public weak var prevResponderField: UITextField?
```

Kemudian override init untuk UITextfield, dan tambahkan satu function setup untuk melakukan memanggil fungsi validasi.

```swift
//load dari storyboard
public required init(coder aDecoder: NSCoder) {
    super.init(coder: aDecoder)!
    setUp()
}

//load dari code
override public init(frame: CGRect) {
    super.init(frame: frame)
    setUp()
}

func setUp() {
    addTarget(self, action: #selector(validate(textField:)), for: .editingDidEndOnExit)
}
```

Selanjutnya, buat fungsi validasinya. Untuk kasus kali ini adalah memvalidasi apakah text field tersebut kosong dan apakah format email yang dimasukan benar. Kemudian cek kembali, ketika user menekan next/done apakah respondernya berikutnya text field atau button, apabila button panggil function button tersebut (submit).

```swift
func validate(textField: UITextField) {
    
    if prevResponderField != nil {
        if (prevResponderField?.text?.isEmpty)!{
            prevResponderField?.becomeFirstResponder()
            return
        }
    }
    
    if (textField.text?.isEmpty)! {
        return
    }
    
    //tambahkan jenis textfield lain bedasarkan jenis keyboarnya pada case-case berikutnya
    switch textField.keyboardType {
    case .emailAddress:
        if !valid(email: textField.text!) {
            return
        }
    default:
        break
    }
    
    //cek apakah responder berikutnya button atau bukan
    switch nextResponderField {
    case let button as UIButton:
        if button.isEnabled {
            button.sendActions(for: .touchUpInside)
        } else {
            resignFirstResponder()
        }
    case .some(let responder):
        responder.becomeFirstResponder()
    default:
        resignFirstResponder()
    }
}

//fungsi-fungsi validasi
func valid(email: String) ->Bool{
    let _emailRegEx = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,6}"
    let _emailTest = NSPredicate(format:"SELF MATCHES %@", _emailRegEx)
    
    return _emailTest.evaluate(with: email)
}

```

Pada class diatas karena validasi dilakukan pada class tersebut maka apabila tidak valid perlu diberikan callback ke class utama(root)nya. Hal ini dapat dilakukan dengan mendelegate class `ResponsiveTextField` ataupun menurunkan class `UIAlertController` itu sendiri untuk mendeteksi root/class utamanya. Pada kasus ini, saya menurunkan class `UIAlertController` dengan menambahkan fungsi-fungsi berikut untuk mengecek  root/class utamanya kemudian menampilakan alert.

```swift
extension UIAlertController {
    
    func show() {
        present(animated: true, completion: nil)
    }
    
    func present(animated: Bool, completion: (() -> Void)?) {
        if let rootVC = UIApplication.shared.keyWindow?.rootViewController {
            presentFromController(controller: rootVC, animated: animated, completion: completion)
        }
    }
    
    private func presentFromController(controller: UIViewController, animated: Bool, completion: (() -> Void)?) {
        if let presented = controller.presentedViewController {
            presented.present(self, animated: animated, completion: completion)
            return
        }
        switch controller {
        case let navVC as UINavigationController:
            presentFromController(controller: navVC.visibleViewController!, animated: animated, completion: completion)
            break
        case let tabVC as UITabBarController:
            presentFromController(controller: tabVC.selectedViewController!, animated: animated, completion: completion)
        default:
            controller.present(self, animated: animated, completion: completion)
        }
    }
}
```

Oke, sekarang untuk menampilkan message sudah di handle. Lalu, ada fungsi lagi untuk mentranslate atau me-localize pesan, simple saja buat satu turunan variable dari `String` dan  kembalikan translation string dari bundle main app. Hal itu dapat dilakukan dengan fungsi berikut

```swift
extension String {
    var localize:String {
        guard let mainBundle = Bundle(identifier: Bundle.main.bundleIdentifier!) else {
            return self
        }
        return NSLocalizedString(self, tableName: nil, bundle: mainBundle, value: "", comment: "")
    }
}
```

Setelah semua telah fungsi tambahan telah dimasukan maka fungsi untuk validasi dirubah menjadi seperti berikut,

```swift
func validate(textField: UITextField) {
    
    if prevResponderField != nil {
        if (prevResponderField?.text?.isEmpty)!{
            prevResponderField?.becomeFirstResponder()
            return
        }
    }
    
    if (textField.text?.isEmpty)! {
        let _alert:UIAlertController = UIAlertController(title: "FAILED".localize, message: "CANNOT_EMPTY".localize, preferredStyle: .alert)
        _alert.addAction(UIAlertAction(title: "DONE".localize, style: .cancel,
                                       handler: { (action) -> Void in
        }))
        _alert.show()
        textField.becomeFirstResponder()
        return
    }
    
    //tambahkan jenis textfield lain bedasarkan jenis keyboarnya pada case-case berikutnya
    switch textField.keyboardType {
    case .emailAddress:
        if !valid(email: textField.text!) {
            let _alert:UIAlertController = UIAlertController(title: "FAILED".localize, message: "INVALID_EMAIL_FORMAT".localize, preferredStyle: .alert)
            _alert.addAction(UIAlertAction(title: "DONE".localize, style: .cancel,
                                           handler: { (action) -> Void in
            }))
            _alert.show()
            textField.becomeFirstResponder()
            return
        }
    default:
        break
    }
    
    //cek apakah responder berikutnya button atau bukan
    switch nextResponderField {
    case let button as UIButton:
        if button.isEnabled {
            button.sendActions(for: .touchUpInside)
        } else {
            resignFirstResponder()
        }
    case .some(let responder):
        responder.becomeFirstResponder()
    default:
        resignFirstResponder()
    }
}

//fungsi-fungsi validasi
func valid(email: String) ->Bool{
    let _emailRegEx = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,6}"
    let _emailTest = NSPredicate(format:"SELF MATCHES %@", _emailRegEx)
    
    return _emailTest.evaluate(with: email)
}
```

Jangan lupa tambahkan file localizationnya, untuk menambahkan file localization bisa baca di [post sebelumnya.](https://www.tendabiru.net/2017/04/02/ios-localization-menggunakan-tag-tertentu/)

## <a name="storyboard">Hubungkan dengan storyboard</a>

Dari sisi scripting sudah semua. Sekarang bagaimana menggunakan `ResponsiveTextField` class ini?

Kembali lagi ke MainStoryboard, rubah class `UITextField` menjadi `ResponsiveTextField` pada **Identity Inspector**.
<div style="text-align:center">
![Custom class](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/xpwehxgg9zlthutab0qp.png)
</div>
Lalu pada **Connection Inspector** hubungkan `nextResponderField` pada field username ke field password.
Kemudian pada field password hubungkan `nextResponderField` ke button login, dan `prevResponderField` ke field username seperti gambar dibawah berikut.

![Username nextResponderField](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/n8zcfk0q0leicbxawhlf.png)
![Passowrd prevResponderField](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/swuok3tymk8srem09kc4.png)
![Password nextResponderField](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/zdi0blwzvoxgeqf79fb8.png)

Lalu tambahkan action untuk button Login

![Connect Action](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/d7xotijzdmphmuv6xioy.png)
![Button Action](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good/sbbnhqzvjgbhfauuqikq.png)

Untuk action saya hanya akan print ke console seperti berikut

```swift
@IBAction func doLogin(_ sender: UIButton) {
    print("Action Login Triggered")
}
```

Jalankan pada simulator, apabila semua telah sesuai maka akan didapat tampilan sebagai berikut.
<div style="text-align:center">
![GIF Hasilnya](https://res.cloudinary.com/tendabiru/image/upload/v1495308513/h5b5qedctrzw1wkk7cvj.gif)
</div>
Yey!!! semua berjalan sebagaimana mestinya.

Untuk kali ini sekian dari saya. Apabila ada yang ingin ditanyakan, diprotes, ataupun menambahkan silahkan berkomentar pada kolom disqus di bawah.