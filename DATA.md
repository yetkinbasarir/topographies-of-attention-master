# t-of-a Data System — DATA.md

## BU CHAT'İ NASIL KULLANIRSIN

### Oturum başında:
1. CLAUDE.md dosyasını yapıştır
2. Bu DATA.md dosyasını yapıştır
3. Şöyle başla:
   ```
   Merhaba. Aşağıdaki iki dosyayı oku:
   1. CLAUDE.md [yapıştır]
   2. DATA.md [yapıştır]
   Bugün data sisteminde [ne yapacağını yaz] konusunu çalışacağız.
   ```

### Oturum sonunda:
1. Bu dosyayı güncelle
2. MASTER.md'ye kısa not düş
3. Yeni RSS kaynağı veya filtre kuralı tanımlandıysa ilgili bölümleri güncelle

---

## Sistemin Amacı

t-of-a data sistemi, TOA tasarım sistemini **canlı veriyle** test etmeyi sağlar.

**Olmadan:** Tasarım kararları statik örnek metinlerle test edilir.
**Olunca:** Grid, typography ve renk sistemi gerçek RSS verisiyle önizlenir.
Dinamik davranışlar gözlemlenebilir — örneğin farklı uzunluktaki başlıklar
grid'i nasıl etkiler, attention rengi hava durumu verisine nasıl tepki verir.

---

## Mimari

### t-of-a data v1 Kapsamı
Kasıtlı olarak sınırlı tutulmuştur:

- **Giriş:** RSS feed
- **İşlem:** Filtre kuralları
- **Çıkış:** İsimlendirilmiş, sınıflandırılmış veri nesneleri
- **Son ürün:** `.md` dosyası — Scope'un diğer modülleri bu .md'yi okur

### Veri Akışı
```
RSS URL
  ↓
Feed Parser
  ↓
Ham Öğeler [RSSItem]
  ↓
Filtre Motoru (kullanıcı kuralları)
  ↓
Filtrelenmiş Öğeler
  ↓
Etiketleme & Sınıflandırma
  ↓
TOADataObject (isimlendirilmiş, sınıflandırılmış)
  ↓
.md Export → Scope modülleri okur
```

---

## Veri Modeli

### RSSItem — Ham Veri
```swift
struct RSSItem {
    let title: String
    let description: String?
    let link: URL?
    let pubDate: Date?
    let imageURL: URL?
    let author: String?
    let categories: [String]
}
```

### TOADataObject — İşlenmiş Veri
```swift
struct TOADataObject {
    let id: UUID
    let sourceKey: String        // "bbc-news", "weather-istanbul"
    let label: String            // kullanıcının verdiği isim
    let category: TOADataCategory
    let primaryText: String      // başlık veya ana içerik
    let secondaryText: String?   // açıklama
    let timestamp: Date
    let imageURL: URL?
    let metadata: [String: String]  // ek alan değerleri

    // TOA sistemiyle bağlantı
    let suggestedGridSignal: TOAContentSignal?  // grid'e veri ipucu
    let suggestedThemeOverride: TOFAThemeCode?  // renk override
}
```

### TOADataCategory
```swift
enum TOADataCategory: String {
    case headline     // ana haber
    case brief        // kısa haber
    case feature      // uzun form
    case weather      // hava durumu
    case market       // piyasa verisi
    case custom       // kullanıcı tanımlı
}
```

---

## Filtre Sistemi

### Filtre Kuralı Yapısı
```swift
struct TOAFilterRule {
    let field: RSSField          // .title, .description, .pubDate, .imageURL
    let condition: FilterCondition
    let value: String

    enum RSSField {
        case title, description, author, pubDate, imageURL, category
    }

    enum FilterCondition {
        case contains(String)
        case notContains(String)
        case longerThan(Int)       // karakter sayısı
        case shorterThan(Int)
        case newerThan(TimeInterval)  // saniye
        case hasImage
        case hasNoImage
        case matchesCategory(String)
    }
}
```

### Filtre Seti
```swift
struct TOAFilterSet {
    let name: String
    let sourceURL: URL
    let rules: [TOAFilterRule]
    let combineWith: LogicOperator  // .and, .or
    let maxItems: Int?

    enum LogicOperator { case and, or }
}
```

### Örnek Filtre
```
Kaynak: https://feeds.bbci.co.uk/news/rss.xml
Kurallar:
  - pubDate son 24 saat içinde
  - description 100 karakterden uzun
  - imageURL var
Mantık: AND
Max: 10 öğe
```

---

## İsimlendirme ve Sınıflandırma

Kullanıcı her filtrelenmiş veri öğesine bir **label** ve **category** atar.

Bu etiketler Scope'un diğer modülleri tarafından kullanılır:
- Grid modülü: `label` → `TOAFlowCell.id` olarak kullanılabilir
- Typography modülü: `primaryText` → önizleme metni
- Color modülü: `category` → tema seçimini etkileyebilir

---

## .md Çıktı Formatı

```markdown
# TOA Data — [Feed Adı]
Oluşturulma: [tarih]
Kaynak: [URL]
Filtre: [filtre seti adı]

## Öğeler

### [label]: [primaryText]
- Kategori: [category]
- Tarih: [timestamp]
- Açıklama: [secondaryText]
- Görsel: [imageURL]
- Grid Sinyali: textLength=[n], hasImage=[bool]
```

---

## Scope Data Modülü

### Arayüz
**Sol panel:** RSS kaynakları listesi
**Orta panel:** Filtre kuralları editörü
**Sağ panel:** Filtrelenmiş önizleme

### Feed Yönetimi
- Feed URL ekle/sil
- Feed adı ver (ör. "BBC World", "Hava Durumu İstanbul")
- Son çekme zamanı göster
- Manuel yenile butonu

### Filtre Editörü
Her kural görsel olarak eklenir — kod yazılmaz.
Arayüz: field seçimi + condition seçimi + değer girişi.
Kurallar listesi — sıra önemli değil, AND/OR mantığı seçilir.

### Etiketleme
Filtrelenmiş öğeler önizleme listesinde görünür.
Her öğeye: label ve category atanır.
"Hepsine uygula" ile tüm öğelere aynı category verilebilir.

### Diğer Modüllerle Bağlantı
Data modülünden "Önizlemeye gönder" dendiğinde:
- Typography modülü bu veriyle metin önizlemesi yapar
- Grid modülü bu veriyle flow engine'i test eder

---

## İlerleme

### Tamamlanan
- Sistem konsepti ve amaç
- Veri modeli kararları
- Filtre sistemi tasarımı

### Devam Ediyor
- (henüz Xcode'da başlatılmadı)

### Sıradaki Adım
Scope uygulaması çalışır hale gelince data modülü eklenecek.
İlk adım: `RSSParser.swift` — basit XML parser, `RSSItem` döndürür.
