# Spotify-Clone
Made with Ai Completely
# 🎵 Cloneify — a full-stack Spotify clone

A working Spotify-style music streaming app with a React frontend, an Express backend,
and a SQLite database. Users can register/log in, search songs, like songs, create
playlists, and play music through a real audio player.

## ✨ Features

- **Authentication** — register/login with passwords hashed via Node `crypto.scrypt`
  and session tokens issued as JWTs (7-day expiry).
- **Music player** — play/pause, next/previous, seek, volume, mute, shuffle, and
  repeat (off / all / one). The bottom player bar persists across the whole app.
- **Search** — search songs by title, artist, or album (debounced), plus genre filters.
- **Likes** — heart any song; saved per user in SQLite. "Liked Songs" is its own playlist.
- **Playlists** — create, add songs to, remove songs from, and delete playlists.
  Fully persisted to the database.
- **Spotify-style dark UI** — sidebar navigation, hover-to-play rows, card grids,
  and a sticky top bar with a user menu.

## 🧱 Tech stack

| Layer    | Technology                                   |
| -------- | -------------------------------------------- |
| Frontend | React 18, React Router 6, Vite 6 (vanilla CSS) |
| Backend  | Node.js + Express 4                          |
| Database | SQLite via the built-in `node:sqlite` module (zero native deps) |
| Auth     | `crypto.scrypt` password hashing + `jsonwebtoken` |

## 🚀 Getting started

Requires **Node.js 22.5+** (uses the built-in `node:sqlite`; tested on Node 24).

```bash
# 1. Install dependencies (root + server + client)
npm run install:all

# 2. Seed the sample songs into the database
npm run seed

# 3. Run both servers in development mode
npm run dev
```

- Frontend: http://localhost:5173
- Backend API: http://localhost:5000

Open the frontend URL, sign up for a free account, and start listening.

### Production build

```bash
npm run build   # builds the client into client/dist
npm start       # Express serves the API + the built client on :5000
```

Then visit http://localhost:5000.

## ▶️ Start & stop the app

### Start (development — auto-reloads on file changes)

```bash
npm run dev      # runs the API (:5000) and the Vite frontend (:5173) together
```

- App: http://localhost:5173 · API: http://localhost:5000

### Start (production — this is what the phone/online link uses)

```bash
npm run build    # optional: rebuild the frontend after any client code changes
npm start        # Express serves the app + API on http://localhost:5000
```

### Share online (optional)

```bash
npm run share    # prints a public https://<random>.trycloudflare.com URL
```

The tunnel only works while `npm start` (or `npm run dev`) is still running.

### Stop

In the terminal that started the app, press **Ctrl+C**. That stops everything it launched
(server, frontend, and tunnel).

If a server is already running in the background (e.g., a closed terminal left it running)
and the port is busy, stop it from a new PowerShell window:

```powershell
# Stop whatever is listening on port 5000 (the app server)
$p = (Get-NetTCPConnection -LocalPort 5000 -State Listen).OwningProcess
Stop-Process -Id $p -Force

# Stop the public Cloudflare tunnel
Stop-Process -Name cloudflared -Force
```

> ⚠️ The whole app runs **on your PC** — it goes offline when the PC is shut down, and the
> tunnel URL changes every time the tunnel restarts. Your data (accounts, likes,
> playlists, uploaded MP3s) lives in the SQLite database, so it is never lost when the
> PC restarts.

## 🔌 API overview

| Method | Endpoint                          | Description                         |
| ------ | --------------------------------- | ----------------------------------- |
| POST   | `/api/auth/register`              | Create an account                   |
| POST   | `/api/auth/login`                 | Log in, returns a JWT               |
| GET    | `/api/auth/me`                    | Current user                        |
| GET    | `/api/songs?q=&genre=`            | List/search songs                   |
| GET    | `/api/songs/:id`                  | Single song                         |
| GET    | `/api/songs/genres`               | All distinct genres                 |
| GET    | `/api/likes`                      | Liked songs                         |
| PUT    | `/api/likes/:songId`              | Like a song                         |
| DELETE | `/api/likes/:songId`              | Unlike a song                       |
| GET    | `/api/playlists`                  | Current user's playlists            |
| POST   | `/api/playlists`                  | Create a playlist                   |
| GET    | `/api/playlists/:id`              | Playlist with its songs             |
| POST   | `/api/playlists/:id/songs`        | Add a song to a playlist            |
| DELETE | `/api/playlists/:id/songs/:songId`| Remove a song from a playlist       |
| DELETE | `/api/playlists/:id`              | Delete a playlist                   |
| GET    | `/api/audio-manifest`             | Expected local-MP3 filename for each song |

All endpoints except register/login require an `Authorization: Bearer <token>` header.

## 📂 Project structure

```
├── package.json          # root scripts (install:all, dev, build, start, seed)
├── server/
│   ├── index.js          # Express app
│   ├── db.js             # SQLite schema + connection
│   ├── seed.js           # Seeds 16 sample songs
│   ├── auth.js           # scrypt hashing + JWT middleware
│   └── routes/           # auth, songs, likes, playlists routers
└── client/
    ├── vite.config.js    # dev proxy /api → :5000
    └── src/
        ├── api.js        # fetch wrapper
        ├── context/      # AuthContext, PlayerContext
        ├── components/   # Sidebar, PlayerBar, SongRow, etc.
        └── pages/        # Home, Search, Library, Playlists, Login…
```

## 🌐 Sharing online

The app runs on `http://localhost:5000` (production build) and is bound to `0.0.0.0`,
so devices on the **same Wi-Fi** can already reach it at `http://192.168.29.90:5000`.

To share it with people **anywhere on the internet** (no account needed), start a
free Cloudflare tunnel:

```bash
npm run share
```

A one-time public URL like `https://<random>.trycloudflare.com` is printed and works
for anyone, anywhere. Note:

- The URL is **temporary** — it changes every time the tunnel restarts.
- Your PC must stay **on** and the server must keep **running** for the link to work.
- The tunnel binary lives in `tools/cloudflared.exe` (downloaded automatically on first run).
- For a permanent address, use a free [ngrok](https://ngrok.com) account or router
  port-forwarding to port 5000.

## 🎵 Sample audio

The library ships with **56 songs** — 16 royalty-free demo tracks plus 40 well-known
**Bollywood** and **Hollywood** songs (metadata only: real titles/artists/albums).

Playback uses [SoundHelix](https://www.soundhelix.com/examples/mp3/) demo MP3s and
album art from [picsum.photos](https://picsum.photos/) as placeholders, so no music
files are bundled and no copyrights are involved.

### ▶️ Play the *real* songs

Two easy ways to add your own legally-owned MP3s — no code or SSH needed:

1. **From the app itself (easiest):** tap any song marked “Tap to add MP3” and pick an
   audio file from your phone or computer. It uploads, becomes playable, and starts
   playing right away.
2. **Drop files into the server folder:** put them in `server/data/audio/` named
   `<title>-<artist>.mp3` (lowercase, spaces → dashes), e.g.:

   - `tum-hi-ho-arijit-singh.mp3`
   - `shape-of-you-ed-sheeran.mp3`
   - `neon-horizon-pulse-theory.mp3`

The server automatically streams your file instead of the placeholder (no restart
needed). Run `GET /api/audio-manifest` to see the exact expected filename for every song.

### 🔒 Songs that still say “Tap to add MP3”

The 16 demo tracks are playable out of the box (their placeholder audio matches the
fictional track name). The 40 Bollywood/Hollywood songs are **locked until you add a
real MP3** — the app never plays a mismatched placeholder, so a song named *Tum Hi Ho*
will never secretly play a random demo track again. Tap the ▶/🎵 on a locked song to
upload its audio file.
