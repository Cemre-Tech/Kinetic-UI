---
name: kinetic-ui
description: Mevcut web arayüzlerini marka, içerik ve çalışan işlevleri koruyarak tutarlı, bol animasyonlu tasarımlara dönüştür. “Arayüzü hareketlendir”, “animation-rich UI” ve “scroll animations” taleplerinde; landing page, SaaS, dashboard, portföy, ürün sayfası ve web uygulamalarında kullan. Kod uygulaması veya yalnızca uygulanabilir hareket tasarımı promptu üret.
---

# Kinetic UI

**Design the motion. Transform the experience.**

Hareketi arayüzün bütününe yayılan bir tasarım sistemi olarak ele al. Marka kimliğini, metinleri, bilgi hiyerarşisini, önemli eylemleri ve çalışan işlevleri koru. Gerekirse kompozisyonu ve boşlukları hareketi destekleyecek şekilde geliştir; tek bir renk paleti, tipografi veya görsel stil dayatma.

Varsayılan profil **Expressive**. Kullanıcının açık profil seçimi önceliklidir; erişilebilirlik ve cihaz uyarlamaları her profilde uygulanır. Profil belirtilmemiş uzun süre kullanılan dashboard, tablo ve form arayüzlerinde **Calm** seç ve nedenini kısaca belirt. **Cinematic** profilini yaratıcı portföy, vitrin veya anlatı bağlamında seç.

## Sırayla çalış

1. **İncele.** Proje kurallarını, mevcut tasarımı, görselleri, bileşenleri, tasarım sistemini, manifest ve lockfile sürümlerini oku. Çalışan navigasyon, form, filtre, modal ve veri akışlarını belirle. Kod veya görsel bulunmuyorsa mevcut bilgiyi kullan; incelemiş gibi davranma.
2. **Önceliklendir.** Ana içerik, birincil CTA, tekrarlı işlemler ve durum değişimlerini çıkar. Seçili bölüm varsa kapsamı ona daralt. Tüm sayfa isteniyorsa ana sahneyle birlikte alt bölümleri, etkileşimleri ve durum geçişlerini tasarla; birkaç fade-in tamamlanmış iş sayılmaz.
3. **Planla.** Kısa bir tablo yaz: `bileşen | amaç | tetikleyici | efekt | profil/token | mobil | reduced-motion`. Ana sahneyi ve onu destekleyen hareketleri birlikte seç. Her pattern'i kullanmak zorunlu değildir. Rutin tercihlerde makul varsayımlarla ilerle; eksik bilgi sonucu değiştiriyorsa sor.
4. **Token oluştur.** Tek bir kaynaktan duration, easing, spring, distance ve stagger değerleri tanımla; mevcut token sistemini genişlet. [motion-patterns.md](references/motion-patterns.md) profil tablosunu ve seçtiğin pattern'leri bu aşamada oku.
5. **İstenen çıktıyı üret.** Kod tabanı varsa kapsam içinde uygula. Motor seçimi, SSR, lifecycle ve örnekler için [implementation-guide.md](references/implementation-guide.md) dosyasını oku. Yalnızca tasarım promptu/spec istenmişse kod, bağımlılık kurulumu veya repo değişikliği yapma; aynı planı zamanlama, trigger, breakpoint, son durum ve erişilebilirlik alternatifleriyle uygulanabilir spesifikasyona dönüştür.
6. **Kontrol et ve teslim et.** Kod uygulamasında [accessibility-performance.md](references/accessibility-performance.md) kontrol akışını çalıştır. Prompt görevinde aynı dosyanın tasarım kabul ölçütlerini kullan. Projenin mevcut test/lint/build komutlarını gerektiği kadar çalıştır. Yapılan kontrolleri, başarısızlıkları ve çalıştırılamayan kontrolleri ayrı belirt; ölçülmemiş FPS veya çalıştırılmamış tarayıcı kontrolünü başarılı gösterme.

## Hareket ilkeleri

- Expressive/Cinematic sayfada belirgin ana koreografi, scroll boyunca ritim, kullanıcı etkileşimleri ve mevcut durum değişimlerini birlikte ele al. Calm profilde görev tamamlamayı ve görsel sürekliliği öne çıkar.
- CTA kullanımını, içerik okunmasını ve durum güncellemelerini animasyon bitişine bağlama. Aynı anda her şeyi hareket ettirme; ana sahneye alan aç.
- Basit etkileşimlerde CSS/WAAPI; React/Next.js'te mevcut uyumlu Motion altyapısı; karmaşık timeline veya pin/scrub için faydası gerekçelendirilmiş GSAP/ScrollTrigger kullan. Mevcut `framer-motion` projesini sırf bu skill için taşımaya çalışma.
- Aynı elementin aynı özelliğini iki animasyon motoruna verme. Scroll hareketiyle hover hareketi çakışıyorsa ayrı wrapper'lar veya tek motor kullan.
- `prefers-reduced-motion` ana tasarım girdisidir. Yoğun transform, parallax, pin ve loop yerine görünür statik içerik veya kısa opacity geçişi ver. Hover'ın klavye focus karşılığını ve görünür focus ring'i koru.
- Fine pointer/hover gerektiren magnetic, takip ve tilt efektlerini uygun cihazlarla sınırla. Mobilde doğal scroll, erişilebilir CTA ve okunabilir içerik sağla.
- Temel içerik SSR'de ve animasyon başlamadığında görünür olsun. Listener, observer, timer, RAF ve timeline kaynaklarını temizle; route geçişi, remount ve kullanıcı tercihi değişimini ele al.
- Öncelikle transform/opacity kullan; büyük blur/filter ve sürekli hareketleri ölçerek değerlendir. Viewport dışı ve arka plandaki loop'ları durdur. 60 FPS bir hedeftir, ölçüm yapılmadan garanti değildir.

## Çıktı sözleşmesi

**Kod görevi:** seçilen profil ve kısa hareket planı; uygulanan değişiklikler; yeni bağımlılık varsa somut gerekçe; test sonuçları ve kalan sınırlar.

**Prompt görevi:** marka ve kapsam kısıtları; profil/token değerleri; ana sahne ve destek hareketleri; trigger ve giriş/çıkış zamanlamaları; mobil/reduced-motion karşılıkları; uygulanacak kabul ölçütleri. Uygulama veya test yapıldığını iddia etme.

Kullanıcının dilinde yanıt ver. Skill'i Codex'te `$kinetic-ui`, Claude Code'da `/kinetic-ui` ile çağır; başka araçlarda o aracın skill çağırma biçimini kullan. Dosya yapısı ve görevin kapsamı değişmez.

## Davranış kabul örnekleri

| İstek | Beklenen seçim ve davranış |
| --- | --- |
| “Landing page'i bol animasyonlu yap.” | Expressive; hero dizisi, alt bölüm ritmi, CTA/focus ve varsa accordion/modal durumları; marka ve işlevler korunur. |
| “Sipariş dashboard'unu hareketlendir.” | Profil belirtilmemişse Calm; filtre/layout sürekliliği, seçili durum ve drawer; form/tabloda uzun stagger, pin veya dekoratif loop yok. |
| “Portföyü cinematic yap.” | Cinematic; bir ana sahne, kontrollü mask/parallax, uygunsa pin; mobil/reduced-motion doğrusal anlatı; okunabilir başlıklar ve doğal scroll. |
| “Yalnızca tasarım promptu yaz; sadece hero.” | Kapsam hero; plan ve uygulanabilir spesifikasyon; repo değişmez, alt sayfa animasyonları eklenmez, test başarısı iddia edilmez. |

Örnek çağrı: **“$kinetic-ui Bu arayüzü expressive yoğunlukta, bol animasyonlu bir tasarıma dönüştür. Marka kimliğini koru; açılış, scroll, mikro etkileşim ve durum geçişlerini birlikte ele al.”**
