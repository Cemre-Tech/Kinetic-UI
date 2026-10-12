# Teknolojiye uyarlama ve uygulama örnekleri

Kod görevinde motor seçmeden önce oku. Örnekler uyarlanacak kalıplardır; tüm projelere kurulacak hazır framework değildir. Manifest ve lockfile'daki sürümü belirle; API kullanmadan önce güncel resmi dokümantasyonu sürümle karşılaştır. Context7 varsa önce resolve-library-id, sonra konuya özel query-docs; yoksa resmi belgeler. Gizli anahtar veya proje verisini dokümantasyon sorgusuna koyma.

## Motor seçimi ve sahiplik

| İhtiyaç | Önce değerlendir | Yeni bağımlılık için somut gerekçe |
| --- | --- | --- |
| Press, underline, küçük hover/focus | Mevcut CSS token/utility | Genellikle gerekmez |
| Tek sefer viewport reveal | IntersectionObserver + WAAPI veya mevcut motor | Native API yeterliyse ekleme |
| React presence, gesture, filtre/layout | Projedeki uyumlu Motion/Framer Motion | Çok bileşende presence/layout sürekliliği native çözümden belirgin şekilde basitleşiyorsa |
| Çok aşamalı timeline, pin ve scrub | Mevcut GSAP/ScrollTrigger veya CSS sticky | Koordineli sahne ve lifecycle gereksinimini açıklayabiliyorsan |

React projesinde mevcut `motion` varsa `motion/react`; mevcut `framer-motion` varsa kurulu sürümün uyumlu import/API'si. Skill'i kullanmak tek başına migration gerekçesi değildir. GSAP kullanımı her sayfa için zorunlu değildir. Var olan paketle işi çöz; yeni paket gerekiyorsa faydayı, bundle etkisini ve değişen lockfile'ı raporla.

Bir property için tek sahip belirle. Motion ile layout-transform verilen node'a CSS hover-transform veya GSAP scroll-transform verme. Dış wrapper scroll/FLIP, iç wrapper hover/press taşıyabilir; gereksiz wrapper semantiği bozmasın. CSS transition'ın JS motorunun aynı özelliğini geciktirmesine izin verme.

## Tek token kaynağı

Aşağıdaki örneği projenin mevcut token katmanına uyarlayıp tek kaynaktan CSS ve motor adapter'ları üret. Tablo değerleri motion-patterns.md ile tutarlıdır. Reduced-motion override'ı bileşende uygulanır.

```js
export const kineticProfiles = {
  calm: {
    duration: { feedback: 120, transition: 220, scene: 320 },
    distance: { small: 4, main: 8, max: 12 },
    easing: { enter: [0.2, 0, 0, 1], exit: [0.4, 0, 1, 1] },
    stagger: 25, delayBudget: 100,
    spring: { stiffness: 420, damping: 40, mass: 1 },
  },
  expressive: {
    duration: { feedback: 160, transition: 400, scene: 700 },
    distance: { small: 8, main: 24, max: 40 },
    easing: { enter: [0.22, 1, 0.36, 1], exit: [0.4, 0, 1, 1] },
    stagger: 65, delayBudget: 260,
    spring: { stiffness: 260, damping: 28, mass: 1 },
  },
  cinematic: {
    duration: { feedback: 180, transition: 550, scene: 1000 },
    distance: { small: 12, main: 40, max: 72 },
    easing: { enter: [0.16, 1, 0.3, 1], exit: [0.4, 0, 1, 1] },
    stagger: 100, delayBudget: 400,
    spring: { stiffness: 170, damping: 26, mass: 1 },
  },
};
export const seconds = (ms) => ms / 1000;
export const cssEase = (points) => `cubic-bezier(${points.join(',')})`;
export const staggerDelay = (index, t) =>
  Math.min(index * t.stagger, t.delayBudget);

// Uygulama/bootstrap katmanında; server render veya client effect içinde.
// Bunları build-time CSS üretmek için de kullanabilirsin.
export function cssTokens(t) {
  return {
    '--kinetic-feedback': `${t.duration.feedback}ms`,
    '--kinetic-ease': cssEase(t.easing.enter),
  };
}
```

Motion/GSAP duration'ı saniye, WAAPI/CSS ms alır. Cubic-bezier dizisini GSAP'in easing adı bekleyen API'sine doğrudan verme; mevcut CustomEase kurulumu varsa adapter üret, yoksa seçilmiş GSAP ease'ini ayrıca tek token olarak tanımla. Ölçüm gerektiren dimension/height geçişlerini toptan yasaklama; önce transform/opacity, sonra sınırlı ve ölçülmüş layout tercih et.

## CSS: semantik ve sabit hedef alanı

Kontrolün dış sınırını hareket ettirmek yerine `.kinetic-label` içeriğini hareket ettir. `cssTokens(t)` değerlerini ilgili köke uygulanmış varsay; CSS'teki fallback'lar JS çalışmasa da feedback sağlar.

```css
.kinetic-control:focus-visible {
  outline: 2px solid currentColor;
  outline-offset: 4px;
}
.kinetic-label {
  display: inline-block;
  transition: transform var(--kinetic-feedback, 160ms)
    var(--kinetic-ease, cubic-bezier(.22, 1, .36, 1));
}
@media (prefers-reduced-motion: no-preference) {
  .kinetic-control:active > .kinetic-label { transform: scale(.98); }
  .kinetic-control:focus-visible > .kinetic-label { transform: translateY(-2px); }
}
@media (hover: hover) and (pointer: fine) and (prefers-reduced-motion: no-preference) {
  .kinetic-control:hover > .kinetic-label { transform: translateY(-2px); }
}
@media (prefers-reduced-motion: reduce) {
  .kinetic-label { transition: none; }
}
```

`<button type="button" class="kinetic-control"><span class="kinetic-label">...</span></button>` veya gerçek `href`'li link kullan. Renk/arka planı mevcut marka token'ları belirler. Form submit kontrolünde doğru `type` ve mevcut handler korunur.

## WAAPI: görünür temel içerik ve cleanup

Bu örnekte CSS içeriği gizlemez. Başarılı observer initialization'dan sonra görünür bölüme sınırlı transform uygular; API başarısızsa içerik aynı yerde kalır. Hero gibi ilk boyamada önemli içerikte görünür→gizli bir flash üretme; transform-only giriş veya dekoratif katman reveal seç. Mask gerekiyorsa yalnızca animasyon aktifken uygula ve cleanup'ta kaldır.

```js
// Component effect/route mount içinde çağır; dönüşünü cleanup olarak kullan.
export function mountReveals(root, t) {
  if (!root || !('IntersectionObserver' in window)) return () => {};
  const reduce = window.matchMedia('(prefers-reduced-motion: reduce)');
  const mobile = window.matchMedia('(max-width: 767px)');
  const active = new Set();
  const seen = new WeakSet();
  let disposed = false;

  const cancelAll = () => {
    for (const animation of active) animation.cancel();
    active.clear();
  };
  const observer = new IntersectionObserver((entries) => {
    for (const { target, isIntersecting } of entries) {
      if (disposed || !isIntersecting || seen.has(target)) continue;
      seen.add(target);
      observer.unobserve(target);
      if (reduce.matches || typeof target.animate !== 'function') continue;
      const distance = mobile.matches ? Math.min(t.distance.main / 2, 24) : t.distance.main;
      try {
        const animation = target.animate(
          [{ transform: `translateY(${distance}px)` }, { transform: 'translateY(0)' }],
          { duration: t.duration.transition, easing: cssEase(t.easing.enter), fill: 'none' },
        );
        active.add(animation);
        // cancel() rejects finished; handle both outcomes.
        animation.finished.then(
          () => active.delete(animation), () => active.delete(animation),
        );
      } catch { /* Native visible layout remains usable. */ }
    }
  }, { threshold: 0.1 });
  for (const node of root.querySelectorAll('[data-kinetic-reveal]')) observer.observe(node);
  const onPreference = () => { if (reduce.matches) cancelAll(); };
  // Resize/orientation sırasında çalışan giriş doğal son konuma dönsün.
  const onMobileChange = () => cancelAll();
  reduce.addEventListener('change', onPreference);
  mobile.addEventListener('change', onMobileChange);
  return () => {
    disposed = true;
    observer.disconnect();
    reduce.removeEventListener('change', onPreference);
    mobile.removeEventListener('change', onMobileChange);
    cancelAll();
  };
}
```

Kökte her seçili node'un transform'u bu motora ait olmalı; mevcut transform'u varsa ayrı inner wrapper kullan. Bölüm çok uzunsa küçük trigger sentinel veya threshold=0 + güvenli rootMargin seç. Eski hedef tarayıcılarda matchMedia listener desteğini proje browser support politikasına göre uyarlamak gerekir.

## React/Motion: SSR'yi koruyan etkileşim

Kurulu `motion` için örnek; mevcut `framer-motion` projesinde import'u sürümüne göre uyarlayarak aynı yaklaşımı kullan. `t` tek token kaynağından seçilen profildir. Bilinmeyen reduced-motion tercihini konservatif ele al. Hover/focus transform'u sadece uygun cihazda isteğe bağlıdır; CSS ring tüm cihazlarda kalır.

```jsx
'use client';
import { motion, useReducedMotion } from 'motion/react';

export function KineticButton({ t, children, type = 'button', ...props }) {
  const reduce = useReducedMotion();
  const allowMotion = reduce === false;
  return (
    <motion.button
      {...props}
      type={type}
      className={['kinetic-control', props.className].filter(Boolean).join(' ')}
      initial={false}
      animate="idle"
      whileTap={allowMotion && !props.disabled ? 'pressed' : undefined}
    >
      <motion.span
        style={{ display: 'inline-block' }}
        variants={{ idle: { scale: 1 }, pressed: { scale: allowMotion ? 0.98 : 1 } }}
        transition={allowMotion
          ? { type: 'spring', ...t.spring }
          : { duration: 0 }}
      >{children}</motion.span>
    </motion.button>
  );
}
```

Outer button hit area sabit; press transform yalnızca iç span'a aittir. Bu span'a ayrıca CSS `.kinetic-label` transform'u verme; ek lift gerekiyorsa tek motorla aynı variant sistemine ekle. Focus ring'i overflow-hidden ancestor'ın kırpmadığını kontrol et. State değişiminde reduced-motion'a geçilirse animasyonu son duruma döndür.

`AnimatePresence` kullanırken çıkan child stable key taşımalı ve doğru presence scope'unda olmalı; initial içerikte `initial={false}` tercih edilebilir. `layout={reduce === false}` ile uzamsal layout hareketini kapat. Panelin kimliğini her toggle'da değiştirerek input state'ini silme. Dialog'un ARIA, focus trap, Escape, inert background ve focus restore işini animasyon kütüphanesinin kendiliğinden çözdüğünü varsayma.

### SSR/hydration ve hook sınırı

- Next.js App Router'da hook/browser API kullanan en küçük entry boundary'ye `use client` koy; sırf animasyon için tüm sayfayı client'a çevirme. Boundary'den geçen props serializable olmalı.
- Client Component ilk istekte server'da da render edilir. `window`, `document`, ölçüm ve matchMedia'yı module scope'ta/render sırasında okuma; effect veya event'e taşı. Server ve ilk client DOM'u tutarlı olsun.
- Motion'un `initial` değeri SSR çıktısına yansıyabilir: opacity:0 kullanımı otomatik hydration hatası değildir, fakat JS başlamadan kritik içeriği gizler. Görünür SSR fallback ve ilk paint'i özellikle kontrol et.
- Sadece mount state değiştirerek `initial` prop'unu değiştirmek giriş animasyonunu güvenilir biçimde yeniden oynatmaz. Var olan visible node'u mount sonrası imperative animate et veya yalnızca dekoratif layer'ı anime et; içerik ağacını remount etme.
- Hook'ları koşullu çağırma. Callback/ref kimlikleri ve effect bağımlılıklarını düzenle; Strict Mode setup→cleanup→setup döngüsünde kaynaklar çoğalmasın.
- İlgili sürüm destekliyorsa hooksuz Motion RSC kullanımını resmi rehberden değerlendir; interaktif hook örneğini server module'a kopyalama.

## GSAP: scoped pin ve responsive cleanup

Mevcut uyumlu GSAP/ScrollTrigger kurulumunu kullan. İlgili import'ları projeye göre seç; Next/SSR'de bu initializer'ı browser effect/client lifecycle içinde çağır. `gsap` ve `ScrollTrigger` burada kurulmuş bağımlılıklardır. Aşağıdaki örnek bir dekoratif scene içindir; panellerin kaynak sırasını/görünürlüğünü değiştirmez.

```js
export function mountPinnedScene(root, gsap, ScrollTrigger, t) {
  gsap.registerPlugin(ScrollTrigger);
  const mm = gsap.matchMedia(root);
  mm.add({
    desktop: '(min-width: 1024px)',
    reduce: '(prefers-reduced-motion: reduce)',
  }, (context) => {
    const { desktop, reduce } = context.conditions;
    if (!desktop || reduce) return;
    const art = root.querySelector('[data-kinetic-art]');
    if (!art) return;
    const delta = Math.min(t.distance.main, 24);
    gsap.fromTo(art, { y: delta }, {
      y: -delta,
      ease: 'none', // Scroll progress mapping is deliberately linear.
      scrollTrigger: {
        trigger: root, start: 'top top', end: '+=150%',
        pin: true, scrub: 0.4, invalidateOnRefresh: true,
      },
    });
  });
  return () => mm.revert();
}
```

`mm.revert()` scope içindeki animasyonları/ScrollTrigger'ları ve inline değişikliklerini temizler; global `killAll()` ile başka sayfaların animasyonlarını öldürme. Media callback'ta manuel listener eklersen kendi cleanup'ında kaldır. Font/görsel yüklenip ölçüler değiştiğinde `ScrollTrigger.refresh()` gereksinimini değerlendir, her scroll frame'de çağırma. Pin spacer ve styles remount/route sonrası kalmamalı.

## Lifecycle sözleşmesi

| Kaynak | Sahip ve temizlik |
| --- | --- |
| Listener / matchMedia | Setup yapan component/route; removeEventListener, aynı handler |
| Observer | Aynı scope; disconnect/unobserve |
| RAF / timer | cancelAnimationFrame, clearTimeout/clearInterval; callback disposed kontrolü |
| WAAPI | Oluşturulan Animation referansları; cancel, finished rejection handling |
| GSAP | Scoped context/matchMedia revert; event'te yaratılan tween'ler de aynı scope'a kaydedilir |
| Infinite loop | Görünürlük + document visibility + reduced-motion + kullanıcı pause birleşik koşulu; tüm listeners cleanup |

Persistent layout route boyunca mount kalıyorsa planın tetikleyicisini açıkça seç: yalnızca ilk mount veya route kimliği değişiminde scoped reinitialize. Tüm içerik ağacına route key verip form state'ini kaybetme. Back/forward ve ikinci mount'u doğrula.

## Resmi kaynaklar

API örneklerini uygulamadan önce hedef projenin kurulu sürümünü aşağıdaki resmi belgelerle karşılaştır.

- [Motion component: SSR, initial, import](https://motion.dev/docs/react-motion-component)
- [Motion reduced-motion](https://motion.dev/docs/react-use-reduced-motion)
- [Motion presence](https://motion.dev/docs/react-animate-presence)
- [GSAP matchMedia ve revert](https://gsap.com/docs/v3/GSAP/gsap.matchMedia())
- [Next.js client boundary](https://nextjs.org/docs/app/api-reference/directives/use-client)
- [WAAPI Element.animate](https://developer.mozilla.org/en-US/docs/Web/API/Element/animate)
