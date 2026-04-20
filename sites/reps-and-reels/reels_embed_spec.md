# Reps & Reels — Reels Grid CMS + Embed Spec
**Stream B2 deliverable** — 2026-04-19
**Goal:** render the 6 most recent IG/TikTok reels on the homepage, in a CMS-driven grid, with lazy-loading, platform-native embeds, and a branded fallback if an embed fails.
**Non-goals:** aggregating IG + TikTok via a third-party tool (EmbedSocial/Curator). Deliberately avoided — see leverage check below.

---

## 1. Leverage check (why native embeds, not a widget)

| Option | Cost | Pros | Cons |
|---|---|---|---|
| **Native embeds in Webflow CMS** (chosen) | $0 | Full style control, no external JS bloat, survives platform redesigns, matches brand palette | Manual URL paste per post |
| EmbedSocial / Curator.io | $29-99/mo | Auto-pulls new posts, one widget call | Can't style past their container, adds ~80-200KB JS, subscription bleed, iframe inside iframe feels cheap |
| Webflow's native IG integration | $0 | Built-in | IG only (no TikTok), Meta breaks it roughly quarterly |

**Call:** native embeds win at this stage. If you hit >3 posts/week sustained and CMS-pasting becomes a chore, revisit EmbedSocial as the Phase 2 move.

---

## 2. Webflow CMS Collection — `Reels`

Create one Collection with these fields:

| Field name | Type | Required | Notes |
|---|---|---|---|
| **Name** | Plain text | Yes | Admin-only label, e.g., "Itchy Fingers 2026-04-15". Not shown publicly. |
| **Platform** | Option (single select) | Yes | Options: `Instagram`, `TikTok` |
| **Post URL** | Link | Yes | Full URL — e.g., `https://www.instagram.com/reel/C8xYz.../` or `https://www.tiktok.com/@repsreels/video/7321...`. Used to generate the embed. |
| **Caption snippet** | Plain text | No | Short line shown below the embed, max 80 chars. Pulled from the original post or written fresh. Acts as a fallback if embed doesn't load. |
| **Date posted** | Date | Yes | Used for sort order — newest first. |
| **Featured** | Switch | No | If true, shows before non-featured. Use sparingly — default is chronological. |
| **Hide from homepage** | Switch | No | Kill-switch for a specific post without deleting the CMS item. |

**Sort:** `Featured` desc, then `Date posted` desc.
**Limit:** 6 items on homepage. Future `/reels` sub-page can show all.

### Admin workflow

After posting to IG or TikTok:
1. Copy the post URL from the platform.
2. Open Webflow Editor → Reels Collection → New Reel.
3. Paste URL in **Post URL**, set **Platform**, add **Name** for your own reference, paste the first line of the caption into **Caption snippet**.
4. Save. Publish.

Total time per post: ~30 seconds.

---

## 3. Embed architecture

### The problem
- Instagram's official embed requires their `embed.js` (~95KB) and renders an iframe with their chrome.
- TikTok's official embed requires their `embed.js` (~40KB) and renders an iframe with their chrome.
- Both load synchronously by default. Loading 6 embeds on page load = ~1MB+ of JS + 6 iframes = Lighthouse tanks.

### The solution: **lazy, on-demand, click-to-load**

Each reel card shows a static placeholder with:
- Platform tag (mono chip — `IG` or `TIKTOK`)
- Caption snippet
- "Tap to load" affordance OR auto-load via `IntersectionObserver` only when the card enters viewport

On viewport entry (or click), the card swaps its inner content for the real embed iframe.

**Result:**
- Initial page load: zero third-party JS, zero iframes
- LCP stays under 2.5s
- Each embed only loads if the user actually scrolls to it or taps it
- Cards that never enter viewport never cost anything

---

## 4. Embed URL patterns

### Instagram
- Post URL: `https://www.instagram.com/reel/{SHORTCODE}/`
- Embed URL: `https://www.instagram.com/reel/{SHORTCODE}/embed`
- Extract shortcode with regex: `/reel\/([^\/\?]+)/`

### TikTok
- Post URL: `https://www.tiktok.com/@{USERNAME}/video/{VIDEO_ID}`
- Embed URL: `https://www.tiktok.com/embed/v2/{VIDEO_ID}`
- Extract video ID with regex: `/video\/(\d+)/`

Both platforms support a direct `/embed` path that doesn't require their official JS widget — we use an `<iframe>` pointing at the embed URL. Smaller, faster, fully style-contained.

---

## 5. Webflow Collection List markup

In the Webflow Designer, place a **Collection List** element where the reels grid goes. Bind it to the Reels Collection. Filter: `Hide from homepage = false`. Sort: `Featured` desc, `Date posted` desc. Limit: 6.

Inside each Collection Item, use an **Embed** block (HTML) with this template. Webflow will replace `{{ }}` with CMS field values at build time.

```html
<div class="reel-card"
     data-platform="{{ wf {&quot;path&quot;:&quot;platform&quot;} }}"
     data-url="{{ wf {&quot;path&quot;:&quot;post-url&quot;} }}"
     data-caption="{{ wf {&quot;path&quot;:&quot;caption-snippet&quot;} }}">
  <div class="reel-card__placeholder">
    <span class="reel-card__chip">{{ wf {&quot;path&quot;:&quot;platform&quot;} }}</span>
    <p class="reel-card__caption">{{ wf {&quot;path&quot;:&quot;caption-snippet&quot;} }}</p>
    <button class="reel-card__play" type="button" aria-label="Load reel">
      <svg viewBox="0 0 24 24" width="32" height="32" aria-hidden="true">
        <circle cx="12" cy="12" r="11" fill="none" stroke="currentColor" stroke-width="1.5"/>
        <path d="M10 8l6 4-6 4V8z" fill="currentColor"/>
      </svg>
    </button>
  </div>
</div>
```

Note: Webflow's actual CMS field syntax is `{{ wf {"path":"field-slug"} }}` — I've HTML-entity-encoded the quotes above because Webflow requires that inside Embed blocks. The field slugs (`platform`, `post-url`, `caption-snippet`) follow Webflow's default slug format — Webflow auto-generates these from field names, confirm the exact slug in your Collection settings.

---

## 6. Site-wide JS (paste in Webflow custom code, before `</body>`)

```html
<script>
(function() {
  'use strict';

  // Extract platform ID from a post URL
  function getEmbedUrl(platform, url) {
    if (!url) return null;
    if (platform === 'Instagram') {
      var m = url.match(/\/reel\/([^\/\?]+)/) || url.match(/\/p\/([^\/\?]+)/);
      return m ? 'https://www.instagram.com/reel/' + m[1] + '/embed' : null;
    }
    if (platform === 'TikTok') {
      var m = url.match(/\/video\/(\d+)/);
      return m ? 'https://www.tiktok.com/embed/v2/' + m[1] : null;
    }
    return null;
  }

  // Swap a card's placeholder for the real embed iframe
  function loadEmbed(card) {
    if (card.dataset.loaded === 'true') return;
    var platform = card.dataset.platform;
    var url = card.dataset.url;
    var embedUrl = getEmbedUrl(platform, url);

    if (!embedUrl) {
      showFallback(card);
      return;
    }

    var iframe = document.createElement('iframe');
    iframe.src = embedUrl;
    iframe.loading = 'lazy';
    iframe.allow = 'autoplay; encrypted-media; picture-in-picture; web-share';
    iframe.allowFullscreen = true;
    iframe.setAttribute('scrolling', 'no');
    iframe.title = platform + ' reel';
    iframe.className = 'reel-card__iframe';

    // Timeout fallback — if iframe doesn't signal load in 8s, show fallback
    var timeout = setTimeout(function() { showFallback(card); }, 8000);
    iframe.addEventListener('load', function() { clearTimeout(timeout); });
    iframe.addEventListener('error', function() {
      clearTimeout(timeout);
      showFallback(card);
    });

    card.innerHTML = '';
    card.appendChild(iframe);
    card.dataset.loaded = 'true';
  }

  // Show fallback state when embed fails
  function showFallback(card) {
    var platform = card.dataset.platform || 'the feed';
    var url = card.dataset.url || (platform === 'Instagram'
      ? 'https://instagram.com/repsreels'
      : 'https://tiktok.com/@repsreels');
    card.innerHTML =
      '<div class="reel-card__fallback">' +
        '<p>Embed\'s not cooperating.</p>' +
        '<p><a href="' + url + '" target="_blank" rel="noopener">Open on ' + platform + ' →</a></p>' +
      '</div>';
    card.dataset.loaded = 'true';
  }

  // Wire up: auto-load via IntersectionObserver when card is 50% visible,
  // OR on click/tap/keyboard-enter of the placeholder button
  function init() {
    var cards = document.querySelectorAll('.reel-card');
    if (!cards.length) return;

    // Click-to-load (redundant with observer but covers reduced-data users)
    cards.forEach(function(card) {
      var btn = card.querySelector('.reel-card__play');
      if (btn) {
        btn.addEventListener('click', function() { loadEmbed(card); });
      }
    });

    // Auto-load via IntersectionObserver
    if ('IntersectionObserver' in window) {
      var observer = new IntersectionObserver(function(entries) {
        entries.forEach(function(entry) {
          if (entry.isIntersecting) {
            loadEmbed(entry.target);
            observer.unobserve(entry.target);
          }
        });
      }, { threshold: 0.5, rootMargin: '100px' });
      cards.forEach(function(card) { observer.observe(card); });
    } else {
      // Fallback for ancient browsers — load everything immediately
      cards.forEach(loadEmbed);
    }
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
  } else {
    init();
  }
})();
</script>
```

**Size:** ~2KB minified, zero dependencies, no third-party requests on page load.

---

## 7. CSS (paste in Webflow custom code, inside `<head>`)

Uses the design tokens from Stream A2. If you've already set up CSS variables globally in Webflow, these will just work.

```html
<style>
  :root {
    --rr-brass: #C9A24B;
    --rr-black: #0B0B0C;
    --rr-iron: #3A3A3D;
    --rr-paper: #E8E4DB;
    --rr-paper-dim: rgba(232, 228, 219, 0.6);
  }

  .reel-card {
    position: relative;
    aspect-ratio: 9 / 16;
    background: var(--rr-iron);
    border: 1px solid rgba(201, 162, 75, 0.15);
    overflow: hidden;
    transition: border-color 200ms ease;
  }
  .reel-card:hover { border-color: rgba(201, 162, 75, 0.4); }

  .reel-card__placeholder {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    padding: 16px;
  }

  .reel-card__chip {
    align-self: flex-start;
    font-family: 'IBM Plex Mono', monospace;
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--rr-brass);
    border: 1px solid var(--rr-brass);
    padding: 4px 8px;
  }

  .reel-card__caption {
    font-family: 'Fustat', sans-serif;
    font-size: 14px;
    line-height: 1.4;
    color: var(--rr-paper);
    margin: 0;
    max-height: 4.2em;
    overflow: hidden;
  }

  .reel-card__play {
    align-self: flex-end;
    background: transparent;
    border: none;
    color: var(--rr-paper);
    cursor: pointer;
    padding: 0;
    transition: color 150ms ease, transform 150ms ease;
  }
  .reel-card__play:hover { color: var(--rr-brass); transform: scale(1.06); }
  .reel-card__play:focus-visible { outline: 2px solid var(--rr-brass); outline-offset: 4px; }

  .reel-card__iframe {
    width: 100%;
    height: 100%;
    border: 0;
    display: block;
  }

  .reel-card__fallback {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 8px;
    padding: 24px;
    text-align: center;
    font-family: 'Fustat', sans-serif;
    color: var(--rr-paper);
  }
  .reel-card__fallback p { margin: 0; font-size: 14px; }
  .reel-card__fallback a {
    color: var(--rr-brass);
    text-decoration: none;
    border-bottom: 1px solid var(--rr-brass);
    padding-bottom: 2px;
  }
  .reel-card__fallback a:hover { color: var(--rr-paper); border-bottom-color: var(--rr-paper); }

  /* Respect reduced motion */
  @media (prefers-reduced-motion: reduce) {
    .reel-card, .reel-card__play { transition: none; }
    .reel-card__play:hover { transform: none; }
  }
</style>
```

---

## 8. Grid container (the Collection List wrapper)

This goes on the Collection List element in Webflow Designer, or in custom CSS if the Designer layout panel doesn't expose it cleanly:

```css
.reels-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 16px;
}
@media (min-width: 640px) {
  .reels-grid { grid-template-columns: repeat(2, 1fr); gap: 20px; }
}
@media (min-width: 1024px) {
  .reels-grid { grid-template-columns: repeat(3, 1fr); gap: 24px; }
}
```

Apply `.reels-grid` as a class on the Collection List wrapper in Webflow.

---

## 9. Accessibility

- ✅ Each `.reel-card__play` button has `aria-label="Load reel"` and is keyboard-focusable
- ✅ Focus state is a 2px Aged Brass outline (matches site standard)
- ✅ `iframe title` attribute describes the embed ("Instagram reel", "TikTok reel")
- ✅ Fallback state is plain text + a link — fully accessible even if JS fails entirely
- ✅ Reduced-motion users get no hover transforms
- ✅ Placeholder caption is readable without the embed loading (useful for screen readers and slow connections)

---

## 10. Analytics (optional, Plausible-friendly)

If you install Plausible per the A4 SEO package, you can track reel engagement by adding this inside `loadEmbed()`:

```js
if (window.plausible) {
  window.plausible('Reel Load', { props: { platform: platform } });
}
```

Gives you a count of how many IG vs. TikTok embeds get loaded on the site. Doesn't track views on the embed itself (platforms own that data).

---

## 11. QA before shipping

- [ ] Confirm Webflow CMS field slugs match the Embed block template exactly (check slug in Collection settings)
- [ ] Publish a staging build, open DevTools Network tab, verify no `instagram.com` or `tiktok.com` requests fire on initial page load
- [ ] Scroll to reels section — confirm embeds load one at a time as they enter viewport
- [ ] Kill network mid-load on a cell connection — confirm fallback message appears within 8s
- [ ] Manually set a CMS item's `Post URL` to a broken URL, republish, confirm fallback renders
- [ ] Mobile test: confirm tap targets are ≥48px on the play buttons
- [ ] Screen reader test (VoiceOver on Mac, TalkBack on Android): confirm cards are announced with platform + caption
- [ ] Lighthouse run with 6 real embeds populated — target: Performance ≥85, no CLS from late-loading iframes
- [ ] Paste a new IG post URL into a fresh CMS item — confirm end-to-end flow works in under 60 seconds

---

## 12. What's NOT in this spec (deferred)

- **Dedicated `/reels` sub-page** — the CMS Collection supports it trivially (just make a Collection Page template and unfilter the 6-item limit), but the brief scopes MVP as homepage + maybe one sub-page. Defer until there's demand.
- **Auto-pulling new posts** — requires Zapier/Make + platform APIs, or EmbedSocial. Manual paste is fine at current cadence.
- **Commenting or reactions on reels** — the embed iframes are interactive on-platform; we don't duplicate.
- **Filtering by pillar (lifting / piping / dual-world)** — interesting future feature; not MVP.

---

## 13. Handoff note for Webflow build

When you (or Claude Code) port the homepage prototype to Webflow:

1. Create the Reels Collection first (per §2).
2. Add 6 placeholder CMS items with real IG/TikTok URLs so you can design against real data (not Lorem Ipsum).
3. Place a Collection List in the reels section of the homepage, bind to Reels Collection, set sort/limit/filter per §2.
4. Inside the Collection Item, add one Embed block and paste the template from §5.
5. Paste the site-wide JS from §6 into Webflow → Project Settings → Custom Code → Footer Code.
6. Paste the CSS from §7 into Webflow → Project Settings → Custom Code → Head Code (or your global stylesheet).
7. Test per §11.

Estimated Webflow build time for this section alone: **45-60 minutes** if you're comfortable with Webflow CMS, 2-3 hours first time.
