# Jarvis – AI Voice Desktop Assistant

Jarvis is a Python-based, voice-controlled desktop assistant built as a 4th-semester project. It combines speech recognition, text-to-speech, face authentication, and phone automation into a single assistant that runs in a lightweight desktop GUI (powered by [Eel](https://github.com/python-eel/Eel)).

Say the wake word, get authenticated by your face, and then control your PC and phone with your voice — open apps and websites, play YouTube videos, send WhatsApp messages/calls, send SMS or make calls on a connected Android device, and chat with an AI chatbot.

## Features

- **Face Authentication** – Uses OpenCV's LBPH face recognizer (with Haar Cascade detection) to unlock the assistant before it responds to commands.
- **Wake Word Detection** – Listens for "Jarvis" or "Alexa" in the background using Picovoice Porcupine.
- **Voice Commands** – Speech-to-text via Google's speech recognition API, and text-to-speech responses via `pyttsx3`.
- **App & Website Launcher** – Opens installed applications or websites by name, backed by a local SQLite database (`jarvis.db`).
- **YouTube Playback** – Searches and plays videos directly on YouTube via voice command.
- **WhatsApp Automation** – Sends messages, and starts voice/video calls over WhatsApp using contact lookup + UI automation.
- **Android Phone Control (via ADB)** – Makes phone calls and sends SMS messages on a connected Android device over ADB.
- **AI Chatbot Fallback** – Any command that doesn't match a known action is routed to a chatbot (via HugChat) for a conversational response.
- **Simple Web-based GUI** – A browser-based front end (HTML/CSS/JS) served locally and launched automatically in an app-mode browser window.

## Tech Stack

| Layer | Technology |
|---|---|
| Core language | Python 3 |
| Desktop/Web bridge | Eel |
| Speech-to-Text | SpeechRecognition (Google API) |
| Text-to-Speech | pyttsx3 |
| Wake word detection | Picovoice Porcupine |
| Face recognition | OpenCV (LBPH + Haar Cascade) |
| Automation | PyAutoGUI, ADB (Android Debug Bridge) |
| Chatbot | HugChat |
| Database | SQLite |
| Frontend | HTML, CSS, JavaScript |

## Project Structure

```
Jarvis/
├── main.py                # App entry point, face-auth flow, Eel init
├── run.py                 # Launches Jarvis + hotword listener as parallel processes
├── device.bat              # Connects an Android device to ADB over Wi-Fi
├── jarvis.db               # SQLite DB: app paths, website shortcuts, contacts (not committed)
├── engine/
│   ├── command.py          # Speech recognition + command router
│   ├── features.py         # Core feature implementations (open apps, YouTube, WhatsApp, chatbot, ADB actions)
│   ├── config.py            # Assistant name/config
│   ├── helper.py            # Utility functions
│   └── auth/
│       ├── recoganize.py    # Real-time face authentication
│       ├── trainer.py       # Trains the LBPH model on sample face images
│       └── haarcascade_frontalface_default.xml
└── www/                     # Frontend UI (index.html, script.js, style.css, assets)
```

## Prerequisites

- Windows OS (uses `msedge.exe`, `.bat` scripts, and `sapi5` TTS voices — this project is Windows-specific)
- Python 3.9+
- A working webcam (for face authentication)
- A working microphone
- [ADB](https://developer.android.com/tools/adb) installed and on your PATH (for Android automation features)
- A WhatsApp Desktop installation (for WhatsApp automation)
- A free [Picovoice](https://picovoice.ai/) account/access key (for wake word detection)

## Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-username>/jarvis.git
   cd jarvis
   ```

2. **Create a virtual environment and install dependencies**
   ```bash
   python -m venv envjarvis
   envjarvis\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Set up the database**
   `jarvis.db` is not committed to the repo. Create it locally with the following tables before first run:
   - `sys_command(name, path)` — maps a spoken app name to its executable path
   - `web_command(name, url)` — maps a spoken name to a website URL
   - `contacts(name, mobile_no)` — contact list used for WhatsApp/call/SMS commands

4. **Set up face authentication**
   - Add your face images to `engine/auth/samples/` following the naming convention `face.<id>.<num>.jpg`
   - Update the `names` list in `engine/auth/recoganize.py` to match your ID mapping
   - Run `engine/auth/trainer.py` to generate `engine/auth/trainer/trainer.yml`

5. **Configure the chatbot**
   - HugChat requires a `cookies.json` file in `engine/` for authentication (not committed to the repo — see [HugChat docs](https://github.com/Soulter/hugging-chat-api) for how to generate one).

## Usage

```bash
python run.py
```

This starts Jarvis and the background hotword listener as separate processes. A browser window opens automatically; complete face authentication when prompted, then start speaking commands, e.g.:

- "Jarvis, open Chrome"
- "Play [song name] on YouTube"
- "Send message to [contact name]"
- "Jarvis, phone call [contact name]"

## Known Limitations

- Windows-only (relies on `sapi5`, `.bat` files, and Edge app-mode launch)
- WhatsApp/contact automation relies on fixed UI coordinates and tab counts, which are sensitive to screen resolution and app version
- Face recognition accuracy depends on lighting and sample image quality
- No error handling around missing hardware (webcam/mic) or missing ADB connection

## Security & Privacy Note

This project trains a face-recognition model on real photos. If you fork or reuse this project, do **not** commit your own (or anyone else's) face images, trained model files, contact lists, or chat cookies to a public repository — keep them in `.gitignore` as shown here.

## Acknowledgments

Built as a 4th-semester academic project, combining computer vision, speech processing, and desktop/mobile automation into a single assistant.
