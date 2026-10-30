# V.I.K.T.O.R. — Standalone Edition

The floating desktop companion with **its own brain**. No Hermes, no external
agent, no extra services: this edition ships **Viktor Core** inside the repo
and starts it together with the app.

- 🐕 Animated Shiba mascot overlay (13 poses, idle antics, speech bubble)
- 🎙️ Fully local voice: "Hey Viktor" wake word, faster-whisper STT,
  Chatterbox cloned voice (optional) or Windows TTS
- 🧠 **Switchable brain:** **Groq** (cloud, fast) ⇄ **Ollama** (local, offline)
- 🛠️ Agent tools: open apps & pages, run commands, read/write files,
  web search/fetch, media keys, long-term memory notes
- 🔒 Core listens on 127.0.0.1 only; destructive shell commands are refused

This is the standalone sibling of [subasmk/viktor](https://github.com/subasmk/viktor)
(the Hermes-powered edition). Same mascot, same voice — different brain.

## Quick start

```bat
npm install
npm start
```

Viktor Core starts automatically with the app (it runs on Electron's own
Node — no separate install). Then pick your brain:

- **Groq (recommended for voice speed):** get a free key at
  [console.groq.com](https://console.groq.com) → API Keys, open
  **http://127.0.0.1:8731/** in a browser and paste it. Done.
- **Ollama (fully offline):** install from [ollama.com](https://ollama.com),
  run `ollama pull llama3.1:8b` once, then switch the provider on the same
  settings page.

Switching applies to the next message — no restart. If Core is ever not
running, `start-core.bat` starts it on its own.

## Using Viktor

- **Click the mascot** — mic on/off. First command needs no wake phrase;
  later ones start with **"Hey Viktor"**. Say **"stop"** to cut speech off.
- **Right-click the mascot** or **Ctrl+B** — the console (chat, notes,
  settings). Persona and the proactive scout are OFF by default and stay
  opt-in, in Settings.
- Voice needs Python for the watcher (see `voice/`); the console and Core
  work without it.

## How it fits together

```
mascot/console (Electron) ──► Viktor Core :8731 ──► Groq  (cloud LLM)
                              (agent loop + tools) └─► Ollama (local LLM)
voice watcher (Python) ─────► same endpoint, same brain
```

Core is an OpenAI-compatible service (`core/server.mjs`): `GET /v1/models`,
`POST /v1/chat/completions` (streaming supported), settings page at `/`.
Conversations persist as JSONL in `core/data/`; `remember`/`recall` notes
live in `core/data/notes.txt`. The agent loop runs up to 6 tool rounds per
message. `run_command` refuses a denylist of destructive patterns
(formatting, recursive system deletes, shutdown, registry deletes) — it is
still your PC, so ask it for things you would run yourself.

## Safety notes

- Core binds to **127.0.0.1 only**, because it can run commands. Do not
  change the bind address.
- The Groq key lives only in `core/config.json` on your machine.

## Packaging note

`npm start` (dev mode) is the supported run. If you package an .exe, add
`core/` as `extraResources` (like `voice/` in the Hermes edition) so the
spawned Core script is readable outside the asar.
