<p align="center">
  <img src="https://img.shields.io/badge/Tauri-2-24C8DB?logo=tauri&logoColor=white" alt="Tauri 2">
  <img src="https://img.shields.io/badge/Rust-1.77%2B-000000?logo=rust&logoColor=white" alt="Rust 1.77+">
  <img src="https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey" alt="Windows, macOS, Linux">
  <img src="https://img.shields.io/badge/status-private-red" alt="Status: private">
</p>

# dria-desktop

**dria as a small always-on-top study window for Windows, macOS and Linux.**

dria is an AI study assistant. This repo is its cross-platform desktop shell:
a compact 420×560 chat window that floats above whatever you are studying and
hides in the system tray when you don't need it.

- **Screenshot → ask.** Capture the screen and send it to the model with your question.
- **Paste → ask.** Pull the clipboard in as context in one click; an "Auto" mode watches the clipboard for new questions.
- **Talk → ask.** Voice input through the WebView's built-in speech recognition, where the platform provides it.
- **Bring your own model.** Google AI, Claude, OpenAI, Groq, Mistral, OpenRouter, or a local Ollama.

The last 50 messages are kept locally so a session survives a restart.

## Quick start

You need Node.js, a Rust toolchain (1.77.2+), and the
[Tauri 2 system prerequisites](https://v2.tauri.app/start/prerequisites/) for your OS.

```bash
git clone https://github.com/angelonrevelo/dria-desktop.git && cd dria-desktop
npm install
npx tauri build      # installers land in src-tauri/target/release/bundle/
```

`npx tauri dev` expects the UI at `http://localhost:3000` (`devUrl` in
`src-tauri/tauri.conf.json`), and the repo does not start a server for it —
serve the `src/` folder on port 3000 yourself before running dev mode.

The app starts hidden: click the tray icon to show or hide the window.

## Configuration

There is no `.env`. Open **Settings** (⚙) in the window and pick a provider,
model, and API key. They are stored in the WebView's local storage on this
machine — never in the repo. Ollama needs no key; it talks to
`http://localhost:11434`.

## How it works

```
 tray icon ──click──► floating window (src/: plain HTML + CSS + JS)
                          │  invoke()
                          ▼
                 Rust side (src-tauri/src/lib.rs)
                 capture_screen · clipboard read/write · read_file_text
                          │
                          ▼
           chosen AI provider's HTTP API (direct from the window)
```

- `src/` — the whole UI, no build step.
- `src-tauri/` — the Tauri app: window, tray, native commands, bundle config and icons.

The keyboard hints in the empty state (Ctrl+Alt+1/2/3) are not registered as
global shortcuts yet; use the buttons.
