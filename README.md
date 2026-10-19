<div align="center">

# 📸 G.Studio — PhotoBooth Web

**Browser-based photobooth studio. Take photos with your webcam, design a photo strip, download it in high resolution.**

No installation, no account, no uploads — everything is processed locally in your browser.

<br />

[![Live Demo](https://img.shields.io/badge/Live_Demo-photo--booth--web.vercel.app-000000?style=for-the-badge&logo=vercel&logoColor=white)](https://photo-booth-web.vercel.app/)
[![Repository](https://img.shields.io/badge/GitHub-PhotoBooth__Web-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/GaluhWikri/PhotoBooth_Web)

<br />

![React](https://img.shields.io/badge/React-18.3-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-5.5-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-5.4-646CFF?style=flat-square&logo=vite&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3.4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-2.x-3FCF8E?style=flat-square&logo=supabase&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

</div>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Live Demo](#-live-demo)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Getting Started](#-getting-started)
- [Environment Variables](#-environment-variables)
- [Supabase Setup](#-supabase-setup)
- [Available Scripts](#-available-scripts)
- [Project Structure](#-project-structure)
- [How It Works](#-how-it-works)
- [Deployment](#-deployment)
- [Browser Support](#-browser-support)
- [Troubleshooting](#-troubleshooting)
- [Roadmap](#-roadmap)
- [Author](#-author)
- [License](#-license)

---

## 🎯 Overview

**G.Studio PhotoBooth Web** turns any modern browser into a mini photo studio. Pick a layout, shoot or upload
photos, decorate the strip with stickers and custom backgrounds, then export the result as a print-ready
high-resolution JPEG.

The application is a **fully client-side SPA** — photos are captured from the webcam via the MediaDevices API,
processed on an HTML Canvas, and rendered into a final image entirely in the browser. Supabase is used **only**
as a lightweight content backend for assets (layouts, stickers, paper templates), so no user data or photos are
ever transmitted.

### Highlights

| | |
|---|---|
| 🎥 **Webcam capture** | Live preview with countdown timer, rule-of-thirds grid, and 14 built-in filters |
| 🖼️ **4 layout formats** | Classic 2×6 strip (2/4 pose) and 4×6 grid (4/6 pose) |
| 🎨 **Deep customization** | Background colors, uploaded backgrounds, photo shapes, text colors, draggable stickers |
| ⚡ **High-res export** | 1200px-wide JPEG drawn on canvas with proper cropping, masking, rotation, and filters |
| 📱 **Responsive** | Desktop grid and mobile carousel for layout selection; touch-friendly sticker controls |
| 🔒 **Privacy first** | All image processing is local; no camera data leaves the device |

---

## 🌐 Live Demo

> **➡️ https://photo-booth-web.vercel.app/**

The demo runs on Vercel. Camera access requires **HTTPS** (or `localhost`) — the demo and local dev setup both satisfy this.

---

## ✨ Features

### Camera
- Real-time webcam preview via `navigator.mediaDevices.getUserMedia`
- Graceful fallback to the default camera if the preferred `facingMode` is unavailable
- **Countdown timer** — 3s / 5s / 10s, with an animated full-screen counter and shutter flash
- **Rule-of-thirds grid** overlay to help with composition
- **14 filters** — Normal, Vintage, Grayscale, Smooth, B&W, Cyber, Bittersweet, OG Vintage, Fresh, Citrus, 2015, Focus, Candy, 80s
- Quick-access pill selector for the first 5 filters plus a full-screen filter gallery
- Photos are captured with an exact **`object-fit: cover`-style crop**, matching what the user sees in the preview
- Upload existing photos from disk as an alternative to the camera

### Photo Strip Editor
- **Photo shapes** — Square, Rounded, Circle, Heart (implemented via CSS mask + canvas path clipping so preview and export always match)
- **Backgrounds** — 7 curated solid colors, uploaded background image, or any paper template from Supabase
- **Text color** — 8 presets plus a native custom color picker
- **Stickers** — drag to position, scale (zoom in/out), rotate, and delete; controls appear as a floating toolbar on the selected sticker
- Live preview that updates instantly while editing
- Delete and retake individual photos, or reset the whole strip

### Export
- Renders the final composition to a **1200px-wide canvas** at the layout's exact aspect ratio
- Re-measures DOM element positions so output precisely matches the on-screen preview
- Applies per-photo filters and the mirrored (selfie) transform
- Draws shape clipping (rect / rounded / circle / heart), stickers with rotation, and custom-colored caption text
- Flattens transparency onto white and exports as **JPEG, quality 0.95**
- Filename: `photostrip-gstudio-<timestamp>.jpg`

### Pages
- **Landing** — hero section with animated floating photo-strip decorations
- **Choose Layout** — desktop grid / mobile carousel with "POPULAR" badges
- **Booth** — the editor described above
- **About**, **Privacy Policy**, **Contact**, **Support**

### Engineering
- **GitHub Actions keep-alive** workflow pings Supabase every 4 days so the free-tier project never pauses
- Strict TypeScript configuration (`strict`, `noUnusedLocals`, `noUnusedParameters`)
- ESLint 9 flat config with React Hooks and React Refresh plugins
- Content-driven layouts, stickers, and papers fetched from Supabase and sorted server-side — adding assets requires **no code changes**

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| UI framework | [React 18](https://react.dev/) + TypeScript |
| Build tool | [Vite 5](https://vitejs.dev/) |
| Styling | [Tailwind CSS 3](https://tailwindcss.com/) + PostCSS + Autoprefixer |
| Icons | [Lucide React](https://lucide.dev/) |
| Drag & drop | [react-draggable](https://github.com/react-grid-layout/react-draggable) |
| Backend / asset CMS | [Supabase](https://supabase.com/) (`@supabase/supabase-js`) |
| Image processing | Native **HTML Canvas 2D API** + `MediaDevices` API |
| Linting | ESLint 9 (flat config) |
| CI/CD | Vercel (hosting) + GitHub Actions (Supabase keep-alive) |

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────┐
│                     Browser (Client)                     │
│                                                          │
│  React SPA ──► State (useState/useRef, no global store)  │
│      │                                                   │
│      ├── Camera.tsx ──► getUserMedia ──► <canvas>        │
│      │                                                  │
│      ├── PhotoStrip.tsx ──► live preview (DOM/Tailwind)  │
│      │        └──► download() ──► Offscreen <canvas> ──► JPEG
│      │                                                   │
│      └── ChooseLayout.tsx                                │
└──────────────────────┬───────────────────────────────────┘
                       │  read-only REST (anon key)
                       ▼
              ┌─────────────────┐
              │    Supabase     │
              ├─────────────────┤
              │  layouts        │
              │  stickers       │
              │  paper          │
              └─────────────────┘
```

**Key design decisions**

- **Single-page state machine.** Navigation is a `currentPage` string in `PhotoBooth.tsx` — no router dependency, keeping the bundle small.
- **Canvas replay, not screenshotting.** Export does *not* use an HTML-to-image library. Instead `PhotoStrip` re-measures every element with `getBoundingClientRect()` and redraws it on a high-resolution canvas. This avoids CORS failures and produces crisp output at any size.
- **Preview === output.** Shape masks (rounded, heart) are implemented identically in CSS and in canvas paths so what you see is what you download.
- **Content lives in the database.** Layouts, stickers, and paper backgrounds are fetched from Supabase, so non-developers can extend the library at runtime.

---

## 🚀 Getting Started

### Prerequisites

- **Node.js ≥ 18** (developed and verified on Node 24)
- **npm ≥ 9** (or pnpm / yarn)
- A modern browser with webcam support
- A Supabase project (optional — see [Supabase Setup](#-supabase-setup))

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/GaluhWikri/PhotoBooth_Web.git

# 2. Enter the project directory
cd PhotoBooth_Web

# 3. Install dependencies
npm install

# 4. Create your environment file
cp .env.example .env   # then fill in your Supabase credentials

# 5. Start the development server
npm run dev
```

The app will be available at **http://localhost:5173**.

> ⚠️ **Camera access:** `getUserMedia` only works on `localhost` or an HTTPS origin. If you access the dev server
> from another device on your LAN (e.g. `http://192.168.1.10:5173`), the browser will block the camera.

---

## 🔐 Environment Variables

Create a `.env` file in the project root:

```env
VITE_SUPABASE_URL=https://your-project-ref.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-public-key
```

| Variable | Required | Description |
|---|---|---|
| `VITE_SUPABASE_URL` | ✅ | Your Supabase project URL |
| `VITE_SUPABASE_ANON_KEY` | ✅ | The **anon / public** API key (safe for client-side use — protected by Row Level Security) |

**Notes**

- Vite only exposes variables prefixed with `VITE_` to client code.
- Never commit `.env` — it is already listed in `.gitignore`.
- If the variables are missing, the app logs a warning and the asset library will be empty; the camera and
  manual uploads still work.
- ⚠️ Never put the **service role key** in a `VITE_` variable — it would be shipped to every visitor.

---

## 🗄️ Supabase Setup

The app expects three tables. All of them are **read-only for anonymous users**.

### 1. `layouts`

| Column | Type | Notes |
|---|---|---|
| `id` | `text` / `uuid` | Primary key |
| `type` | `text` | One of `strip-2`, `strip-4`, `grid-4`, `grid-6` |
| `title` | `text` | Display name |
| `description` | `text` | e.g. `Size 2 x 6 Strip (4 Pose)` |
| `photo_count` | `int4` | Number of photo slots |
| `aspect_ratio` | `float8` | Width ÷ height (e.g. `0.3333` for a 2×6 strip) |
| `css_class` | `text` | Preview styling hint |
| `preview_image_url` | `text` | Preview image (public URL or Storage path) |
| `display_order` | `int4` | Ascending sort order |

### 2. `stickers`

| Column | Type | Notes |
|---|---|---|
| `name` | `text` | Unique sticker name (also the sort key and React key) |
| `url` | `text` | Public image URL |

### 3. `paper`

| Column | Type | Notes |
|---|---|---|
| `name` | `text` | Unique paper name (sort key) |
| `url` | `text` | Public background image URL |

### Row Level Security

Enable RLS and allow public reads only:

```sql
alter table public.layouts  enable row level security;
alter table public.stickers enable row level security;
alter table public.paper    enable row level security;

create policy "Public read layouts"  on public.layouts  for select using (true);
create policy "Public read stickers" on public.stickers for select using (true);
create policy "Public read paper"    on public.paper    for select using (true);
```

### Asset hosting

Store sticker, paper, and preview images in a **public Supabase Storage bucket** (or any CDN that sends
CORS-permissive headers). Cross-origin images must be fetchable with CORS enabled, otherwise the canvas export
will fail with a *tainted canvas* error.

### Keep-alive workflow

`.github/workflows/keep-alive.yml` pings the Supabase REST API every 4 days (Supabase pauses free projects after
7 days of inactivity). Add these repository secrets under **Settings → Secrets and variables → Actions**:

| Secret | Value |
|---|---|
| `SUPABASE_URL` | Your project URL |
| `SUPABASE_ANON_KEY` | Your anon public key |

Trigger it manually from the **Actions** tab → *Keep Supabase Alive* → *Run workflow*.

---

## 📜 Available Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start the Vite dev server with HMR at `http://localhost:5173` |
| `npm run build` | Type-check and produce an optimized production bundle in `dist/` |
| `npm run preview` | Serve the built `dist/` bundle locally for verification |
| `npm run lint` | Run ESLint over the whole project |

---

## 📁 Project Structure

```
photobooth/
├── .github/
│   └── workflows/
│       └── keep-alive.yml        # Scheduled Supabase ping (prevents free-tier pause)
├── public/
│   ├── Icon/                     # Favicon
│   ├── LayoutType/               # Layout preview thumbnails (type 1–4)
│   ├── Paper/                    # Bundled fallback paper textures
│   └── Stickers/                 # Bundled fallback stickers
├── src/
│   ├── components/
│   │   ├── PhotoBooth.tsx        # Page router + booth state container
│   │   ├── Camera.tsx            # Webcam capture, filters, countdown, grid
│   │   ├── PhotoStrip.tsx        # Live preview + high-res canvas export
│   │   ├── ChooseLayout.tsx      # Layout picker (desktop grid / mobile carousel)
│   │   ├── LandingPage.tsx       # Hero / marketing page
│   │   ├── Navbar.tsx            # Responsive navigation bar
│   │   ├── ColorPicker.tsx       # Color swatch input
│   │   ├── About.tsx
│   │   ├── Contact.tsx
│   │   ├── PrivacyPolicy.tsx
│   │   └── Support.tsx
│   ├── lib/
│   │   └── supabaseClient.ts     # Supabase client initialisation
│   ├── App.tsx                   # Root component
│   ├── main.tsx                  # React entry point
│   ├── index.css                 # Tailwind directives + global styles
│   └── vite-env.d.ts
├── index.html
├── tailwind.config.js
├── postcss.config.js
├── vite.config.ts
├── tsconfig.json                 # + tsconfig.app.json, tsconfig.node.json
├── eslint.config.js
├── AI_PROMPT.md                  # Original build specification
└── package.json
```

---

## ⚙️ How It Works

### 1. Capture

`Camera.tsx` opens the webcam stream and mirrors it with `transform: scaleX(-1)` so it feels like a mirror. On
shutter, the visible region is translated into source-rectangle coordinates by replicating the
`object-fit: cover` algorithm, then drawn to a canvas and exported as a JPEG data URL (quality 0.9).

```ts
ctx.drawImage(video, offsetX, offsetY, renderWidth, renderHeight, 0, 0, canvas.width, canvas.height);
const photoDataUrl = canvas.toDataURL('image/jpeg', 0.9);
```

Filters are **not** baked into the captured bitmap — only the filter string is stored, so the CSS filter can be
re-rendered crisply later in the preview and re-applied via `ctx.filter` during export.

### 2. Edit

`PhotoBooth.tsx` holds the editor state (photos, background, shape, text color, stickers) and passes it down to
`PhotoStrip.tsx`, which renders the live preview using CSS Grid sized by the layout's `photoCount`.
Stickers are positioned with `react-draggable` and stored as `{ id, src, x, y, scale, rotation }`.

### 3. Export

`PhotoStrip` exposes an imperative `download()` handle. Export works by:

1. Waiting one frame for the sticker UI to hide.
2. Measuring the preview container and every photo element with `getBoundingClientRect()`.
3. Creating a 1200px-wide canvas at `layout.aspectRatio`.
4. Drawing the background (solid color or `cover`-cropped image).
5. For each photo: applying the shape clip path → `ctx.filter` → mirror transform → `cover` crop → `drawImage`.
6. Redrawing stickers with translation and rotation, then the caption text with matching font metrics and shadow.
7. Flattening onto white and calling `toDataURL('image/jpeg', 0.95)` to trigger the download.

---

## 🚢 Deployment

### Vercel (recommended)

1. Push the repository to GitHub.
2. Import the project at [vercel.com/new](https://vercel.com/new).
3. Vercel auto-detects Vite. Confirm:
   - **Framework preset:** Vite
   - **Build command:** `npm run build`
   - **Output directory:** `dist`
4. Add `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` under **Settings → Environment Variables**.
5. Deploy. HTTPS is provided automatically, which is required for camera access.

### Netlify / Cloudflare Pages / GitHub Pages

The output is a static bundle in `dist/` — any static host works. Since routing is handled in React state
(not URL paths), **no SPA rewrite rules are required**.

### Local production check

```bash
npm run build
npm run preview
```

---

## 🌍 Browser Support

| Browser | Status |
|---|---|
| Chrome / Edge (desktop & Android) | ✅ Fully supported |
| Firefox | ✅ Supported |
| Safari (macOS & iOS 15+) | ✅ Supported |
| Internet Explorer | ❌ Not supported |

Requires `MediaDevices.getUserMedia`, Canvas 2D (`ctx.filter`, `ctx.roundRect` with fallback), and CSS
`mask-image`. On browsers without `ctx.roundRect`, a manual rounded-rect path is used automatically.

---

## 🧩 Troubleshooting

| Symptom | Cause & Fix |
|---|---|
| **Camera preview is black / permission prompt never appears** | The page is not on HTTPS or `localhost`, or the camera is used by another app. Close other apps, allow the permission, and reload. |
| **"Loading stickers..." never resolves** | Supabase env vars are missing or the tables don't exist. Check the browser console and [Supabase Setup](#-supabase-setup). |
| **Download throws a canvas / CORS error** | Asset images are served without CORS headers. Host stickers and papers on a public bucket that sends `Access-Control-Allow-Origin: *`. |
| **Downloaded strip looks different from the preview** | Rebuild and hard-refresh — a stale bundle is the usual cause. Layout metrics depend on the rendered preview size. |
| **`npm run build` fails on unused variables** | `tsconfig.app.json` enables `noUnusedLocals` / `noUnusedParameters`. Remove or use the flagged symbols. |
| **Layouts list is empty** | The `layouts` table is empty. `ChooseLayout` falls back to the bundled `DEFAULT_LAYOUTS` only when the fetch fails; seed the table to populate the picker. |

---

## 🗺️ Roadmap

- [ ] Editable caption text (currently fixed to `G.STUDIO`)
- [ ] Persist sessions in Supabase so strips can be shared via a link
- [ ] PNG export with transparent background
- [ ] AI background removal for portrait cut-outs
- [ ] Additional layout presets and multi-strip sheets
- [ ] Automated tests (Vitest + Playwright) and a CI lint/build pipeline

---

## 👤 Author

**Galuh Wikri Ramadhan**

[![GitHub](https://img.shields.io/badge/GitHub-GaluhWikri-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/GaluhWikri/)

---

## 📄 License

Released under the **MIT License**. See the [LICENSE](LICENSE) file for details, or add one if it is not present yet.

<div align="center">
<br />

**Made with ❤️ and a webcam.**

⭐ If this project helped you, consider giving it a star!

</div>
