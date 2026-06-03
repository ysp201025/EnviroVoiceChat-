# 🎙️ EnviroVoice

> **Minecraft Bedrock Proximity Voice Chat** — Browser-based, WebRTC-powered, environment-aware.

EnviroVoice is a self-hosted web app that brings **proximity voice chat** to Minecraft Bedrock. Players hear each other at volumes that scale with in-game distance, and their audio automatically shifts to match the environment — underwater reverb, cave echoes, and more. No plugins required; just open the page in your browser.

---

## ✨ Features

- **Proximity Audio** — Volume fades as players move apart; silence beyond the configured max distance (default 20 blocks).
- **Environment Effects** — Automatic audio FX based on your in-game state:
  - 🌊 **Underwater** — Low-pass filter for a muffled, submerged feel.
  - 🕳️ **Cave** — Deep reverb + feedback delay for hollow cave acoustics.
  - 🌿 **Surface** — Clean audio with no effects.
- **Push-to-Talk (PTT)** — Keyboard-based PTT with a customisable key binding (desktop only).
- **Voice Activity Detection** — Visual indicator when you or other players are speaking.
- **Minecraft Bedrock Integration** — The game can remotely mute/deafen players and adjust mic volume.
- **Microphone Selector** — Switch between available audio input devices mid-call.
- **Participant List** — Live list of connected players with their Minecraft skin, speaking state, and distance in blocks.
- **Saved Profile** — Gamertag and room URL are stored locally so you skip setup on your next visit.
- **Light / Dark Mode** — Minecraft-themed UI with a toggleable dark mode.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Voice transport | WebRTC (peer-to-peer audio) |
| Signaling | WebSocket (bring-your-own server) |
| Audio FX | [Tone.js](https://tonejs.github.io/) v14 |
| NAT traversal | STUN + TURN (public free servers) |
| Player skins | [mc-api.io](https://mc-api.io) Bedrock endpoint |
| UI | Vanilla HTML / CSS / JavaScript |
| Fonts | Minecraft Regular & MinecraftTen |

---

## 📁 Project Structure

```
EnviroVoice/
├── index.html          # App shell — 3-screen UI (Setup → Profile → Call)
├── script.js           # Core logic (WebRTC, signaling, audio, Minecraft integration)
├── colorMode.js        # Light/dark mode toggle with localStorage persistence
├── style.css           # Minecraft-themed component system (CSS variables, dark mode)
├── version.json        # Last-updated timestamp
├── fonts/
│   ├── 1_Minecraft-Regular.otf
│   └── MinecraftTen-VGORe.ttf
└── img/
    ├── icon.png
    ├── title-light.png
    ├── title-dark.png
    ├── sun.png
    └── moon.png
```

---

## 🚀 Getting Started

### 1. Prerequisites

- A **WebSocket signaling server** (handles `join`, `leave`, `offer`, `answer`, `ice-candidate`, and `participants-list` message types).
- A web host that serves the files over **HTTPS** — browsers require it for microphone access.

### 2. Deploy

Upload the project folder to any static host (GitHub Pages, Netlify, your own server, etc.).

```bash
# Example with a local dev server
npx serve .
```

> **Important:** Microphone access requires HTTPS. `localhost` is the only plain-HTTP exception.

### 3. Use

1. Open the URL in a supported browser (Chrome, Firefox, or Safari).
2. Enter your **Minecraft Gamertag / Xbox Live username**.
3. Paste your **WebSocket room URL** (e.g. `wss://your-server.example.com/room/abc`).
4. Click **💾 Save**, then **🎙️ Join Voice Channel**.
5. Grant microphone permission when prompted.

---

## 🎮 Minecraft Integration

EnviroVoice listens for a WebSocket data frame from Minecraft containing player state. The expected payload structure is:

```json
{
  "type": "minecraft-data",
  "myPlayer": {
    "gamertag": "YourGamertag",
    "data": {
      "isUnderWater": false,
      "isInCave": true
    }
  },
  "playersList": [
    { "gamertag": "FriendA", "x": 10, "y": 64, "z": -5 },
    { "gamertag": "FriendB", "x": 30, "y": 64, "z": 10 }
  ],
  "muteStates": [
    { "gamertag": "YourGamertag", "isMuted": false, "isDeafened": false, "micVolume": 1.0 }
  ],
  "config": {
    "maxDistance": 20
  }
}
```

The app responds by:
- Scaling each participant's volume using a quadratic falloff: `volume = (1 - distance / maxDistance)²`
- Switching audio effects when `isUnderWater` or `isInCave` changes.
- Applying mute / deafen / volume changes from the `muteStates` array.

If no Minecraft data is received the app still functions as a standard voice room.

---

## ⌨️ Push-to-Talk

PTT is available on desktop browsers. To enable it:

1. In the call screen, check the **Push to Talk** checkbox.
2. Click the key button to assign a hotkey (default: `V`).
3. Hold the key to transmit; release to mute.

PTT overrides any mute state sent by Minecraft — your mic will only open while the key is held.

---

## 🐛 Debug Tools

Open the browser console and call these global helpers:

```js
debugAudio()       // Logs volume and distance state for all participants
testAudio()        // Plays a 440 Hz test tone for 2 seconds
diagnoseWebRTC()   // Prints ICE state, senders, and receivers for every peer connection
```

---

## 📄 License

[MIT](LICENSE) © 2025 Halo333X
