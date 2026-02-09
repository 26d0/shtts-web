# SHTTS Web Synthesizer

Browser-based frontend for the **SHARP SHTTS v1.1.1** text-to-speech engine from *Shaberu! DS Oryouri Navi* (Nintendo DS, 2006).

Runs the original ARM binary code in-browser via [Unicorn.js](https://github.com/AlexAltea/unicorn.js) (Unicorn Engine compiled to JavaScript with Emscripten). No server required -- everything runs client-side.

## Features

- All 12 voice types (male, female, child, elderly, character voices)
- All 16 emotion presets (happy, calm, angry, whisper, etc.)
- Adjustable speed, pitch, and volume
- Accepts katakana text with SHTTS prosody markers
- Outputs 22050 Hz 16-bit mono PCM (playback + WAV download)
- Loads ROM (.nds) or raw arm9.bin directly

## Usage

1. Open `tts_web.html` in a browser
2. Load your own legally-obtained copy of *Shaberu! DS Oryouri Navi* ROM or its extracted `arm9.bin`
3. Enter katakana text with prosody markers (e.g. `コンニチワ．`)
4. Select voice, emotion, and parameters
5. Click **Synthesize**

### Prosody markers

| Marker | Function |
|--------|----------|
| `'` | Accent nucleus (pitch drops after preceding mora) |
| `２` | Word/phrase boundary |
| `０` | Falling intonation / sentence-final |
| `％` | Devoicing |
| `／` | Accent phrase boundary |
| `＿` | Pause (multiple for longer) |
| `．` | Sentence end (declarative) |
| `！` | Sentence end (exclamatory) |
| `？` | Sentence end (interrogative) |

### Example inputs

```
コンニチワ．
シャベ'ル＿＿＿ディーエス＿オリョーリナビ．
ヨ'２ーコソ＿シャベ'２ル＿ディーエス２＿オリョーリ２／ナ'２ビエ！
```

## Requirements

- A modern browser with JavaScript enabled
- Your own legally-obtained ROM file

**No ROM, game data, or proprietary code is included in this repository.**

## File structure

```
shtts-web/
├── LICENSE                  # GPLv2
├── README.md
├── tts_web.html             # Main application
└── lib/
    └── unicorn-arm.min.js   # Unicorn.js ARM build (GPLv2)
```

## How it works

The NDS game contains SHARP's SHTTS (SH Text-to-Speech) v1.1.1 engine entirely within its ARM9 binary -- a PARCOR lattice-filter speech synthesizer with ~180KB of baked-in voice model data. This tool:

1. Loads the ARM9 binary into a Unicorn Engine (ARM emulator) instance
2. Stubs 5 NDS OS functions (mutex, threading, sleep) as no-ops
3. Patches `SHTTS_Speak` for synchronous execution
4. Calls the original `SHTTS_Init`, `SHTTS_SetProperty`, and `SHTTS_Speak` functions
5. Collects PCM output via an audio callback stub
6. Wraps the result in a WAV container for playback/download

## Legal notice

- **SHARP SHTTS v1.1.1**: The TTS engine, voice model data, and synthesis code are the proprietary property of **SHARP Corporation**.
- **Shaberu! DS Oryouri Navi**: (C) 2006 Nintendo / indieszero. All rights reserved.
- This repository contains **no copyrighted game data, engine code, or voice models**. Users must supply their own legally-obtained ROM or arm9.bin file.
- For **personal, non-commercial, research and educational use only**.
- Internal function addresses and API constants were obtained through reverse engineering for interoperability research purposes.

## License

This project is licensed under the [GNU General Public License v2.0](LICENSE) (GPLv2), due to its dependency on [Unicorn.js](https://github.com/AlexAltea/unicorn.js) which is GPLv2-licensed.

```
Copyright (C) 2025 shtts-web contributors

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.
```

### Third-party components

| Component | License | Source |
|-----------|---------|--------|
| Unicorn Engine | GPLv2 | [unicorn-engine/unicorn](https://github.com/unicorn-engine/unicorn) |
| Unicorn.js (JS port) | GPLv2 | [AlexAltea/unicorn.js](https://github.com/AlexAltea/unicorn.js) |
