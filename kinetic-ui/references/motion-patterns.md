# Hareket profilleri ve pattern seçimi

Plan/token üretirken profil tablosunu; efekt seçerken ilgili pattern'i oku. Sayıları başlangıç değeri kabul et, hedef markaya ve içerik yoğunluğuna göre ayarla. Birimler süre için **ms**, mesafe için **CSS px**; satır reveal'da `%` satır yüksekliğidir. Motorların saniye/ms farkını uygulama rehberindeki adapter ile dönüştür.

## Üç profil

| Boyut | Calm | Expressive (varsayılan) | Cinematic |
| --- | --- | --- | --- |
| Kullanım | Dashboard, tablo, form, uzun oturum | Landing, SaaS pazarlama, ürün | Portföy, vitrin, anlatı |
| Feedback / geçiş / sahne | 120 / 220 / 320 ms | 160 / 400 / 700 ms | 180 / 550 / 1000 ms |
| Mesafe: küçük / ana / üst sınır | 4 / 8 / 12 px | 8 / 24 / 40 px | 12 / 40 / 72 px |
| Enter easing (cubic-bezier) | (0.2, 0, 0, 1) | (0.22, 1, 0.36, 1) | (0.16, 1, 0.3, 1) |
| Exit easing | (0.4, 0, 1, 1) | (0.4, 0, 1, 1) | (0.4, 0, 1, 1) |
| Stagger / toplam gecikme bütçesi | 25 / 100 ms | 65 / 260 ms | 100 / 400 ms |
| Spring: stiffness / damping / mass | 420 / 40 / 1 | 260 / 28 / 1 | 170 / 26 / 1 |
| Sürekli hareket | Varsayılan kapalı | Tek dekoratif odak, 12–20 s, ≤8 px | En çok iki destek katmanı, 16–28 s, ≤16 px |
| Mobil | Feedback aynı; reveal ≤8 px | Mesafe yaklaşık %50, stagger ≤40 ms | Mesafe ≤24 px, stagger ≤50 ms; pin yerine akış |

Spring değerleri süre değildir; duration/easing ile birlikte tek bir geçişe yığma. CSS'te tween, fizik destekleyen motorda spring seç. Spring'i input feedback için gereksiz salınımlı yapma. Exit'i enter süresinin yaklaşık %70'iyle başlat; eylemi bekletme.

Mobil süreyi körlemesine azaltma: daha küçük mesafe ve daha az eşzamanlı katman genellikle yeterlidir. Reduced-motion tüm profillerin üstünde çalışır: uzamsal hareket/loop kapalı, gerekli durum açıklaması anlık veya 100–150 ms opacity, stagger sıfır.

## Ana sahne ve ritim

Ana sahne için bir baskın motif seç: maskeli tipografi, ürün kompozisyonu veya anlatı paneli. Destek hareketleri aynı easing ve mesafe ailesinden gelsin. Sayfada giriş → bölüm geçişi → kullanıcı feedback'i → durum sürekliliği ritmini kur. İlgisiz başlıklara tekrar tekrar aynı gösterişli açılışı uygulama. Bir tablo satırının güncellenmesiyle portföy sahnesinin açılışı aynı yoğunlukta olmamalı.

Örnek Expressive plan:

| Bileşen | Amaç | Trigger | Efekt/token | Mobil | Reduced-motion |
| --- | --- | --- | --- | --- | --- |
| Hero başlık → açıklama → CTA → görsel | Mesajı sırayla okut | İlk görünür mount | 700 ms sahne, 65 ms stagger, 24 px | 12 px, 40 ms | Statik, gecikmesiz |
| Özellik bölümleri | Okuma ritmi | Görünür alana giriş | 400 ms geçiş, 24 px, tek sefer | 12 px; kısa grup | Statik veya 120 ms fade |
| CTA ve kartlar | Eylem feedback'i | Hover/focus/press | 160 ms, 4 px lift, scale .98 press | Lift kapalı; press | Ring/renk, transform yok |
| FAQ | Durumun anlaşılması | Aç/kapat | 220–400 ms panel, icon dönüşü | Aynı etkileşim | Anlık aç/kapat |

## Pattern kataloğu

### 1. Hero açılış koreografisi

**Amaç/seçim:** Başlık, açıklama, CTA ve görsel arasında öncelik kur; landing ve vitrinlerde ana sahne. Dashboard'da kısa başlık/toolbar feedback'ine indir.

**Trigger/değerler:** İlk görünür mount; başlık t=0, açıklama t=stagger, CTA t=2×stagger, görsel t=3×stagger. Sahne süresi ve ana mesafeyi profilden al; görsele .98→1 scale eklemek isteğe bağlı. CTA başlangıçtan itibaren tıklanabilir ve focus edilebilir; kritik metni JS bekleyen opacity:0 ile saklama.

**Mobil/reduced:** Mobilde mesafeyi ve öğe sayısını azalt, görseli metinle yarıştırma. Reduced-motion'da tüm öğeler aynı anda statik; gerekli kısa fade varsa gecikmesiz.

### 2. Satır reveal, maske ve stagger

**Amaç/seçim:** Kısa editoryal başlıkta tipografik vurgu. Uzun paragraf, form label'ı veya sık güncellenen veriyi kelime/harf halinde hareketlendirme.

**Trigger/değerler:** Hero mount veya section entry; Expressive 350–650 ms, Cinematic 600–1000 ms, satır iç wrapper 80–105%→0 translateY, profil easing/stagger. Maskenin padding'i aksan ve descender'ları kesmesin. Orijinal başlığın erişilebilir adı bir kez okunsun; dekoratif kopyalar `aria-hidden`. Satır kırılması gerekiyorsa gerçek ölçüm/font yüklenmesi ve resize sonrası yeniden ölçüm yap; metni elle sabit satırlara bölmek responsive'i bozabilir.

**Mobil/reduced:** Mobilde doğal line-wrap veya başlık bütününe kısa reveal. Reduced-motion'da maskeyi/transform'u kaldır, tam metin görünür. Seçilebilir metin, SEO ve okuma sırası korunur.

### 3. Scroll-triggered section reveal

**Amaç/seçim:** Sayfanın alt bölümlerini okuma akışına bağla; her liste satırını ayrı observer ile izleme.

**Trigger/değerler:** Section viewport'a girince; yaklaşık %10–20 intersection veya `top 85%`. Çok uzun section'da yüksek threshold kullanma. Geçiş süresi, ana mesafe, 3–5 öğelik kısa stagger; toplam gecikme bütçesini aşma. Tek sefer oynat, geri scroll'da okunan içeriği tekrar gizleme.

**Mobil/reduced:** Mobilde 8–12 px, kısa grup. Reduced-motion/no-JS'de içerik görünür; gözlemci başlayamaması görünürlüğü etkilemez.

### 4. Ölçülü parallax

**Amaç/seçim:** Dekoratif veya ürün görseline derinlik; metin, CTA, form ve sabit navigasyonda kullanma.

**Trigger/değerler:** Bölüm scroll ilerlemesi 0→1, lineer mapping; Expressive y=−12→12 px, Cinematic −24→24 px; ayrı iç görsel wrapper. Taşmayı güvenli kırp; görsel için yeterli overscan ayır. Scroll yönünü kilitleme veya wheel/touch'u ele geçirme.

**Mobil/reduced:** Mobilde statik veya yarı mesafe; reduced-motion'da y=0. Görselin konumu anlamı değiştirmesin.

### 5. İlerleme göstergesi

**Amaç/seçim:** Uzun makale veya anlatıdaki konumu göster; kısa sayfada gereksiz sayaç ekleme. İşlem ilerlemesini scroll ilerlemesiyle karıştırma.

**Trigger/değerler:** İlgili container scroll progress; `scaleX(0…1)`, origin left, lineer veya ≤100 ms smoothing. İçerik/viewport yüksekliği değişince ölçümü yenile. Dekoratif scroll bar'ı `aria-hidden` yap; gerçek işlem için isimli progressbar ve gerçek veri kullan, her kareyi live region'da duyurma.

**Mobil/reduced:** İnce statik konum işareti veya anlık ölçek güncellemesi; reduced-motion'da tween yok. Yerleşim metni/CTA'yı kapatmasın.

### 6. Sticky/pinned scroll storytelling

**Amaç/seçim:** 2–4 anlamlı adımı olan ürün/portföy hikâyesi; tablo/form veya kısa içerik için seçme. Önce CSS sticky'nin yeterli olup olmadığını değerlendir.

**Trigger/değerler:** Scene top'a gelince; panel başına yaklaşık 0.7–1 viewport scroll, 2–3 viewport toplam bütçe. Scrub 0.3–0.6 s yalnızca uygun motorda; içerik/durum erişimi animasyondan bağımsız. Aktif panel kadar klavye sırası ve erişilebilir içerik görünürlüğü de yönetilsin; üst üste görünmez linkler bırakma.

**Mobil/reduced:** Pin/scrub kapalı, paneller kaynak sırasıyla normal belge akışında. Jump/anchor erişimini ve geri scroll'u koru. Sticky header çakışması, orientation değişimi ve pin cleanup kontrol edilir.

### 7. Buton press ve link hareketi

**Amaç/seçim:** Gerçek eylemin feedback'i. Linki butona veya butonu div'e dönüştürme.

**Trigger/değerler:** Pointerdown/keyboard activation; scale .98→1, profil feedback süresi veya spring. Link underline `scaleX`, 120–180 ms. Hit area sabit outer kontrol, hareket eden inner label. Focus-visible ring gecikmeden görünür.

**Mobil/reduced:** Touch'ta kısa press, hover'a bağlı bilgi yok. Reduced-motion'da transform kapalı; underline/ring ve renk değişimi kalır.

### 8. Kart hover/focus ve seçili durum

**Amaç/seçim:** Tıklanabilir kartın eylemini veya aktif seçimi belirt; statik kartı etkileşimli gibi gösterme.

**Trigger/değerler:** Hover/focus-within; lift 2–4 px, scale en çok 1.015, 160–240 ms. Seçili işaret 120–220 ms opacity/position; seçimi ikon/metin/semantik durumla da belirt. Tek link içeren kartta link focus'u kullan; nested interactive hedefleri bozma.

**Mobil/reduced:** Hover lift yok, seçim işareti anlık. Reduced-motion'da border/ring/ikon ile feedback; focus outline maskeye takılmaz.

### 9. Modal ve drawer giriş/çıkışı

**Amaç/seçim:** Katman hiyerarşisi ve açılan panelin kökenini açıkla; mevcut dialog primitive'ini kullan.

**Trigger/değerler:** Open state; modal opacity + y=8–24 px, geçiş 220–400 ms, backdrop 120–180 ms; drawer 16–40 px slide + opacity veya uygun kısa tam kenar geçişi. Exit yaklaşık %70 süre. Semantik open/close ve focus yönetimi hareketten bağımsızdır; çıkışta kalan DOM artık tıklanabilir veya Tab ile erişilebilir olmasın.

**Mobil/reduced:** Drawer viewport'a sığar, iç scroll doğaldır; hareket mesafesi küçülür. Reduced-motion'da statik veya 100–150 ms fade; Escape, focus restore ve scroll lock cleanup aynı kalır.

### 10. Accordion ve tab geçişleri

**Amaç/seçim:** Açık panel ve aktif tab arasında süreklilik; klavye davranışı ve ARIA ilişkilerini mevcut primitive sağlar.

**Trigger/değerler:** Expanded/selected değişimi; accordion 180–280 ms içerik fade, icon 90°; gerekli height/layout animasyonu ölçülmüş küçük bölgede. Tabs 160–240 ms opacity, istenirse 4–8 px yönlü hareket; indicator 180–220 ms. Paneli animasyon için yeniden mount edip form verisini silme.

**Mobil/reduced:** Tap/keyboard aynı durumları açar. Reduced-motion'da yükseklik, indicator ve rotation anlık; focus ve okuma sırası doğru paneldedir.

### 11. Liste giriş/çıkışı, filtre ve layout sürekliliği

**Amaç/seçim:** Öğenin eklenmesini/çıkarılmasını veya filtre sonucunu takip ettir; canlı tabloda dikkat dağıtıcı tekrar girişleri önle.

**Trigger/değerler:** Stable key ile veri değişimi; yeni öğe 160–240 ms, 4–12 px; exit 120–180 ms. Layout FLIP/Motion `layout` 220–400 ms veya profil spring. Stagger yalnızca ilk kısa grup, ≤profil bütçesi; her keystroke'u sıraya alıp geciktirme. Pagination/virtualization ile tüm veri kümesini animate etme.

**Mobil/reduced:** Kısa fade veya anlık yeni düzen. Reduced-motion'da layout tween yok; sonuç sayısı/boş durum ve seçimin durumu metinle açıklanır. Focus kaybolan öğedeyse mantıklı hedefe alınır.

### 12. Mesh/gradient ve dekoratif floating katmanlar

**Amaç/seçim:** Marka atmosferi; ana içeriğin arkasında sınırlı alan. Calm'da varsayılan kapalı.

**Trigger/değerler:** Viewport'ta ve sayfa görünürken; profil loop süresi/mesafesi. Büyük `background-position`, blur veya filter yerine statik gradient içeren küçük wrapper'ı transform et. En fazla profil tablosundaki katman sayısı; metnin kontrastı tüm karelerde korunur. 5 saniyeden uzun otomatik hareket için uygulanabilir pause/stop kontrolünü değerlendir.

**Mobil/reduced:** Mobilde tek statik katman veya loop kapalı. Reduced-motion'da sabit kompozisyon. Offscreen, arka plan sekmesi ve kullanıcı pause durumunda loop durur.

### 13. Magnetic CTA, pointer-follow ve hafif tilt

**Amaç/seçim:** Yaratıcı vitrinlerde tek bir eylemi destekle; dashboard/form veya temel navigasyon için seçme.

**Trigger/değerler:** `(hover: hover) and (pointer: fine)` ve gerçek mouse pointer; CTA inner label max 4–6 px takip, tilt ±3°, dekoratif pointer halo 8–16 px offset; spring veya yaklaşık 100–180 ms smoothing. Outer hit area sabit, native cursor görünür; pointerleave/blur'da sıfırla. Ölçümleri event başına tekrarlama; RAF/motion value ile yaz, listener/RAF cleanup yap.

**Mobil/reduced:** Tamamen kapalı; statik CTA, ring ve press feedback. Klavye focus eşdeğeri ring/underline'dır, pointer takip değildir. Bu efektler hiçbir içeriğe erişimin koşulu olmaz.
