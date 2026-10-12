# Erişilebilirlik, cihaz uyarlaması ve performans

Kod tesliminden önce bu akışı uygula. Prompt/spec görevinde tasarım kabul ölçütlerini kullan; tarayıcı veya performans testini yapılmış gösterme. Buradaki hareket bütçeleri tasarım başlangıcıdır, ölçülmüş performans garantisi değildir.

## Reduced-motion ana akışı

OS tercihini CSS'te `prefers-reduced-motion`, JS'te matchMedia veya mevcut motor hook'u ile izle; çalışma sırasında değişimini de ele al. SSR ve tercih bilinmiyorken temel içerik görünür ve kullanılabilir olsun.

| Tam hareket | Reduced-motion karşılığı |
| --- | --- |
| Hero/satır/mask reveal | Tam metin ve görsel statik; gecikme/stagger yok |
| Scroll reveal | Statik veya 100–150 ms opacity; tekrar gizleme yok |
| Parallax/tilt/magnetic/pointer-follow | Kapalı; doğal yerleşim ve native cursor |
| Pin/scrub anlatı | Aynı içerik kaynak sırasıyla normal belge akışında |
| Mesh/floating/dekoratif loop | Sabit kompozisyon; devam eden loop iptal edilir |
| Layout/list position tween | Anlık yeni layout, metin/ikon ile durum açıklaması |
| Modal/drawer/tab | Anlık veya kısa opacity; semantik state ve focus yönetimi aynı |
| Press/selection | Ring, underline, renk ve ikon; transform yok |

Reduced-motion override'ında yalnızca duration'ı 0.01 ms yapmak yeterli değildir: pin, auto-scroll, observer tarafından hidden bırakılan stiller ve JS loop'ları ayrıca temizlenir. Tüm sayfanın `transform` değerini global `!important` ile silme; mevcut yerleşimleri bozabilir. Kapsamlı seçiciler ve motorun son durumunu kullan.

## Klavye, semantik ve okunabilirlik

- Buton/link semantiği, tab sırası, gerçek href, disabled davranışı, form submit ve seçili durum korunur. Hover ile açığa çıkan bilgi focus/tap ile de erişilir.
- Focus-visible ring sürekli görünür; mask/overflow, sticky header veya drawer tarafından kapanmaz. Kart nested control'leri kullanılabilir kalır.
- Dialog primitive'inin erişilebilir adı, focus giriş hedefi, Tab trap, Escape, arka plan inertliği, kapanışta focus restore ve scroll unlock davranışını kontrol et. Exit DOM'u varsa focus/tıklama erişimini kapat; görünmez fakat aktif kontroller bırakma.
- Accordion ve tab rollerini/key davranışlarını doğrula; animasyon bu semantiği değiştirmez. Çıkan panelde focus varsa güncellemeden önce mantıklı hedef belirle.
- Text reveal başlığı tek kez okutur. Görsel kopya `aria-hidden` olabilir; canonical metin accessible tree'de kalır. Maske aksan/descender ve metin seçimini kesmez.
- Kontrastı hareketli arka planın bütün durumlarında kontrol et; durumu yalnızca renkle veya hareketle anlatma. Animasyon çalışmadan da gerekli mesaj/metin/ikon bulunur.
- Otomatik başlayıp 5 saniyeden uzun süren, diğer içerikle paralel hareket için [WCAG Pause, Stop, Hide](https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide.html) koşullarını değerlendir; uygulanıyorsa ulaşılabilir pause/stop/hide kontrolü sağla. Yalnızca OS tercihi bu kontrolün yerine geçmez. Flash/strobe efektleri ekleme.

## Mobil ve pointer uyarlaması

Motion yoğunluğunu yalnızca ekran genişliğiyle belirleme. Pointer efektleri için hover/fine-pointer sorgusu ve actual pointer type birlikte değerlendirilir; hybrid cihazda touch event'ine mouse davranışı uygulama.

Mobilde normal scroll ve pinch zoom korunur. Wheel/touch event'lerini preventDefault ile yönlendirme, scroll hijacking veya CTA'yı kovalatan magnetic hit area oluşturma. Başlıklar sarıldığında maskeler, sticky yüksekliği ve görsel taşması yeniden değerlendirilir. CTA'lar animasyonun ortasında da kullanılabilir; dokunma hedefleri mevcut erişilebilir boyutunu korur.

## Performans kararları

Önce transform/opacity. Compositor kullanımı her zaman ucuzluk garantisi değildir; çok büyük layer, blur ve filtre GPU/bellek maliyetini artırabilir. `will-change` yalnızca ihtiyaç süresince ve sınırlı öğede; tüm karta/section'a sürekli verme.

Scroll/pointer olayında React state'i her karede güncelleme. Motion value, WAAPI veya birleştirilmiş RAF kullan. Ölçümleri yazmalardan önce grupla; read→write→read layout thrash yaratma. Büyük listelerde mevcut virtualization'ı koru, her node'a observer ve tween açma.

Sürekli hareket için tek birleşik koşul kullan:

```text
run = inViewport && documentVisible && !reducedMotion && !userPaused
```

Koşul false iken loop pause/cancel olur. Kullanıcı pause ettikten sonra viewport'a geri gelmek veya sekmeye dönmek loop'u kendiliğinden yeniden başlatmaz. Cleanup bütün observer/listener/RAF/timeline kaynaklarını kaldırır. Animasyon bütçesi aşılırsa önce katman sayısını, blur'u, parallax'ı ve uzun stagger'ı azalt; işlemi/CTA'yı yavaşlatma.

60 FPS hedefinde kare bütçesi yaklaşık 16.7 ms'dir. Hedef cihazda frame pacing, long task, layout/paint ve etkileşim gecikmesini ölç; ekranın refresh rate'i farklı olabilir. Ölçmeden “60 FPS garantili” veya “performans sorunu yok” yazma.

## Kod uygulaması kontrol akışı

1. **Başlangıç:** Projenin mevcut lint/test/build komutlarını manifestten bul; ilgili kontrolleri çalıştır. İlgisiz mevcut hataları ayrı kaydet. Animasyon için yeni bir test framework'ü eklemek otomatik gereklilik değildir.
2. **Temel içerik:** JS kapalı/yavaş/hata veren durumda kritik metin, CTA ve doğal belge akışı görünür. SSR HTML ve ilk client render tutarlı; console'da hydration uyarısı yok.
3. **Profil/kapsam:** Planın ana ve destek hareketleri uygulanmış; marka, metin, bilgi hiyerarşisi, URL'ler, form ve veri akışları korunmuş. Seçili bölüm dışına değişiklik taşmamış.
4. **Cihaz:** En az dar mobil ve desktop; resize/orientation; touch ve klavye; font/uzun başlık; doğal scroll, overflow ve CTA erişimi kontrol edilir. Gerçek cihaz yoksa emülasyon olduğunu belirt.
5. **Tercih:** Reduced-motion açıkken ilk yükleme ve sayfa açıkken tercih değişimi. Transform/parallax/pin/loop kapanır; içerik son görünür duruma döner; state/focus davranışı korunur.
6. **Etkileşim:** Tab/Shift+Tab/Enter/Space; hover/focus; modal Escape ve focus restore; accordion/tab; filtre/list değişimi; hızlı ardışık input. İnteraktif kontroller exit süresince görünmez aktif kalmaz.
7. **Lifecycle:** Route ileri/geri, remount ve React Strict Mode. Duplicate event, stale observer, pin spacer, dangling timer/RAF veya cleanup sonrası animation callback hatası yok.
8. **Performans:** Representative scroll + etkileşim trace'i kaydet. Loop offscreen/arka planda durur. Kullanılan cihaz, viewport, profil, trace ve gözlenen sonucu raporla; trace alınamadıysa ölçülmedi olarak yaz.

Her madde için `yapıldı + kanıt`, `başarısız + gerçek hata` veya `çalıştırılmadı + neden` belirt. Build geçmesi görsel/erişilebilirlik performans doğrulaması yerine geçmez. Gerekli ortam yoksa yapılabilen statik kontrolü tamamla, geri kalanını açıkça ayır.

## Prompt/spec kabul ölçütleri

Kod ortamı yoksa planın uygulanabilirliğini incele: süre/birimler, easing, stagger bütçesi, trigger, mobile breakpoint, nihai görünür durum, reduced-motion karşılığı, motor sahipliği ve cleanup beklentisi açık mı? Markayı ve kapsamı koruyor mu? Normal Expressive sayfa yalnızca hero fade'iyle bırakılmış mı? Bunlar spesifikasyon kontrolleridir; runtime testi değildir.

## Dört senaryoyla doğrulama

Skill oluşturulurken veya değişirken aşağıdaki gerçekçi isteklere karşı davranışını değerlendirmek için geçici ayrı fixture'lar veya yalnızca plan/spec çıktıları kullan. Sonuçları kanıtla raporla; uygulama ortamı olmayan senaryoda prompt değerlendirmesini browser başarısı olarak yazma.

| Senaryo | Girdi | Kabul ölçütü |
| --- | --- | --- |
| Landing | Marka renkleri ve hero/özellik/FAQ yapısı; “bol animasyonlu yap” | Expressive, ana sahne + alt bölümler + etkileşim + mevcut state; zorunlu GSAP yok; CTA erişilebilir |
| Dashboard | Filtreli sipariş tablosu, düzenleme drawer'ı; profil yok | Calm, kısa feedback/layout, stable identity/input state; pin/loop/uzun stagger yok |
| Cinematic portföy | İşler grid'i ve 3 adımlı hikâye; “cinematic” | Tek baskın sahne, uygun optional pin; mobil/reduced doğrusal akış; cleanup ve pause tasarımı |
| Prompt-only | “Sadece hero için tasarım promptu, kodu değiştirme” | Yalnızca hero spec; token/trigger/alternatifler; dosya/paket değişikliği ve test iddiası yok |

Bir başarısız davranış bulunursa ilgili kuralı dar ve somut düzelt; her gözlemi bütün projelere zorunlu yeni pattern olarak ekleme.

## Kaynaklar

- [Motion erişilebilirlik rehberi](https://motion.dev/docs/react-accessibility)
- [MDN prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@media/prefers-reduced-motion)
- [W3C WCAG 2.2.2](https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide.html)
