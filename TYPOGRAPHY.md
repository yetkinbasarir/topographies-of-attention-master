# TOA Typography System — TYPOGRAPHY.md

## BU CHAT'İ NASIL KULLANIRSIN

### Oturum başında:
1. CLAUDE.md dosyasını yapıştır
2. Bu TYPOGRAPHY.md dosyasını yapıştır
3. Şöyle başla:
   ```
   Merhaba. Aşağıdaki iki dosyayı oku:
   1. CLAUDE.md [yapıştır]
   2. TYPOGRAPHY.md [yapıştır]
   Bugün typography sisteminde [ne yapacağını yaz] konusunu çalışacağız.
   ```

### Oturum sonunda:
1. Bu dosyayı güncelle
2. MASTER.md'ye kısa not düş
3. Yeni font veya stil tanımlandıysa "Tanımlı Fontlar" ve
   "Tanımlı Stiller" bölümlerini güncelle

---

## Sistemin Felsefesi

Typography sistemi renk ve grid sisteminin devamıdır.
Renk "rol davranışı", grid "mekansal davranış", typography "metin davranışı" tanımlar.

**Temel kural:** Fontun karakteri grid'e kilitlenir, grid değişmez.
Bir fontu sisteme eklemek demek o fontun 8 pt grid ile ilişkisini
matematiksel olarak tanımlamak demektir.

---

## Ölçüm Sistemi

### Neden Özel Ölçüm Gerekiyor
Aynı punto değerinde iki farklı font farklı görsel büyüklükte olabilir.
"12pt" demek büyük harfin 12pt olduğu anlamına gelmez.

### Çözüm
Her font için iki kalibrasyon yapılır:
- **Cap-height kalibrasyonu:** Büyük harf yüksekliği tam 8 pt grid'e kilitlenir
- **x-height kalibrasyonu:** Küçük harf yüksekliği tam 8 pt grid'e kilitlenir

Hangi kalibrasyonun kullanılacağı fontun karakterine göre seçilir.

### Matematiksel İlişki
```
cap_ratio = capHeight / fontSize
punto_for_8pt_cap = 8 / cap_ratio

// Örnek: Georgia Regular
// capHeight = 26.93pt @ 40pt → cap_ratio = 0.6733
// punto_for_8pt_cap = 8 / 0.6733 = 11.88pt
```

### macOS'ta Doğru Ölçüm API'si
```swift
import CoreText

func calibrate(fontName: String, fontSize: CGFloat) -> (cap: CGFloat, x: CGFloat) {
    let ctFont = CTFontCreateWithName(fontName as CFString, fontSize, nil)
    return (
        cap: CTFontGetCapHeight(ctFont),   // Apple pt
        x: CTFontGetXHeight(ctFont)         // Apple pt
    )
}

let metrics = calibrate(fontName: "Georgia", fontSize: 40)
let capRatio = metrics.cap / 40
let puntoFor8ptCap = 8 / capRatio  // → SwiftUI Font.custom() için
```

---

## Stil Sistemi

### 5 Stil Tag'i
```
caption    — etiket, açıklama, en küçük metin
bodytext   — ana paragraf
title      — bölüm başlığı
subtitle   — alt başlık
display    — büyük görsel başlık
```

### Her Stil Bir Aile — Aynı Punto
```
bodytext
├── regular      (400)
├── bold         (700)     — aynı punto
├── italic       (400, italic: true)  — aynı punto
├── boldItalic   (700, italic: true)  — aynı punto
└── condensed / extended  — destekleniyorsa, yine aynı punto
```

**Kritik:** Bold için ayrı punto, italic için ayrı punto olmaz.

### Stil Yapısı
```swift
public struct TOATypographyStyle {
    let tag: String           // "bodytext"
    let snapBasis: String     // "cap-height" veya "x-height"
    let size: CGFloat         // Apple pt
    let lineHeight: CGFloat   // 8pt'nin katı
    let gridRows: Int         // lineHeight / 8
    let fontName: String      // Font.custom() için
    let weight: Font.Weight
    let italic: Bool
}
```

### Line-Height Kuralı
Her zaman 8 pt'nin katı:
`×1=8pt` · `×2=16pt` · `×3=24pt` · `×4=32pt` · `×5=40pt` · `×6=48pt`

---

## SwiftUI Entegrasyonu

### Doğru Grid Hizalaması
```swift
var attributedStringAttributes: [NSAttributedString.Key: Any] {
    let style = NSMutableParagraphStyle()
    style.minimumLineHeight = lineHeight
    style.maximumLineHeight = lineHeight
    return [
        .font: NSFont(name: fontName, size: size) ?? .systemFont(ofSize: size),
        .paragraphStyle: style
    ]
}
```

---

## TOATypographyKit Paket Yapısı

```
Sources/TOATypographyKit/
├── Models/
│   ├── TOATypographyStyle.swift
│   ├── TOAStyleTag.swift
│   └── TOAFontMetrics.swift
├── Calibration/
│   ├── TOAFontCalibrator.swift    — CTFont ölçüm motoru
│   └── TOACalibrationResult.swift
├── Library/
│   └── TOATypographyLibrary.swift — tanımlı stiller
└── SwiftUI/
    ├── TOATextStyle+ViewModifier.swift
    └── Font+TOA.swift
```

---

## Scope Typography Modülü

### Kalibrasyon Arayüzü
İki panel — bağımsız:

**Cap-height paneli:**
- Punto girişi (Apple pt)
- Editable örnek metin (büyük harf)
- 48pt (8×6) iki çizgi arası stage
- 0.5pt hassasiyet offset kontrolü
- CTFont ile canlı ölçüm
- "Cap-height oturdu" onay butonu

**x-height paneli:**
- Aynı yapı, küçük harf odaklı

### Stil Oluşturma Akışı
1. Her iki panel onaylandı
2. Stil tag'leri aktif olur
3. Tag seçilir → line-height + varyantlar tanımlanır
4. "Önizlemeye gönder" → satır tabanlı render + 8pt grid overlay
5. Önizleme onaylanır → stil kaydedilir

### Önizleme Metni
Kullanıcı tanımlar. İki alan:
- Başlık satırı (örn. "TOPOGRAPHIES OF ATTENTION")
- Paragraf metni (örn. "We are type designers...")

### Satır Tabanlı Render
`paragraphStyle.minimumLineHeight = paragraphStyle.maximumLineHeight = lineHeight`
Fontun iç boşlukları dengelenir, 8pt grid çizgileri satır tabanlarına oturur.

---

## Tanımlı Fontlar

*(Scope'ta kalibrasyon yapıldıkça dolar)*

| Font Adı | Ağırlık | Cap→8pt Punto | x→8pt Punto | Tercih |
|----------|---------|---------------|-------------|--------|

---

## Tanımlı Stiller

*(Scope'ta stiller onaylandıkça dolar)*

| Tag | Font | Snap | Punto | Line-height | Varyantlar |
|-----|------|------|-------|-------------|------------|

---

## İlerleme

### Tamamlanan
- Sistem felsefesi ve matematik
- Stil yapısı ve kurallar
- Browser prototipi (v1-v10 arası iterasyon)
- CTFont API yaklaşımı netleşti

### Devam Ediyor
- TOATypographyKit Swift Package
- Scope typography modülü

### Sıradaki Adım
Scope uygulaması temel iskeleti hazır olunca:
1. `TOAFontCalibrator.swift` yaz — CTFont API
2. Scope'ta `CalibrationView.swift` oluştur
3. İlk fontu kalibre et ve kaydet
