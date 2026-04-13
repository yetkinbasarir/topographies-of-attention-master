# Topographies of Attention — Master

Bu repo, **Topographies of Attention** tasarım sisteminin koordinasyon
ve referans merkezi olarak görev yapar.

---

## İçerik

| Dosya | Açıklama |
|-------|----------|
| `CLAUDE.md` | Ana kimlik referansı — her Claude chat'ine başlangıç dosyası |
| `MASTER.md` | Tüm projelerin koordinasyon ve ilerleme takibi |
| `SCOPE.md` | Scope macOS utility uygulaması brifing ve ilerleme |
| `TYPOGRAPHY.md` | Typography sistemi ve kalibrasyon aracı brifing |
| `DATA.md` | t-of-a data sistemi (RSS + filtre) brifing |

---

## İlgili Repolar

| Repo | Açıklama |
|------|----------|
| [topographies-of-attention-color-system](https://github.com/yetkinbasarir/topographies-of-attention-color-system) | TOFAColorSystem Swift Package |
| [topographies-of-attention-grid-system](https://github.com/yetkinbasarir/topographies-of-attention-grid-system) | TOAGridKit Swift Package |

---

## Nasıl Kullanılır

Her yeni Claude oturumuna `CLAUDE.md` ile başla.
Konuya özel oturumlarda ilgili `.md` dosyasını da ekle.
Oturum sonunda `MASTER.md`'yi güncelle.

Detaylı kullanım talimatları her `.md` dosyasının başında yer alır.

---

## Sistem Katmanları

```
1. Color       → TOFAColorSystem       (hazır)
2. Grid        → TOAGridKit            (hazır)
3. Typography  → TOATypographyKit      (geliştiriliyor)
4. Icons       → —                     (planlanıyor)
5. Buttons     → —                     (planlanıyor)
6. Animation   → —                     (planlanıyor)
```

---

## Scope Utility

Tüm katmanları test eden macOS uygulaması.
Bkz. `SCOPE.md`

## t-of-a Data

Tasarım sistemini canlı RSS verisiyle besleyen modül.
Bkz. `DATA.md`
