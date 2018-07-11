---
title: "Membuat Framework  Menggunakan Cocoapod"
slug: "membuat-framework-menggunakan-pod"
categories:
- development
- tutorial
date: 2017-05-27T22:00:48Z
description: "Membuat framework dengan cocoapod yang merupakan dependency manager untuk cocoa yang digunakan pada pembuatan aplikasi iOS."
draft: false
tags:
- swift
- iOS
thumbnailImage: https://source.unsplash.com/b18TRXc8UPQ/
thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/b18TRXc8UPQ/
---
Sebelum membuat project menggunakan cocoapod, sebelumnya perlu diketahui dahulu apa itu cocoapod?

> CocoaPods is a dependency manager for Swift and Objective-C Cocoa projects. It has over 32 thousand libraries and is used in over 2.1 million apps. CocoaPods can help you scale your projects elegantly.

Dari penjabaran yang diambil dari situs resminya, kalau diartikan berarti  cocoapod adalah dependency manager untuk project yang menggunakan framework cocoa milik apple.
<!--more-->
Apa itu dependency manager? mungkin yang pernah develop menggunakan php atau nodejs sudah familiar dengan composer ataupun npm, cocoapod berfungsi sama seperti 2 dependency manager di atas, yaitu mengatur, menambahkan (install/download, update), mensetting secara default framework/library/apapun yang dibutuhkan dalam project tersebut untuk kemudian menyimpannya ke dalam suatu file atau project tersendiri. Sehingga project/framework tersebut bisa menjadi modul tersendiri, yang apabila ingin dipergunakan lagi di project lain, hanya perlu 'menempelkan' modul tersebut.

Hal ini juga mengurangi kompleksitas ketika menggunakan banyak plug-in/modul. Karena suatu project dikerjakan secara team, dari file konfigurasi bisa diketahui project tersebut menggunakan plug-in/modul apa saja, serta instalasi bisa dilakukan secara seragam. 

# Install Cocoapods

Hal pertama yang perlu dilakukan adalah menginstall cocoapod. Perlu diketahui, cocoapods sendiri menggunakan [ruby](https://www.ruby-lang.org/en/), maka untuk instalasinya menggunakan gem.  Hal ini dapat dilakukan via terminal dengan menjalankan perintah berikut.

```language-bash
sudo gem install cocoapods
```

Dengan perintah di atas, cocoapod terinstal dalam system atau user admin, untuk yang tidak ingin cocoapod terinstall di system (diinstall menggunakan sudo) dan terinstall sebagai local seperti penulis, maka perlu menambahkan konfigurasi berikut pada `.bashrc` (atau `.zshrc` apabila menggunakan zsh) pada directory home user (~) sehingga semua perintah `gem install <package>` dapat diinstall sebagai local user dan bukan admin.


```language-bash
#gem-local
export GEM_HOME=$HOME/.gem
export PATH=$GEM_HOME/bin:$PATH
```

Setelah disave, kemudian jalankan perintah `source .bashrc` (atau `source .zshrc` bagi yang menggunakan zsh) untuk me-load settingan yang telah disimpan sebelumnya. kemudian jalankan perintah install cocoapod tanpa sudo.

```language-bash
gem install cocoapods
```
Maka cocoapod akan terinstal pada directory `~/.gem/` atau direktori lokal.

# Membuat Framework

Agar teratur, pindah ke directory dimana project akan dibuat dengan perintah `cd`, pada contoh kali ini penulis akan menggunakan directory `~/project/`. Perlu diketahui folder/direktori project pada home user perlu dibuat terlebih dahulu, hal ini dapat dilakukan dengan perintah `mkdir`.

```language-bash
mkdir ~/project
cd ~/project
```

Untuk tulisan kali ini, penulis akan membuat pod dengan mengambil salah satu class dari postingan sebelumnya, [form login yang disertai dengan validasi](https://www.cizzu.com/2017/05/21/ios-login-form-dengan-validasi/) yaitu class `ResponsiveTextField` yang kemudian dibuat menjadi modul (pod) tersendiri dengan nama `formLogin`.

Lakukan perintah berikut pada terminal,

```language-bash
pod lib create "formLogin"
```

Akan muncul jendela interaktif pada terminal, 

```language-bash
What language do you want to use?? [ Swift / ObjC ]
 > swift
```

Tentukan bahasa yang ingin digunakan, apakah swift/objc.

```bash
Would you like to include a demo application with your library? [ Yes / No ]
 > yes
```

Opsi ini untuk menentukan apakah kita perlu membuat aplikasi demo tersendiri untuk library/framework ini. Pada dasarnya opsi ini membuat 2 project, yaitu project untuk library pod itu sendiri dan project untuk meload library pod itu.

```bash
Which testing frameworks will you use? [ Quick / None ]
 > none

Would you like to do view based testing? [ Yes / No ]
 > no

``` 

Untuk mempermudah, maka penulis tidak melakukan testing apapun pada pod ini.

Setelah semua selesai, xcode akan terbuka otomatis dengan semua file-file defaultnya.

![xcode](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/uk1jd24lhth6vnnavhfe.png#center)

Buka file `formlogin.podspec` pada bagian `s.source_files = 'formLogin/Classes/**/*'` mengindikasikan bahwa semua file yang dianggap `class` nantinya harus berada di folder tersebut.

![formlogin.podspec](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/lmtebzfobsch9ylz4ygn.png#center)

Kemudian hapus file `ReplaceMe.swift` yang ada di project Pods.

![delete replaceme](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/r1jbz5qwqckljgixw33q.png#center)

pilih `Move to trash`.

Buat file baru yang merepresentasikan nama class dalam kasus ini `FormLogin.swift` yang merupakan turunan dari `UITextField`.

![create new file](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/vuoykesajwhidur2y1am.png#center)

Kemudian simpan pada `formLogin/Classes/`

![save file](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/n4gjxt1tsbvmsapxwb6n.png#center)

paste isi file dari [postingan sebelumnya](https://www.cizzu.com/2017/05/21/ios-login-form-dengan-validasi/) atau paste dari code dibawah

```swift
//
//  FormLogin.swift
//  Pods
//
//  Created by Afriyandi Setiawan on 5/27/17.
//
//

import UIKit

class ResponsiveTextField: UITextField {
    
    @IBOutlet public weak var nextResponderField: UIResponder?
    @IBOutlet public weak var prevResponderField: UITextField?
    
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
    
}

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

extension String {
    var localize:String {
        guard let mainBundle = Bundle(identifier: Bundle.main.bundleIdentifier!) else {
            return self
        }
        return NSLocalizedString(self, tableName: nil, bundle: mainBundle, value: "", comment: "")
    }
}

```

Agar bisa digunakan selain di project pod tersebut, maka `class ResponsiveTextField` perlu merubah access controlnya menjadi open.

```language-swift
open class ResponsiveTextField: UITextField {
```

Buat form pada project form login dan atur `connection inspector`-nya seperti yang telah dibuat di [project sebelumnya](https://www.cizzu.com/2017/05/21/ios-login-form-dengan-validasi/#storyboard),

![login form](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/ucl7ayzgc70wuj8b2dez.png#center)

![koneksi ke storyboard](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/jmacbjk0dbfnfzbypokn.png#center)

![koneksi ke storyboard](https://res.cloudinary.com/tendabiru/image/upload/q_auto:good,w_0.5/ooumvo8pouj5cviqda7s.png#center)

Jalankan via storyboard. Apabila semua berjalan normal maka hasilnya akan seperti berikut

![running](https://res.cloudinary.com/tendabiru/image/upload/v1495308513/h5b5qedctrzw1wkk7cvj.gif#center)

# Integrasi pod

Lalu, bagaimana menintegrasikan pod ini ke dalam project? cocoapod sendiri menyediakan repository umum, yang secara default dapat digunakan oleh siapapun dan siapapun dapat berkontribusi di dalamnya. Namun, cocoapod juga mendukung repository yang sifatnya private, sehingga apabila user mendevelop pod/library yang hanya digunakan secara terbatas maka cocoapod sudah mendukung hal tersebut. 

Cocoapod juga mendukung pod yang bersifat local seperti yang dilakukan pada pod development di atas, serta mendukung instalasi pod langsung dari server git setperti github, bitbucket, dll. Untuk tulisan kali ini, yang dicontohkan adalah cocoapod yang diambil dari github langsung. Pod yang dibuat pada post ini di simpan di repository berikut [https://github.com/aphe/formLogin](https://github.com/aphe/formLogin).

Untuk menambahkan repository ini pada project cukup menambahkan baris perintah berikut pada file `Podfile`. Untuk mendapatkan `Podfile` maka perlu dilakukan `pod init` terlebih dahulu  pada project baru yang ingin menggunakan library tersebut.

Anggap skenario seperti berikut,

1. Dibuat project dengan nama `Login Anywhere`
2. `Login anywhere`  menggunakan pod form Login yang diambil dari [https://github.com/aphe/formLogin](https://github.com/aphe/formLogin)
3.  Login anywhere dapat menvalidasi menggunakan validasi yang ada pada library formLogin

Untuk melakukan hal tersebut maka buat project baru dengan nama project `Login Anywhere` kemudian init pod dengan 

```bash
pod init
```

Maka akan dibuatkan satu file bernama `Podfile` ubah pod file tersebut dengan menambahkan baris berikut  diantar notasi `target <project name>` dan `end`

```language-ruby
target 'Login Anywhere' do
  # Comment the next line if you're not using Swift and don't want to use dynamic frameworks
  use_frameworks!

  # Pods for testPod
  pod 'formLogin', :git => 'https://github.com/aphe/formLogin'

end
```

apabila ingin menambahkan library lainnya, cukup notasikan pod berikutnya dibawah pod sebelumnya tanpa menggunakan deliminiter apapun.

Setelah disimpan, kemudian jalankan perintah berikut,

```bash
pod install
```

kemudian secara otomatis akan dibuat satu file workspace dengan nama yang sama seperti nama project. Buka workspace tersebut dengan perintah 

```bash
open Login\ Anywhere.xcworkspace
```

Maka xcode pun akan membuka workspace tersebut yang didalamnya terdiri 2 project, yaitu project `Login Anywhere` dan project `Pods` yang didalamnya berisi library-library yang digunakan.

Kiranya sekian post kali ini, apabila ada yang ingin ditanyakan, diprotes, atau apapun silahkan berkomentar via kolom komentar di bawah.

salam.