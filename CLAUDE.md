# CLAUDE.md — Thalia's Birthday Scrapbook

## What This Project Is
A birthday scrapbook website for Thalia. Friends contribute pages with photos, a Spotify embed, and a personal message. Built as a static single-page React app — no build step, no bundler.

## Skill Invoking
- **Invoke the `frontend-design` skill** before writing any frontend code, every session, no exceptions.

---

## Tech Stack
- **React 18** via CDN UMD + Babel standalone for JSX (no Node, no bundler)
- **Custom CSS variables** — not Tailwind. Never introduce Tailwind.
- **Fonts:** Cormorant Garamond (display/headings) + Jost (UI/labels) via Google Fonts
- **Primary color:** `--rose: #C4686A` and its derived palette (`--rose-mid`, `--rose-light`, `--rose-pale`)
- All styles live inline in `index.html` inside a `<style>` block
- No external CSS or JS files except page/section data files

---

## File Structure
```
index.html                — app shell: all CSS, all components, router
serve.mjs                 — local dev server (port 3000)
pages/
  _template.js            — copy this to add a new contributor page
  pageCollage.js          — Abbey's collage page (layout: 'collage', 42 photos)
  pageAdnan.js            — Adnan's page (fireworks + password-locked)
  page-recap.js           — Party recap page (party: true, password-locked)
sections/
  _template.js            — copy this to add an intro/transitional section
images/
  imgCollage/             — photos for the collage page (photo-1.JPG … photo-42.JPG)
  imgAdnan/               — photos for Adnan's page
```

---

## Current Page Order
Determined by `<script>` tag order in `index.html`:
1. `pageCollage.js` — "Little Moments ✦" (collage layout)
2. `pageAdnan.js`   — Adnan's page (standard polaroid layout)
3. `page-recap.js` is loaded but uses `party: true` so it appears in its own "The Party" menu section

---

## How Pages Work
Each `pages/*.js` file pushes a data object to `window.__SCRAPBOOK_PAGES__`. Page order = `<script>` tag order in `index.html`. To add a page:
1. Copy `pages/_template.js` → `pages/page-yourname.js`
2. Fill in content
3. Add `<script src="pages/page-yourname.js"></script>` to `index.html` in the desired position

Page data fields:
```js
{
  title:          string,
  layout?:        'collage' | 'grid',   // omit for default polaroid strip layout
  spotifyEmbed?:  string,               // Spotify embed URL
  spotifyStartAt?: number,              // seconds
  photos:         [{ src, caption?, crop?, fit? }],
  message?:       string,
  celebrations?:  true,                 // floating hearts + fireworks on this page
  party?:         true,                 // puts page in "The Party" menu section instead of "Pages"
  password?:      string,               // locks page behind a password prompt
}
```

### Page Layouts
| `layout` value | Component | Description |
|---|---|---|
| *(omitted)* | `PolaroidStrip` | 5 swinging polaroids on a wire — standard contributor layout |
| `'grid'` | `PhotoGrid` | Masonry-style grid of polaroid cards, any number of photos |
| `'collage'` | `PhotoCollage` | Scattered tape/photo cards with swing animation, seeded shuffle, supports `{ text: "..." }` note cards |

## How Intro Sections Work
Same pattern as pages but push to `window.__INTRO_SECTIONS__`. These appear between the homepage and the scrapbook. Load them via `<script src="sections/section-N.js"></script>` in `index.html`.

---

## App Navigation Flow
`home` → `intro[0…N]` (optional) → `scrapbook[0…N]`

- Home button (top-right) returns to homepage
- Hamburger menu (top-left) lists all pages and allows direct navigation
- Prev/Next buttons at the bottom navigate sequentially
- Previous on scrapbook page 1 goes back to last intro section (or home if none)
- Dark mode toggle (crescent moon icon, top-right)

### Page Transitions
Directional sliding via `navigate(dir, fn)` — `dir` is `'fwd'`, `'bwd'`, or `'jump'`:
- **Forward**: current page exits left, next slides in from right
- **Backward**: current page exits right, previous slides in from left
- **Jump** (menu): scale+fade in/out

---

## Local Dev Server
```
node serve.mjs
```
Serves project root at `http://localhost:3000`. Start in background. Do not start a second instance if already running.

---

## Git & Deployment
- **Never push to GitHub unless the user explicitly says to.** Do not commit or push after making changes.
- Deployed on **Vercel** connected to `github.com/AdnanHacks/ts-scrapsite`
- Vercel auto-deploys on push to `main`
- **Image filenames are case-sensitive on Vercel (Linux).** Always match exact case in both the file and the `src` reference.
- Images in `imgCollage/` use uppercase `.JPG` extension — keep consistent.
- Some photos were originally HEIC disguised as `.JPG`. Convert with: `sips -s format jpeg photo.JPG --out photo.JPG`

---

## Design Rules
- Never use `transition-all` — only animate `transform` and `opacity`
- Never use Tailwind or default blue/indigo colors
- All interactive elements need hover, focus-visible, and active states
- Surfaces use a layering system: `--bg` (base) → `--surface` (cards) → floating (modals/lightbox)
- Spacing uses CSS `clamp()` for responsive scaling
- Dark mode via `[data-theme="dark"]` on `document.documentElement`

---

## Key Components (all in index.html)
| Component | Purpose |
|---|---|
| `HomePage` | Landing screen with note card |
| `IntroSection` | Transitional pages between home and scrapbook |
| `PolaroidStrip` | 5 swinging polaroids on a wire with lightbox on click |
| `PhotoGrid` | Wrapping grid of tilted polaroid cards for `layout: 'grid'` pages |
| `PhotoCollage` | Scattered tape cards with swing animation for `layout: 'collage'` pages |
| `Lightbox` | FLIP animation zoom from thumbnail to center |
| `SpotifyEmbed` | Spotify iframe (152px height, autoplay attempted) |
| `MessageCard` | Typewriter-animated message with ghost-overlay height trick |
| `CelebrationEffects` | Floating hearts + fireworks on pages with `celebrations: true` |
| `PasswordGate` | Lock screen for pages with a `password` field |

---

## Things NOT to Do
- Do not add unrequested features or "improvements"
- Do not refactor working code unless asked
- Do not add comments or docstrings to code you didn't change
- Do not push to GitHub without being explicitly told
- Do not start a second dev server if one is running
- Do not use Tailwind
- Do not introduce a build step or bundler
