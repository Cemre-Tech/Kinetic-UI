# Kinetic UI

**Design the motion. Transform the experience.**

[English](README.md) · Türkçe

Kinetic UI, mevcut web sitelerine ve uygulamalara hareket eklemek için kullanılan bir skill paketidir. Codex veya Claude Code ile açılış animasyonlarını, scroll efektlerini, etkileşimleri ve durum geçişlerini planlayıp uygulamanı sağlar. Marka kimliği, içerik ve çalışan özellikler korunur.

Landing page, ürün sayfası, portföy, dashboard ve SaaS arayüzlerinde kullanılabilir. İstersen bütün bir sayfayı, istersen tek bir bileşeni seçebilirsin.

## Hareket profilini seç

| Profil | Nerede kullanılır? | Ne sağlar? |
| --- | --- | --- |
| **Calm** | Dashboard, tablo ve form | Uzun süreli kullanımda dikkat dağıtmayan kısa etkileşimler ve anlaşılır durum değişimleri. |
| **Expressive** | Landing page ve ürün sayfası | Sıralı hero açılışı, bölüm geçişleri ve belirgin etkileşimler. Varsayılan profil. |
| **Cinematic** | Yaratıcı portföy ve görsel hikâye | Daha belirgin zamanlama, maskeli açılışlar ve içeriğe uygun scroll anlatımı. |

Profiller ortak süre, easing, spring, mesafe ve stagger değerleri kullanır. Hareket mevcut görsel stile uyarlanır; belirli bir renk paleti veya tipografi dayatılmaz.

## Kurulum

1. Depoyu **Code → Download ZIP** ile indir ve arşivi çıkar.
2. İçinde `kinetic-ui/` bulunan klasörde bir terminal aç.
3. Referansları da içeren **`kinetic-ui` klasörünün tamamını** kullandığın aracın skill dizinine kopyala.

| Araç | Tüm projelerinde | Yalnızca bir projede | Kullanım |
| --- | --- | --- | --- |
| Codex | `~/.agents/skills/kinetic-ui/` | `<proje>/.agents/skills/kinetic-ui/` | `$kinetic-ui` |
| Claude Code | `~/.claude/skills/kinetic-ui/` | `<proje>/.claude/skills/kinetic-ui/` | `/kinetic-ui` |

Kişisel kurulum için aşağıdaki komutlardan uygun olanı seç. Skill zaten kuruluysa güncellemeden önce mevcut kopyayı kontrol et.

<details>
<summary>Windows — PowerShell</summary>

**Codex**

```powershell
New-Item -ItemType Directory -Force "$HOME/.agents/skills" | Out-Null
Copy-Item -LiteralPath ./kinetic-ui -Destination "$HOME/.agents/skills/kinetic-ui" -Recurse
```

**Claude Code**

```powershell
New-Item -ItemType Directory -Force "$HOME/.claude/skills" | Out-Null
Copy-Item -LiteralPath ./kinetic-ui -Destination "$HOME/.claude/skills/kinetic-ui" -Recurse
```

</details>

<details>
<summary>macOS / Linux — terminal</summary>

**Codex**

```bash
mkdir -p ~/.agents/skills
cp -R ./kinetic-ui ~/.agents/skills/
```

**Claude Code**

```bash
mkdir -p ~/.claude/skills
cp -R ./kinetic-ui ~/.claude/skills/
```

</details>

Kopyaladıktan sonra geliştirmek istediğin projeyi araçta aç ve skill'i çağır. Listede görünmüyorsa oturumu yeniden başlat; `SKILL.md` dosyasının doğrudan `kinetic-ui/` içinde olduğunu kontrol et.

Yalnızca bir projede kullanmak için klasörü tablodaki proje dizinine kopyala. Aynı skill'in iki kez görünmemesi için tek bir kurulum kapsamı seç.

[Agent Skills](https://agentskills.io) desteği olan başka araçlarda da talimat paketini kullanabilirsin. Kurulum yolu ve çağırma biçimi araca göre değişir. `agents/openai.yaml` Codex'in arayüz bilgilerini taşır; ortak talimatlar `SKILL.md` ve `references/` içindedir.

Resmî kurulum belgeleri: [Codex skills](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills) · [Claude Code skills](https://code.claude.com/docs/en/skills).

## Kullanım

Geliştirmek istediğin projeyi aç; kapsamı ve hareket profilini belirt. Skill, isteğini yazdığın dilde yanıt verir.

**Codex**

```text
$kinetic-ui Bu landing page'i expressive yoğunlukta hareketlendir. Marka kimliğini,
içeriği ve çalışan özellikleri koru. Hero açılışını, scroll efektlerini,
etkileşimleri ve durum geçişlerini birlikte ele al.
```

**Claude Code**

```text
/kinetic-ui Bu landing page'i expressive yoğunlukta hareketlendir. Marka kimliğini,
içeriği ve çalışan özellikleri koru. Hero açılışını, scroll efektlerini,
etkileşimleri ve durum geçişlerini birlikte ele al.
```

İsteği daha dar tutabilirsin:

- **Dashboard:** “Sipariş tablosu ve düzenleme drawer'ında calm hareket kullan. Filtreleri, klavye kullanımını ve form verisini koru.”
- **Portföy:** “Bu portföye cinematic hareket ekle. Tek bir ana görsel sahne kullan; mobilde doğal okuma akışı kalsın.”
- **Tek bileşen:** “Yalnızca fiyatlandırma kartlarını hareketlendir. Sayfanın diğer bölümlerini değiştirme.”
- **Yalnızca tasarım:** “Hero için bir hareket spesifikasyonu yaz. Süreleri, tetikleyicileri ve mobil alternatifleri belirt. Kodu değiştirme.”

Bu isteklerin başına Codex'te `$kinetic-ui`, Claude Code'da `/kinetic-ui` ekle.

## Nasıl çalışır?

1. Mevcut tasarımı, bileşenleri, bağımlılıkları ve proje kurallarını inceler.
2. Ana içeriği, önemli eylemleri ve faydalı animasyon fırsatlarını belirler.
3. Amaç, tetikleyici, efekt ve cihaz alternatiflerini içeren kısa bir hareket planı yazar.
4. Ortak hareket değerlerini tanımlayıp istenen kapsamda uygular.
5. Proje ortamının izin verdiği ölçüde responsive davranışı, erişilebilirliği, kaynak temizliğini ve performansı kontrol eder.

Basit etkileşimlerde CSS veya Web Animations API kullanılır. React/Next.js projelerinde mevcut uyumlu Motion altyapısı değerlendirilir. Daha karmaşık timeline ve scroll sahneleri için GSAP/ScrollTrigger seçilebilir. Yeni bağımlılıklar somut faydayla gerekçelendirilir; aynı animasyon özelliğini tek motor yönetir.

Mobilde doğal kaydırma ve kullanılabilir kontroller korunur. Reduced-motion tercihi yoğun hareketi statik içerik veya kısa geçişlerle değiştirir. Klavye focus'u, okunabilirlik ve çalışan eylemler her aşamada önceliklidir.

## Dosyalar

```text
kinetic-ui/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── motion-patterns.md
    ├── implementation-guide.md
    └── accessibility-performance.md
```

| Dosya | İçeriği |
| --- | --- |
| [SKILL.md](kinetic-ui/SKILL.md) | Ana iş akışı, kapsam ve profil seçimi. |
| [motion-patterns.md](kinetic-ui/references/motion-patterns.md) | Profil değerleri, animasyon kalıpları, mobil ve reduced-motion alternatifleri. |
| [implementation-guide.md](kinetic-ui/references/implementation-guide.md) | Motor seçimi, kod örnekleri, SSR ve kaynak temizliği. |
| [accessibility-performance.md](kinetic-ui/references/accessibility-performance.md) | Erişilebilirlik kontrolleri, cihaz uyarlaması ve performans doğrulaması. |
| [openai.yaml](kinetic-ui/agents/openai.yaml) | Codex için görünen ad, açıklama ve başlangıç promptu. |

Skill talimatları ve teknik referanslar Türkçe yazılmıştır. Türkçe veya İngilizce isteklerle kullanabilirsin.

## Doğrulama

Paket yapısı, YAML bilgileri ve yerel referans bağlantıları kontrol edildi. Yedi izole JavaScript testi; token dönüşümlerini, reveal davranışını, reduced-motion değişimini, mobil mesafeleri, kaynak temizliğini, remount'u ve pin koşullarını kapsar. Landing page, dashboard, cinematic portföy ve yalnızca tasarım isteyen görevler plan/spesifikasyon düzeyinde de değerlendirildi.

Tarayıcı görünümü, gerçek cihaz davranışı ve framework entegrasyonu hedef projede ayrıca kontrol edilmelidir. Akıcı hareket hedeflenir; kare hızı kullanıcıların kullandığı cihazlarda ölçülür.
