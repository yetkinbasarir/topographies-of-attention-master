# Topographies of Attention — Ana Kimlik Referansı (CLAUDE.md)

Bu dosya her yeni Claude konuşmasının başında paylaşılır.
Sistem hakkında bağlam kurar, terminolojiyi tanımlar, kod üretimini yönlendirir.

---

## NASIL KULLANILIR

### Bu dosyayı her yeni chate şöyle başlat:
```
Merhaba. Aşağıdaki CLAUDE.md dosyasını oku ve bu bağlamda çalışmaya başla.
[CLAUDE.md içeriğini yapıştır]
Bugünkü konu: [ne yapacağını yaz]
```

### Master Chat ile ilişki:
- Her oturumun sonunda Master Chat'e kısa bir ilerleme notu bırak
- "Scope typography modülü tamamlandı, CLAUDE.md güncellendi" gibi
- Yeni bir karar alındıysa CLAUDE.md'yi güncelle ve Master Chat'e bildir

---

## Sistemin Felsefesi

Topographies of Attention, birden fazla iOS uygulamasını tek bir kimlik
şemsiyesi altında tutan davranışsal bir tasarım sistemi altyapısıdır.

**Davranışsal** kelimesinin anlamı: Kimlik öğeleri — renk, grid, tipografi,
animasyon — dışarıdan gelen verilere (günün saati, mevsim, hava durumu,
kullanıcı etkileşimi vb.) önceden tanımlanmış algoritmalar aracılığıyla
tepki verebilir. Bu tepkiler rastgele değildir; sistemin sınırları içinde
tanımlı olasılıklar arasında gerçekleşir.

**Temel ilke:** Her uygulama kendi işlev ve konseptini taşır, ancak görsel
ve mekansal dil her zaman bu sistemden gelir. Hiçbir uygulama boş sayfayla
başlamaz.

**Tek geliştirici.** Temel tasarım kararları değişmez.
Sistem büyüdükçe bu dosya büyür.

**Platform:** Öncelikle iPhone / iOS. iPad ve Mac henüz tanımsız.

---

## Katman Sırası

| # | Katman | Paket | Durum |
|---|--------|-------|-------|
| 1 | Color | TOFAColorSystem | Hazır |
| 2 | Grid | TOAGridKit | Hazır |
| 3 | Typography | TOATypographyKit | Geliştiriliyor |
| 4 | Icons | — | Planlanıyor |
| 5 | Buttons | — | Planlanıyor |
| 6 | Animation | — | Planlanıyor |

---

## 1. Color System

**Package:** `TOFAColorSystem`
**Import:** `import TOFAColorSystem`
**Repo:** `topographies-of-attention-color-system`
**Platform:** iOS 17+, macOS 14+

### Temel İlkeler
- Renkler gün ve gece arasında **değişmez**
- Gece modu aynı renk setinin **rollerini yeniden atayarak** kurulur
- Hiçbir uygulama renk hardcode etmez — her renk bu paketten gelir

### Rol Sistemi

| Rol | Gündüz | Gece |
|-----|--------|------|
| `background` | topography | darkInk |
| `primaryText` | darkInk | topography |
| `secondaryText` | midInk | midInk |
| `line` | attention | attention |
| `attention` | attention | attention |

Shell temasında `attention` rolü `nil`'dir.

### Shell Temaları

**Shell Day:** background `#F1F4F8` · text `#202A38` · secondary `#6A7384`
**Shell Night:** background `#EEE8E3` · text `#23201E` · secondary `#8F8680`

### Aylık Temalar (12 ay × 2 faz = 24 tema)

| Ay | Attention Adı | Topography | Attention |
|----|--------------|------------|-----------|
| Ocak | Signal Blue | `#CCDFFF` | `#5457E8` |
| Şubat | Air Blue | `#BAE4FA` | `#0072E7` |
| Mart | Tide Teal | `#B3E7EE` | `#0D828E` |
| Nisan | Sprout Green | `#C6E6CD` | `#27864E` |
| Mayıs | Leaf Lime | `#D9E2C1` | `#637F18` |
| Haziran | Pollen Gold | `#EBDDBD` | `#956F16` |
| Temmuz | Apricot Flame | `#F0DBBE` | `#B95B17` |
| Ağustos | Coral Pulse | `#FDD6C5` | `#DD3423` |
| Eylül | Rose Signal | `#FFD4CE` | `#D13E63` |
| Ekim | Berry Magenta | `#FFD3DF` | `#B84D9C` |
| Kasım | Violet Mark | `#F4D5EF` | `#8561CB` |
| Aralık | Indigo Pulse | `#E1DAFB` | `#5869E5` |

### 26 Tema Kodu
```swift
.shellDay / .shellNight
.januaryDay/.januaryNight — .decemberDay/.decemberNight
```

### Temel API
```swift
// Otomatik — sunrise/sunset ile
let bundle = TOFAThemeResolver.bundle(for: Date(), sunrise: s, sunset: s)

// Fallback — saat bazlı
let phase = TOFAThemeResolver.phase(now: Date())
let bundle = TOFAThemeResolver.bundle(for: Date(), phase: phase)

// Doğrudan kod ile
let theme = TOFAThemeLibrary.theme(for: .aprilDay)

// Kullanım
view.background(theme.background)
text.foregroundColor(theme.primaryText)
```

### Terminoloji
- **topography** → ayın zemin rengi, gündüz background'u
- **attention** → ayın sinyal rengi, her zaman line rengi
- **shell** → aylık renklerden bağımsız nötr kabuk
- **bundle** → shell + monthly temasının birlikte döndürüldüğü yapı
- **faz** → `.day` veya `.night`

---

## 2. Grid System

**Package:** `TOAGridKit`
**Import:** `import TOAGridKit`
**Repo:** `topographies-of-attention-grid-system`
**Platform:** iOS 17+, macOS 14+

### Temel İlkeler
- Minimum dikey birim: **8 pt** (1 row = 8 pt)
- Aktif yatay alan: her zaman **368 pt**
- Grid orijini: top safe area içindeki ilk 8 pt çizgisi
- Her ekran **tek bir mod** seçer

### İki Mod

| Mod | Sütun | Genişlik | Gutter |
|-----|-------|----------|--------|
| `.six` | 6 | 48 pt | 16 pt |
| `.four` | 4 | 80 pt | 16 pt |

### Division'lar

**Mode 6:**
`.six` · `.threeThree` · `.fourTwo` · `.twoTwoTwo` · `.oneTwoThree` · `.oneOneFour` · `.oneOneOneOneOneOne`

**Mode 4:**
`.four` · `.twoTwo` · `.threeOne` · `.oneThree` · `.oneOneTwo` · `.oneOneOneOne`

### Yükseklik Davranışları
```swift
.fixed(8)                                        // sabit 8 row
.dynamic(minRows: 3, maxRows: 10)                // içerik ölçülür
.linkedDynamic(group: "x", minRows: 3, maxRows: 10) // grupla eşitlenir
```

### Margin Compensation
393 pt ekran → `(393-368)/2 = 12.5` → sol: 12, sağ: 13

### Temel API
```swift
TOAGridProvider(mode: .six) {
    TOAGridOverlay() // sadece debug
}

@Environment(\.toaGridContext) var context
let origin = context.gridOriginY
let width = context.width(for: 3)
let height = context.rowsToHeight(5)

let engine = TOAFlowEngine()
let placed = engine.place(groups: groups, context: context)
```

### Bilinen Düzeltmeler Gerekli
1. `TOAGridProvider` içinde `displayScale` hardcoded `2` → environment'tan okunmalı
2. `TOAGridOverlay` yüksekliği `240pt` hardcoded → `proxy.size.height` olmalı

### Terminoloji
- **row** → 8 pt dikey birim
- **active field** → 368 pt yatay çalışma alanı
- **grid origin** → safe area içindeki ilk baseline
- **mode** → ekranın sütun yapısı (4 veya 6)
- **division** → modun yatay bölünme kompozisyonu
- **gutter** → sütunlar arası 16 pt boşluk
- **flow engine** → yukarıdan aşağıya yerleşim motoru

---

## 3. Typography System

**Package:** `TOATypographyKit` *(geliştiriliyor)*
**Repo:** `topographies-of-attention-typography-system`

### Temel İlkeler
- Minimum punto birimi: **8 pt** (Apple pt)
- Her fontun cap-height veya x-height'ı 8 pt grid'e kilitlenir
- Line-height her zaman 8 pt'nin katıdır
- Punto her varyant için sabittir (bold, italic aynı puntoyu kullanır)

### Ölçüm Yöntemi
Her font için iki ayrı kalibrasyon yapılır:
1. **Cap-height kalibrasyonu** → büyük harf yüksekliği 8 pt'ye kilitlenir
2. **x-height kalibrasyonu** → küçük harf yüksekliği 8 pt'ye kilitlenir

Hangi ölçümün kullanılacağı o fontun tasarım karakterine göre seçilir.

### Stil Hiyerarşisi
`caption` · `bodytext` · `title` · `subtitle` · `display`

Her stil bir **aile** — içinde varyantlar barındırır:
- `regular`, `bold`, `italic`, `boldItalic`
- Genişlik varyantları: `condensed`, `extended` (destekleniyorsa)
- Tüm varyantlar **aynı puntoyu** kullanır

### Grid İlişkisi
```
bodytext (cap-height temelli)
  punto:       11.84pt  (cap = 8pt için hesaplanan)
  line-height: 24pt     (×3 grid)
  grid-rows:   3
```

*(Detaylı API TYPOGRAPHY.md'de tanımlanacak)*

---

## 4. Scope Utility Uygulaması

**Uygulama adı:** Scope
**Platform:** macOS 14+
**Teknoloji:** SwiftUI
**Amaç:** TOA ekosistemi için tasarım kararlarını test eden utility

### Modüller
1. **Color** — TOFAColorSystem görselleştirme
2. **Grid** — TOAGridKit önizleme
3. **Typography** — Font kalibrasyon + stil tanımlama
4. **Data** — RSS veri sistemi + filtre arayüzü

*(Detaylar SCOPE.md'de)*

---

## 5. Data System

**Sistem adı:** t-of-a data
**İlk versiyon:** RSS odaklı

### Amaç
Tasarım sisteminin canlı veriyle test edilmesini sağlar.
Scope içinde yaşar, diğer modüllerle konuşur.

*(Detaylar DATA.md'de)*

---

## Hızlı Terminoloji Referansı

| İfade | Karşılığı |
|-------|-----------|
| "nisan modu" / "april" | `.aprilDay` veya `.aprilNight` |
| "shell" | `.shellDay` / `.shellNight` |
| "gece" | `TOFAPhase.night` |
| "attention rengi" | o ayın `attentionHex` |
| "6 kolon" | `TOAGridMode.six` |
| "4 kolon" | `TOAGridMode.four` |
| "3+3 bölünme" | `TOADivision.threeThree` |
| "sabit X row" | `.fixed(X)` |
| "dinamik yükseklik" | `.dynamic(minRows:maxRows:)` |
| "bağlı dinamik" | `.linkedDynamic(group:minRows:maxRows:)` |

---

## Yeni Uygulama Başlarken

1. `Package.swift`'e `TOFAColorSystem` ve `TOAGridKit` ekle
2. `TOFAThemeResolver` ile tarihe ve faza göre bundle al
3. Grid modunu belirle: `.four` veya `.six`
4. `TOAGridProvider` ile ekran iskeleti oluştur
5. Debug'da `TOAGridOverlay` aç, yayında kapat
6. Hiçbir renk hardcode etme — her renk `theme.*` üzerinden gelsin
