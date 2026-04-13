# Topographies of Attention — Master Chat

Bu chat sadece koordinasyon içindir.
Kod yazılmaz. Tasarım detayına girilmez.
Sadece ilerleme takibi ve chat'ler arası bağlantı yönetimi yapılır.

---

## MASTER CHAT NASIL KULLANILIR

### Her oturum başında yap:
1. Bu dosyayı Claude'a yapıştır
2. "Master chat'i güncelle, bugün X konusunu çalıştım" de
3. Claude bu dosyayı günceller, sen kaydet

### Her oturum sonunda yap:
1. Aktif chat'ten çıkmadan önce o chat'in .md dosyasını güncelle
2. Master chat'e git, ilerleme notunu bırak
3. Güncellenmiş MASTER.md'yi kaydet

### Yeni bir konu başlatırken:
1. İlgili .md dosyasını aç (SCOPE.md, TYPOGRAPHY.md vb.)
2. Yeni chat'e şöyle başla:
   ```
   Merhaba. Aşağıdaki iki dosyayı oku:
   1. CLAUDE.md [yapıştır]
   2. [KONU].md [yapıştır]
   Bugünkü konu: [ne yapacağını yaz]
   ```
3. Oturum sonunda ilgili .md'yi güncelle, MASTER.md'ye not düş

---

## Aktif Projeler

| Proje | .md Dosyası | Durum | Son Güncelleme |
|-------|-------------|-------|----------------|
| Color System | CLAUDE.md | Hazır | Bu oturum |
| Grid System | CLAUDE.md | Hazır | Bu oturum |
| Typography | TYPOGRAPHY.md | Geliştiriliyor | Bu oturum |
| Scope App | SCOPE.md | Başlangıç | Bu oturum |
| Data System | DATA.md | Konsept | Bu oturum |

---

## Chat Envanteri

### Chat 1 — Bu oturum (Temel Bağlam)
**Konu:** Color, Grid, Typography temelleri + Scope konsepti + Data sistemi konsepti
**Çıktılar:**
- CLAUDE.md tamamlandı
- SCOPE.md hazırlandı
- TYPOGRAPHY.md hazırlandı
- DATA.md hazırlandı
- MASTER.md hazırlandı
- Typography kalibrasyon aracı prototiplendi (browser widget)
**Kalan:** Scope uygulaması Xcode'da başlatılmadı

### Chat 2 — Scope (Başlatılmadı)
**Konu:** macOS Scope utility uygulaması
**Başlangıç dosyası:** SCOPE.md + CLAUDE.md
**Önkoşul:** Xcode açık, yeni macOS projesi hazır

### Chat 3 — Typography (Başlatılmadı)
**Konu:** TOATypographyKit Swift Package + Scope typography modülü
**Başlangıç dosyası:** TYPOGRAPHY.md + CLAUDE.md
**Önkoşul:** Scope uygulaması temel iskeleti hazır olmalı

### Chat 4 — Data (Başlatılmadı)
**Konu:** t-of-a data sistemi, RSS + filtre arayüzü
**Başlangıç dosyası:** DATA.md + CLAUDE.md
**Önkoşul:** Scope uygulaması çalışır durumda olmalı

---

## Alınan Kararlar

### Mimari Kararlar
- Tüm sistemler Swift Package olarak dağıtılır
- Platform: iOS 17+, macOS 14+
- UI: SwiftUI
- Scope: macOS utility, document-based app kararı verilmedi

### Tasarım Kararları
- 8 pt minimum birim (Apple pt)
- Aktif alan: 368 pt sabit
- Renkler değişmez, roller yeniden atanır
- Typography: cap-height veya x-height temelli kalibrasyon
- Line-height her zaman 8 pt'nin katı
- Bold/italic aynı puntoyu kullanır, ayrı stil değil

### Açık Kararlar (Henüz Verilmedi)
- [ ] Scope document-based mi, global state mi?
- [ ] Typography paketi için font ailesi seçimi
- [ ] Icons sistemi mimarisi
- [ ] Buttons sistemi mimarisi
- [ ] TOAGridKit displayScale düzeltmesi
- [ ] TOAGridOverlay yükseklik düzeltmesi

---

## Bağımlılık Haritası

```
TOFAColorSystem (hazır)
    └── Scope Color Modülü
    └── Tüm iOS Uygulamaları

TOAGridKit (hazır)
    └── Scope Grid Modülü
    └── Tüm iOS Uygulamaları

TOATypographyKit (geliştiriliyor)
    └── Scope Typography Modülü
    └── Tüm iOS Uygulamaları

t-of-a data (konsept)
    └── Scope Data Modülü
    └── Scope önizlemelerine veri sağlar
```

---

## İlerleme Notları

**[Bu oturum]**
- Color ve Grid sistemleri kod seviyesinde incelendi, çalışır durumda onaylandı
- Typography kalibrasyon aracı browser'da prototiplendi, Xcode'a taşınacak
- Scope uygulaması ve Data sistemi konsept olarak tanımlandı
- Tüm .md dosyaları hazırlandı
- Bir sonraki adım: Scope Xcode projesi açılacak
