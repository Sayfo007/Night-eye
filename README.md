# NightEye CCTV

Turn spare Android phones into private security cameras. One static web app, no backend to run.

- **One login, two roles** – sign in with the same email and password on every device, then choose **Camera** or **Viewer**.
- **Live view** – WebRTC video straight from the camera phone, up to 1080p / 30 fps.
- **Recording on the camera** – continuous clips saved on the camera phone (IndexedDB), with automatic deletion of the oldest clips when your storage limit is reached.
- **Recorded history** – browse, play, download and delete a camera's clips remotely from any viewer.
- **Two-way talk** – hold the mic button on the viewer and speak through the camera's speaker.
- **Installable** – works as a full-screen PWA from Chrome's "Add to Home screen".

## Deploy (GitHub Pages)

Camera and microphone access require HTTPS, so the app must be hosted.

1. Create a new GitHub repository and push these files to the `main` branch.
2. In the repository go to **Settings → Pages** and set **Source** to **GitHub Actions**.
3. The included workflow (`.github/workflows/pages.yml`) publishes the site on every push. Your app will be at `https://<your-username>.github.io/<repo-name>/`.

Other static hosts (Netlify, Cloudflare Pages, Vercel) also work: serve the repository root as-is.

## Use

1. **Camera phone:** open the site in Chrome, sign in, choose **Camera**, pick a slot (1–8) and quality, then tap **Start camera**. Keep it plugged in with the screen on.
2. **Your phone:** open the site, sign in with the **same** email and password, choose **Viewer**. Cameras appear automatically (tap **Scan** to refresh). Tap one to watch, talk or open its recorded history.
3. Optional: use Chrome's menu → **Add to Home screen** on each device for a full-screen app.

## How it works

- Your email and password are hashed locally (SHA-256) into a private key. Each camera registers on the free public PeerJS broker under an ID derived from that key plus its slot number; viewers derive the same IDs and connect to them. Only someone who knows your email and password can compute those IDs.
- Video and audio travel peer to peer over WebRTC. The broker only helps devices find each other.
- Recordings stay on the camera phone. History is requested over a WebRTC data channel when a viewer opens it.

## Limitations

- No real account system: there is no sign-up, password reset or server-side check, and a weak password means a guessable key. Use a long, unique password.
- Discovery uses PeerJS's free public broker, which has no uptime guarantee. For production, self-host a [PeerJS server](https://github.com/peers/peerjs-server) and pass its `host`, `port` and `path` in the `new Peer(...)` options.
- The default TURN relay (`openrelay.metered.ca`) is shared and rate-limited. Replace the `ICE` constant in `index.html` with your own TURN credentials for dependable connections on mobile networks.
- Clips are stored in browser storage. Clearing site data deletes them, and they can only be viewed while the camera is online.
- Android may throttle the page if the camera phone's screen turns off; keep it awake and charging.
- Clips are saved in segments, so there can be a fraction of a second between clips.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app (UI, WebRTC, recording, history) |
| `manifest.webmanifest` | PWA install metadata |
| `sw.js` | Service worker that caches the app shell |
| `icons/` | App icons |
| `.github/workflows/pages.yml` | Deploys to GitHub Pages |

## License

MIT
