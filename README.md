# 🎵 Reso Lavalink Server — Discloud Deployment

> Self-hosted Lavalink v4 with **LavaSrc** (Spotify/Deezer) + **YouTube Source** plugins
> for high-quality, original-sounding audio playback.

---

## 📁 Files in this folder

| File | Purpose |
|------|---------|
| `application.yml` | Lavalink server configuration (plugins, sources, quality settings) |
| `discloud.config` | Discloud deployment config (Java runtime, RAM, auto-restart) |
| `Lavalink.jar` | **You must download this** — the Lavalink server itself |

---

## 🚀 Setup Steps

### Step 1: Get Spotify API Credentials (FREE)

1. Go to [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)
2. Click **"Create App"**
3. Fill in:
   - **App Name**: `Reso Lavalink` (anything works)
   - **App Description**: `Music bot` (anything works)
   - **Redirect URI**: `http://localhost` (required but not used)
4. Click **"Create"**
5. On the app page, click **"Settings"**
6. Copy your **Client ID** and **Client Secret**

### Step 2: Edit `application.yml`

Open `application.yml` and replace these placeholders:

```yaml
# Line ~25 — Change the password
password: "ResoLavalink2026!"    # Change to YOUR secure password

# Line ~109-110 — Paste your Spotify credentials
clientId: "YOUR_SPOTIFY_CLIENT_ID"
clientSecret: "YOUR_SPOTIFY_CLIENT_SECRET"
```

### Step 3: Download Lavalink.jar

Download the latest Lavalink v4 JAR from:
**https://github.com/lavalink-devs/Lavalink/releases/latest**

- Click on `Lavalink.jar` under "Assets"
- Save it into THIS folder (`lavalink/`)

Or run this PowerShell command from this folder:
```powershell
Invoke-WebRequest -Uri "https://github.com/lavalink-devs/Lavalink/releases/download/4.2.2/Lavalink.jar" -OutFile "Lavalink.jar"
```

### Step 4: Deploy to Discloud

1. **ZIP** this entire `lavalink/` folder contents:
   - `Lavalink.jar`
   - `application.yml`
   - `discloud.config`

   > ⚠️ ZIP the **contents**, not the folder itself. The files should be at the root of the ZIP.

2. Go to **Discloud Dashboard** or use the Discloud Discord bot
3. Upload the ZIP file using `.up` command or dashboard upload
4. Wait for it to start — check logs for: `Lavalink is ready to accept connections`

### Step 5: Update Your Bot's `.env`

Once Discloud gives you the host URL, update your bot's `.env`:

```env
LAVALINK_HOST=your-discloud-url-here
LAVALINK_PORT=2333
LAVALINK_PASSWORD=ResoLavalink2026!
LAVALINK_SECURE=false
```

---

## 🔧 YouTube OAuth Setup (Optional but Recommended)

If you get "Sign in Required" errors for some YouTube tracks:

1. In `application.yml`, set:
   ```yaml
   oauth:
     enabled: true
     skipInitialization: false
   ```
2. Restart Lavalink on Discloud
3. Check the logs — you'll see a Google device auth URL and code
4. Open that URL in your browser, enter the code using a **burner Google account**
5. Copy the `refreshToken` from the logs
6. Update `application.yml`:
   ```yaml
   oauth:
     enabled: true
     skipInitialization: true
     refreshToken: "paste_token_here"
   ```
7. Re-upload and restart

---

## 🎚️ Audio Quality Explained

This setup uses **ISRC-based Deezer matching** as the primary provider:

```
Spotify link → Extract ISRC code → Find exact match on Deezer → Stream studio master
```

**Provider priority** (configured in `application.yml`):
1. `dzisrc:%ISRC%` — Deezer by ISRC (exact studio master, best quality)
2. `ytmsearch:%ISRC%` — YouTube Music by ISRC (high quality fallback)
3. `dzsearch:%QUERY%` — Deezer text search
4. `ytmsearch:%QUERY%` — YouTube Music text search
5. `ytsearch:%QUERY%` — Regular YouTube (last resort)

This is why voices will sound **real and original** — we're matching the exact
recording (ISRC) instead of searching by title and getting random YouTube uploads.

---

## ❓ Troubleshooting

| Problem | Solution |
|---------|----------|
| `No matches found` for Spotify links | Check your Spotify clientId/clientSecret are correct |
| Songs still sound "fake" | Check logs — if source is `youtube`, Deezer ISRC isn't matching. Try adding a Deezer ARL cookie |
| `429 Too Many Requests` | Your IP is rate-limited. Wait or use a different IP |
| `Sign in required` (YouTube) | Set up YouTube OAuth (see above) |
| Lavalink won't start | Make sure you have at least 1024 MB RAM in `discloud.config` |
| `Connection refused` from bot | Check LAVALINK_HOST, PORT, PASSWORD in bot's `.env` match `application.yml` |
