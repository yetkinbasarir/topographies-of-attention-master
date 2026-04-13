# Scope — Tasarım Utility Uygulaması

## BU CHAT'İ NASIL KULLANIRSIN

### Oturum başında:
1. CLAUDE.md dosyasını yapıştır (ana kimlik bağlamı için)
2. Bu SCOPE.md dosyasını yapıştır
3. Şöyle başla:
   ```
   Merhaba. Aşağıdaki iki dosyayı oku:
   1. CLAUDE.md [yapıştır]
   2. SCOPE.md [yapıştır]
   Bugün Scope uygulamasında [ne yapacağını yaz] konusunu çalışacağız.
   ```

### Oturum sonunda:
1. Bu dosyayı güncelle — ne tamamlandı, ne kaldı
2. MASTER.md'ye kısa bir not düş

---

## Uygulama Kimliği

**Ad:** Scope
**Platform:** macOS 14+
**Teknoloji:** SwiftUI
**Amaç:** Topographies of Attention ekosistemi için tasarım kararlarını
test eden, önizleyen ve .md çıktısı üreten utility uygulaması

**Felsefe:** Bir uygulama geliştirmeye başlamadan önce tüm tasarım kararları
Scope'ta test edilir ve onaylanır. Scope çıktısı (.md dosyaları) uygulamanın
başlangıç noktasıdır.

---

## Modül Yapısı

```
Scope
├── Color Modülü        → TOFAColorSystem görselleştirme
├── Grid Modülü         → TOAGridKit önizleme
├── Typography Modülü   → Font kalibrasyon + stil tanımlama
└── Data Modülü         → RSS + filtre + önizleme verisi
```

Her modül bağımsız çalışır ama birbirleriyle konuşabilir.
Örneğin: Data modülünden çekilen RSS verisi Typography modülünde
gerçek veriyle önizleme yapılmasını sağlar.

---

## Teknik Kararlar

### Proje Yapısı
```
Scope/
├── ScopeApp.swift
├── ContentView.swift          — Ana navigasyon
├── Modules/
│   ├── Color/
│   │   ├── ColorModuleView.swift
│   │   └── ColorModuleViewModel.swift
│   ├── Grid/
│   │   ├── GridModuleView.swift
│   │   └── GridModuleViewModel.swift
│   ├── Typography/
│   │   ├── TypographyModuleView.swift
│   │   ├── CalibrationView.swift
│   │   └── StyleBuilderView.swift
│   └── Data/
│       ├── DataModuleView.swift
│       └── RSSFilterView.swift
├── Shared/
│   ├── Models/
│   └── Components/
└── Package Dependencies:
    ├── TOFAColorSystem
    ├── TOAGridKit
    └── TOATypographyKit (hazır olunca)
```

### Navigasyon
Sol sidebar: modül listesi
Sağ alan: seçili modülün içeriği
macOS native sidebar pattern — `NavigationSplitView`

### State Yönetimi
Her modül kendi `ViewModel`'ını taşır.
Modüller arası veri paylaşımı için merkezi bir `ScopeStore` — `@Observable`

### Çıktı Sistemi
Her modül `.md` export edebilir.
Çıktılar `~/Documents/Scope/` altına kaydedilir.

---

## Color Modülü

### Ne Gösterecek
- 26 temanın tamamı — shell ve aylık
- Her tema için 5 rol rengi (background, primaryText, secondaryText, line, attention)
- Day/Night geçişi toggle
- Mevcut ay ve faza göre otomatik aktif tema

### Etkileşim
- Tema seçimi → renk rolleri güncellenir
- Canlı önizleme: örnek bir kart/ekran iskeletine renkleri uygula
- Export: seçili temanın renk değerlerini .md olarak kaydet

### Bağımlılık
```swift
import TOFAColorSystem
```

---

## Grid Modülü

### Ne Gösterecek
- iPhone modellerine göre grid önizlemesi (375, 390, 393, 430 pt genişlikler)
- Mode 4 ve Mode 6 karşılaştırması
- Tüm division'lar görsel olarak
- Safe area simülasyonu
- Margin compensation hesabı

### Etkileşim
- Model seçimi → grid yeniden hesaplanır
- Division seçimi → aktif bölünme vurgulanır
- Debug overlay açma/kapama

### Bağımlılık
```swift
import TOAGridKit
```

---

## Typography Modülü

### Ne Gösterecek
- Font kalibrasyon aracı (cap-height + x-height, iki ayrı panel)
- 8×6 = 48 pt grid üzerinde font hizalama
- Stil oluşturma (caption, bodytext, title, subtitle, display)
- Varyant yönetimi (regular, bold, italic, boldItalic)
- Satır tabanlı önizleme — grid hizalamalı
- TOA metni ile önizleme

### Kritik Teknik Detay
Font ölçümü için `CTFont` API kullanılacak — `UIFont`/`NSFont` değil.
`CTFontGetCapHeight()` ve `CTFontGetXHeight()` gerçek metrik değerleri verir.
Bu değerler Apple pt cinsindendir, doğrudan SwiftUI `Font.custom()` ile kullanılabilir.

```swift
let ctFont = CTFontCreateWithName(fontName as CFString, fontSize, nil)
let capHeight = CTFontGetCapHeight(ctFont)  // Apple pt
let xHeight = CTFontGetXHeight(ctFont)      // Apple pt
```

### Çıktı
- `TOATypographyKit` Swift Package için kaynak kod
- TYPOGRAPHY.md güncelleme

### Bağımlılık
```swift
import CoreText  // ölçüm için
import TOATypographyKit  // hazır olunca
```

---

## Data Modülü

*(Detaylar DATA.md'de — bu modül sonraki aşamada)*

---

## Geliştirme Sırası

1. **Xcode projesi aç** — macOS App, SwiftUI, minimum macOS 14
2. **Package bağımlılıkları ekle** — TOFAColorSystem, TOAGridKit
3. **Ana navigasyon iskeleti** — NavigationSplitView, sidebar, modül listesi
4. **Color modülü** — en az bağımlılığı olan, iyi başlangıç noktası
5. **Grid modülü**
6. **Typography modülü** — en karmaşık, CTFont API gerektiriyor
7. **Data modülü** — en sona

---

## Açık Kararlar

- [ ] Document-based app mı, global state mi?
- [ ] Çıktı formatı: sadece .md mi, yoksa .json da mı?
- [ ] Uygulama ikonu ve kimliği
- [ ] Scope kendi içinde bir tema seçici olacak mı? (Scope da TOA kimliğini mi taşımalı?)

---

## İlerleme

### Tamamlanan
- Konsept ve mimari tasarım
- Modül yapısı kararları

### Devam Ediyor
- (henüz Xcode projesi açılmadı)

### Sıradaki Adım
Xcode'da yeni macOS projesi aç:
- Product Name: Scope
- Team: kişisel
- Bundle ID: com.toa.scope
- Interface: SwiftUI
- Minimum macOS: 14.0
