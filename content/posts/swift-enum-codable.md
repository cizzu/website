---
title: "Swift Enum Codable"
date: 2020-11-29T12:41:18+07:00
categories:
  - Notes
  - Swift
tags: [development, swift]
keywords: [swift, enum, codable, encodable, decodable, json, array, dictionary]
draft: false
# thumbnailImage: https://source.unsplash.com/xxxx/
#thumbnailImagePosition: "top"
coverImage: https://source.unsplash.com/QQKfUpuT8u8/
---

Swift codable merupakan salah satu protocol yang mungkin akan sering digunakan, karena mempermudah developer untuk mem-parsing JSON format yang umumnya merupakan standart baku return dari API server.

<!--more-->

[Codable](https://developer.apple.com/documentation/swift/codable) sendiri merupakan type alias dari [Encodable](https://developer.apple.com/documentation/swift/encodable) dan [Decodable](https://developer.apple.com/documentation/swift/decodable), yang berarti dapat meng-encode ataupun men-decode 'dirinya' menjadi bentuk lain tanpa perlu ada proses manual.

Anggap ada json yang berisi beberapa karakter game of thrones season akhir yang menjelaskan apakah karakter itu masih hidup dan nama aliasnya,

```swift
let json = """
{
  "characters": [
    {
    "first_name": "Daenerys",
    "last_name": "Targaryen",
    "alias": "The Mother of Dragons",
    "is_alive": 1
    },
    {
    "first_name": "Cersei",
    "last_name": "Lannister",
    "alias": null,
    "is_alive": 0
    },
    {
    "first_name": "Jon",
    "last_name": "Snow",
    "alias": "The White Wolf",
    "is_alive": "resurrected"
    }
  ]
}
"""
```

### Old fashioned way

Maka dahulu kala untuk memparsing dapat dilakukan dengan hal semacam berikut,

```swift
let jsonObj = Data(json.utf8)
if let objects = try JSONSerialization.jsonObject(with: jsonObj) as? [String: Any] {
    if let characters = objects["characters"] as? [[String: Any]] {
        for character in characters {
            let name = character["first_name"] ?? ""
            if let isAlive = character["is_alive"] as? Int {
                print("\(name) is \(isAlive != 0 ? "alive" : "dead")")
            } else if let other = character["is_alive"] as? String {
                print("\(name) is \(other)")
            }
        }
    }
}
```

Semua nya masih terbilang manual, dan banyak type casting disitu yang kalau json nya lebih panjang maka type casting nya pun jadi lebih panjang dan _ngejelimet_.

Output yang dihasilkan (spoiler alert)

```bash
Daenerys is alive
Cersei is dead
Jon is resurrected
```

### SwiftyJSON (kind of) way

Atau kalau rajin sedikit bisa dengan membuat satu struct sendiri seperti berikut

```swift
struct RawJSON {

    var value: Any?

    init(json string: String) throws {
        let data = Data(string.utf8)
        value = try JSONSerialization.jsonObject(with: data, options: .allowFragments)
    }

    init(value: Any?) {
        self.value = value
    }

    var string: String? {
        value as? String
    }

    var int: Int? {
        value as? Int
    }

    var array: [RawJSON]? {
        let converted = value as? [Any]
        return converted?.map{ RawJSON(value: $0) }
    }

    var dictionary: [String: RawJSON]? {
        let converted = value as? [String: Any]
        return converted?.mapValues { RawJSON(value: $0) }
    }

    subscript(key: String) -> RawJSON {
        dictionary?[key] ?? RawJSON(value: nil)
    }
}
```

Pemakaiannya berubah menjadi

```swift
let jsonRaw = try RawJSON(json: json)
jsonRaw["characters"].array?.forEach({ (json) in
    if let isAlive = json["is_alive"].int {
        print("\(json["first_name"].string ?? "") is \(isAlive != 0 ? "alive" : "dead")")
    } else if let other = json["is_alive"].string {
        print("\(json["first_name"].string ?? "") is \(other)")
    }
})
```

Outputnya masih sama seperti metode sebelumnya.

Developer yang pernah bersinggungan dengan [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON) mungkin familiar dengan konsep diatas.

Sudah tidak diperlukan lagi type casting walaupun pada contoh diatas seluruh type yang keluar adalah optional, kalaupun optionalnya mau dihilangkan yang paling mudah dengan cara membuat satu variable non-optional yang membuka optional value dengan menyediakan default value sebagai fall back nya.

Kekurangannya adalah seluruh key masih perlu diinput manual dan literal, walaupun bisa disiasati dengan membuat semacam list ataupun enum untuk masing-masing key nya.

### Codable Way

Jika menggunakan metode Codable, yang pertama harus dilakukan adalah membuat sebuah struct yang mengadopsi protocol Codable seperti berikut,

```swift
struct Characters: Codable {
    let characters: [Detail]

    struct Detail: Codable {
        let firstName: String
        let lastName: String
        let alias: String
        let isAlive: Int

        enum CodingKeys: String, CodingKey {
            case firstName = "first_name"
            case lastName = "last_name"
            case alias
            case isAlive = "is_alive"
        }
    }
}
```

🤔️ terlihat lebih rapih, terstruktur dan mudah dibaca oleh orang lain, seluruh key nya pun bisa terlebih dahulu di definisi dengan CodingKey yang "ajaib".

Cara penggunaannya pun cendrung lebih mudah, seperti berikut

```swift
let jsonObj = Data(json.utf8)
let decoded = try JSONDecoder().decode(Characters.self, from: jsonObj)
decoded.characters.forEach { (character) in
    switch character.isAlive {
    case .alive:
        print("\(character.firstName) is alive")
    case .dead:
        print("\(character.firstName) is dead")
    case .other(let status):
        print("\(character.firstName) is \(status)")
    }
}
```

Output masih sama seperti dua metode sebelumnya, tapi melihat sepintas metode dengan Codable ini sepertinya lebih deklaratif, lugas dan bisa dipahami secara sederhana.

### Blocker

Tapi... dengan contoh JSON diatas,

Jika dijalankan maka terjadi error seperti berikut
![error](https://res.cloudinary.com/tendabiru/image/upload/v1606655721/swift_error.jpg)

hal ini disebabkan oleh `alias` key yang ternyata bisa diisi oleh type **String** ataupun JSON null (nil).

Karena swift secara DNA sudah mengadopsi konsep optional value maka untuk mengatasi hal tersebut, `alias: String` bisa dibuat menjadi optional sehingga struct Characters menjadi seperti berikut,

```swift
struct Characters: Codable {
    let characters: [Character]

    struct Character: Codable {
        let firstName: String
        let lastName: String
        let alias: String?
        let isAlive: Int

        enum CodingKeys: String, CodingKey {
            case firstName = "first_name"
            case lastName = "last_name"
            case alias
            case isAlive = "is_alive"
        }
    }
}
```

Ok, coba jalankan lagi.

![error](https://res.cloudinary.com/tendabiru/image/upload/v1606656635/swift_error_2_yo3niu.png)
🤔️ ternyata masih ada error

Ternyata ini dikarenakan `is_alive` yang valuenya bisa berupa **Int** tapi kadang juga bisa berupa **String**.

Dari sini terlihat kekurangan dari Codable itu sendiri, yaitu sebaiknya antara developer backend dan developer mobile harus memiliki contract API yang jelas diawal.

Tapi kadang, developer mobile tidak punya kuasa untuk mengatur developer backend 😅️.

Maka sebaiknya kita bisa berdamai dengan diri sendiri 👼️, dan salah satu alternatif yang mungkin bisa dilakukan ketika bertemu situasi seperti ini dengan memasukan value `is_alive` kedalam sebuah enum yang pastinya juga mengadopsi protocol Codable.

Implementasinya kira-kira seperti ini,

```swift
struct Characters: Codable {

    enum isAlive: Codable {

        case alive
        case dead
        case other(String)

        func encode(to encoder: Encoder) throws {
            var sd = encoder.singleValueContainer()
            switch self {
            case .alive:
                try sd.encode(1)
            case .dead:
                try sd.encode(0)
            case .other(let str):
                try sd.encode(str)
            }
        }

        init(from decoder: Decoder) throws {
            let container = try decoder.singleValueContainer()
            if let value = try? container.decode(Int.self) {
                switch value {
                case 0:
                    self = .dead
                case 1:
                    self = .alive
                default:
                    self = .other("Undefined")
                }
            } else if let value = try? container.decode(String.self) {
                self = .other(value)
            } else {
                self = .other("Undefined")
            }
        }
    }

    let characters: [Details]

    struct Details: Codable {
        let firstName: String
        let lastName: String
        let alias: String?
        let isAlive: isAlive

        enum CodingKeys: String, CodingKey {
            case firstName = "first_name"
            case lastName = "last_name"
            case alias
            case isAlive = "is_alive"
        }
    }
}
```

Karena enum isAlive ini Codable, maka secara tidak langsung perlu adanya func yang bisa men-decode dan men-encode, karena seperti dibilang di atas, Codable sendiri adalah type alias dari Encodable dan Decodable.

enum isAlive ini ga harus berada di dalam struct Characters ya, tapi bisa dipisah.

Jika dipisah kira-kira akan jadi seperti ini,

```swift
enum isAlive: Decodable {

    case alive
    case dead
    case other(String)

    init(from decoder: Decoder) throws {
        let container = try decoder.singleValueContainer()
        if let value = try? container.decode(Int.self) {
            switch value {
            case 0:
                self = .dead
            case 1:
                self = .alive
            default:
                self = .other("Undefined")
            }
        } else if let value = try? container.decode(String.self) {
            self = .other(value)
        } else {
            self = .other("Undefined")
        }
    }
}

extension isAlive: Encodable {
    func encode(to encoder: Encoder) throws {
        var sd = encoder.singleValueContainer()
        switch self {
        case .alive:
            try sd.encode(1)
        case .dead:
            try sd.encode(0)
        case .other(let str):
            try sd.encode(str)
        }
    }
}

struct Characters: Codable {

    let characters: [Details]

    struct Details: Codable {
        let firstName: String
        let lastName: String
        let alias: String?
        let isAlive: isAlive

        enum CodingKeys: String, CodingKey {
            case firstName = "first_name"
            case lastName = "last_name"
            case alias
            case isAlive = "is_alive"
        }
    }
}
```

Jadi lebih jelas ya pembagiannya, mana fungsi yang men-decode dan mana yang men-encode.

Untuk merubah dari data ke json string juga bisa, cara penggunaanya seperti berikut,

```swift
let budi = Characters(characters: [Characters.Details(firstName: "Budi", lastName: "Ono", alias: "King of the King", isAlive: .other("not exist"))])
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted
let jsonData = try encoder.encode(budi)
let jsonString = String(data: jsonData, encoding: .utf8)
print(jsonString!)
```

Yang akan menghasilkan output

```bash
{
  "characters" : [
    {
      "alias" : "King of the King",
      "last_name" : "Ono",
      "is_alive" : "not exist",
      "first_name" : "Budi"
    }
  ]
}
```
