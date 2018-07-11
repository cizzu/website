+++
author = "Afriyandi Setiawan"
date = 2017-09-11T03:29:18Z
description = ""
draft = true
slug = "aplikasi-dengan-touch-id-ios-part-2"
title = "Aplikasi Dengan Touch ID iOS - part 2"

+++

Sebelum membaca keseluruhan dari post ini, ada baiknya melihat post [sebelumnya (part 1)](https://www.cizzu.com/2017/06/10/aplikasi-dengan-touchid-ios-part-1/) dahulu.

Beberapa hal yang sudah dijabarkan antara lain adalah membuat tampilan dan coding awal. Untuk part 2 ini lebih ke dalam coding dan implementasinya.

Oke, karena update nya sudah terlalu lama langsung kita mulai saja.

Terakhir untuk tampilan sudah selesai semua, sekarang yang pertama dilakukan adalah memberikan aksi ketika tombol2 disentuh. Dan setiap sentuhan akan merubah icon-icon indicator diatasnya, sekaligus menyimpan passpharse untuk security code nya. Untuk melakukan hal tersebut buka file tambahkan outlet untuk icon indicator dengan menarik dan menahan tombol ctrl. Karena icon-icon ini sudah menjadi stackView maka outlet stackView tersebut menjadi outlet nya.

Yang kita lakukan adalah menset bakcup password untuk touchID. Karena touchID akan dianggap tidak valid setelah 3 kali gagal. Ataupun hal ini diperlukan untuk device-device yang tidak memiliki fitur touchID.

Sebelum memulai untuk mempercantik tampilan, rubah `extension UIButton` menjadi `extension UIView` sehingga icon-icon indicator bisa dirubah corner-nya menjadi bulat dan memiliki border berwarna abu-abu atau gray, jangan lupa ubah color nya menjadi putih atau default. 

Kemudian tarik dan jadikan outlet

*stackView

##Add button action

Buat satu internal variable dengan nama passphrase dengan type string, variable ini yang digunakan dan menjadi passphrase password sementara

```swift
    internal var passPhrase:String = ""
```

Oke, lalu tambahkan baris kode berikut pada action button

```swift
    @IBAction func onButtonTap(_ sender: UIButton) {
        passPhrase += sender.currentTitle ?? ""
        let _indicatorIcon = indicatorIcon.arrangedSubviews.filter { (view) -> Bool in
            return view.backgroundColor == .gray
        }
        if _indicatorIcon.count < indicatorIcon.arrangedSubviews.count {
            UIView.animate(withDuration: 0.2, animations: {
                self.indicatorIcon.arrangedSubviews[_indicatorIcon.count].backgroundColor = .gray
            })
            if _indicatorIcon.count == indicatorIcon.arrangedSubviews.count - 1 {
                print(passPhrase)
            }
        } else {
            return
        }
    }
```

Test pada simulator, apabila benar maka setelah 4 tombol angka yang ditekan maka pada debug area akan mencetak angka yang ditekan sebelumnya.

Selanjutnya, tambahkan action pada tombol delete seperti di bawah ini.

```swift
    @IBAction func onUndoTap(_ sender: UIButton) {
        if passPhrase != "" {
            let view = indicatorIcon.arrangedSubviews[passPhrase.characters.count - 1]
            UIView.animate(withDuration: 0.2, animations: {
                view.backgroundColor = .white
            }, completion: { _ in
                self.passPhrase.remove(at: self.passPhrase.index(before: self.passPhrase.endIndex))
            })
        }
    }
```

##Save passphrase

Selanjutnya, yang perlu dilakukan adalah menyimpan passphrase tersebut. Untuk mempermudah tutorial ini, maka passphrase tidak di enkrip dan akan disimpan di UserDefaults bawaan framework iOS.

Untuk menyimpan maka tambahkan function berikut

```swift
    func passPhraseAction() -> (isActive:Bool, passPhrase:String?) {
        let userDef = UserDefaults()
        switch userDef.value(forKey: "passPhrase") {
        case nil:
            userDef.set(passPhrase, forKey: "passPhrase")
            return (true, passPhrase)
        case let value as String:
            return (true, value)
        default:
            return (false, nil)
        }
    }
```

Kemudian rubah function `onButtonTap` menjadi seperti dibawah

```swift
    @IBAction func onButtonTap(_ sender: UIButton) {
        passPhrase += sender.currentTitle ?? ""
        let _indicatorIcon = indicatorIcon.arrangedSubviews.filter { (view) -> Bool in
            return view.backgroundColor == .gray
        }
        if _indicatorIcon.count < indicatorIcon.arrangedSubviews.count {
            UIView.animate(withDuration: 0.2, animations: {
                self.indicatorIcon.arrangedSubviews[_indicatorIcon.count].backgroundColor = .gray
            })
            if _indicatorIcon.count == indicatorIcon.arrangedSubviews.count - 1 {
                if let setting = isSetting, setting == true {
                    let alert = UIAlertController(title: "Save Passcode", message: "Do you want to save the passcode?", preferredStyle: .alert)
                    alert.addAction(UIAlertAction(title: "Save", style: .default, handler: { _ in
                        if self.passPhraseAction().isActive {
                            self.dismiss(animated: true, completion: {})
                        }
                    }))
                    alert.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: { _ in
                        self.dismiss(animated: true, completion: {})
                    }))
                    self.present(alert, animated: true, completion: {})
                }
            } else {
                return
            }
        } else {
            return
        }
    }
```
##Integrasi dengan main view

Backup password untuk touchID, sudah selesai

Kemudian tambahkan satu function untuk membaca UserDefault serta pada main view dan menambahkan baris berikut di ViewController.swift pada bagian `ViewDidAppear` dan function `prepare(for segue...)`. Dan jangan lupa tambahkan outlet untuk switch nya.

*switch connect

Function untuk membaca UserDefault dapat dijabarkan sebagai berikut

```swift
    func passPhraseIsActive() -> Bool {
        let userDef = UserDefaults()
        switch userDef.value(forKey: "passPhrase") {
        case nil:
            return false
        case let value as String where value != "":
            return true
        default:
            return false
        }
    }
```

Untuk mempermudah tutorial ini, tidak dilakukan validasi apapun untuk membaca backup passcode tersebut.

Dan perubahan pada `ViewDidAppear` dan `prepare(for segue...)`

```swift
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if let dest = segue.destination as? PassCodeViewController {
            if self.switchPassCode.isOn {
                dest.isSetting = true
            } else {
                dest.isSetting = false
            }
        }
    }
```

```swift
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        if passPhraseIsActive() {
            self.switchPassCode.isOn = true
        }
    }
```

##Implementasi Touch ID

Untuk mengimplementasikan touch id, maka diperlukan tambahan class `LocalAuthentication` serta tambahkan method nya pada function `viewDidLoad` yang ada pada file `PassCodeViewController.swift`


