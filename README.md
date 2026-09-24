# Legend Boy: your AI assistant for your phone (powered by Google Gemini)

Legend Boy is a mobile AI assistant. **Cloudflare only hosts it** (free). **All the AI runs on Google Gemini** using your own API key.

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/Goro888/Alixo)

## Features

| Section | What it does |
|---|---|
| **Splash** | Opens with Legend Boy's photo and a glowing animation. Tap it and he **greets you out loud** |
| **Talk** | Hands-free voice chat. You talk, he listens, stops by himself when you go quiet, answers **with his voice**, then listens again. Tap his face to interrupt him |
| **Chat** | Streaming AI chat with Markdown and code blocks. Each reply can be copied, read aloud, shared or retried. Chats are saved to the device, and you can use voice typing |
| **Camera** | Live camera with front/back flip. Take a photo, then ask about it with a mode: *Describe, Read text, Solve, Translate, Identify, Tips* |
| **Files & Photos** | Reads **PDF, Word (.docx), Excel (.xlsx), PowerPoint (.pptx), CSV, text, code** and photos. Summarise, explain, pull out key facts, or get a quiz |
| **Research** | Uses **Google Search grounding**: Gemini searches Google and writes a report with **numbered citations** and source cards. Has *Quick* and *Deep* modes |
| **Create image** | Makes an image from text with Gemini "Nano Banana" |
| **Settings** | Change Legend Boy's photo, set your name, pick one of 30 Gemini voices (or the phone's voice), choose the speech language (incl. Kurdish and Arabic), turn on auto-read and the greeting, delete chats |
| **Install as app** | It's a PWA: "Add to Home Screen" gives it an icon and opens it full screen |

### Gemini models used (change them in `wrangler.jsonc` → `vars`)
| Var | Default | Used for |
|---|---|---|
| `GEMINI_MODEL` | `gemini-flash-latest` (always Google's newest Flash) | chat, photos, research, voice transcription, PDF reading |
| `GEMINI_TTS_MODEL` | `gemini-3.8-flash-lite-tts` | Legend Boy's voice |
| `GEMINI_IMAGE_MODEL` | `gemini-3.1-flash-lite-image` | creating images |
| `TTS_SPEAKER` | `Puck` | default voice |

If a model isn't available on your plan, the app shows a clear error. Voice falls back to the phone's built-in voice automatically.

---

## 🚀 Deploy to Cloudflare

### 1) Deploy the app
**From the dashboard (works on a phone):**
1. Go to **dash.cloudflare.com → Workers & Pages → Create → Import a repository**
2. Choose **Goro888/Alixo**. Leave **Build command** empty, set **Deploy command:** `npx wrangler deploy`
3. Click **Deploy**

**Or from a computer:** `npm install && npx wrangler login && npm run deploy`

### 2) Add your Gemini API key (as a SECRET, never in the code)
1. Get a key at **https://aistudio.google.com/apikey** (new keys start with `AQ.`, which is fine)
2. Cloudflare dashboard → your Worker **legend-boy** → **Settings → Variables and Secrets → + Add**
3. **Type:** `Secret` · **Name:** `GEMINI_API_KEY` · **Value:** your key → **Deploy / Save**

   (computer alternative: `npx wrangler secret put GEMINI_API_KEY`)

> ⚠️ Never paste your key into `wrangler.jsonc` or any file. This GitHub repo is public and bots steal keys within minutes.

### 3) Put it on your phone
Open `https://legend-boy.<your-name>.workers.dev`
- **iPhone (Safari):** Share → **Add to Home Screen**
- **Android (Chrome):** ⋮ → **Install app**

Camera and microphone need https. Cloudflare gives you https automatically.

### Optional secrets
| Secret | Why |
|---|---|
| `ACCESS_CODE` | Password-locks the app so strangers can't use up your Gemini quota. Enter the code in the app's Settings |

**Cost:** Cloudflare Workers hosting is free (100k requests/day). Gemini has a free tier with per-minute and per-day limits. See your limits at https://aistudio.google.com/rate-limit. If you hit them, the app tells you to wait.

---

## 🖼️ Use your own photo for Legend Boy
There are two ways:
1. **In the app:** Settings ⚙️ → *Legend Boy's photo* → **Change photo**. The photo is stored on your phone.
2. **For everyone:** replace `public/img/legend-boy.jpg` with your photo (square, about 640×640). If you want the icons to match, also replace `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` and `favicon.png` in the same folder. Then redeploy.

---

## 🧪 Local development
```bash
npm install
npm run dev:demo   # demo mode: fake AI answers, no key needed
# real AI locally: create a file .dev.vars with  GEMINI_API_KEY=your_key  (it is git-ignored), then:
npm run dev
```

## Project structure
```
wrangler.jsonc          Cloudflare config (Worker + static assets + Gemini model vars)
wrangler.demo.jsonc     Local demo config (fake AI)
src/worker.js           API (Gemini): /api/chat, /api/research, /api/transcribe, /api/tts, /api/extract, /api/imagine
public/                 The phone app (no build step)
  index.html            Screens: splash, chat, talk, camera, files, research, settings
  css/app.css           Mobile-first dark UI with safe-area support
  js/app.js             App logic
  js/voice.js           Mic recording + auto-stop on silence + voice playback queue
  js/camera.js          Live camera
  js/markdown.js        Safe Markdown renderer
  js/api.js, store.js, media.js
  sw.js, manifest.webmanifest   Installable PWA
  img/                  Legend Boy photo + app icons
```
