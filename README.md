# SHTTS Web Synthesizer

Runs the SHARP SHTTS v1.1.1 TTS engine from Shaberu! DS Oryouri Navi (NDS, 2006) in a browser. You give it the ROM (or just the arm9.bin), type katakana with prosody markers, and it speaks.

The engine is a PARCOR lattice-filter synthesizer baked entirely into the game's ARM9 binary (~180KB of voice model data, ~44KB of synthesis code). This tool loads that binary into [Unicorn.js](https://github.com/AlexAltea/unicorn.js) (an ARM emulator compiled to JS), stubs out the NDS OS calls, patches `SHTTS_Speak` for synchronous execution, and collects the PCM output. 12 voices, 16 emotions, adjustable speed/pitch/volume. Output is 22050 Hz 16-bit mono.

No server, no backend. Everything runs client-side.

## Usage

1. Open `tts_web.html` in a browser
2. Load your ROM (.nds) or extracted arm9.bin
3. Type katakana text with prosody markers, pick a voice/emotion, hit Synthesize

You need a legally-obtained copy of the game. No ROM data is included here.

## Prosody markers

The TTS engine uses full-width katakana with these inline markers:

| Marker | What it does |
|--------|-------------|
| `'` | Accent nucleus -- pitch drops after the preceding mora |
| `２` | Word/phrase boundary |
| `０` | Falling intonation (sentence-final) |
| `％` | Devoices the preceding mora |
| `／` | Accent phrase boundary |
| `＿` | Pause. Stack them for longer pauses: `＿＿＿` |
| `．` | End of sentence (declarative) |
| `！` | End of sentence (exclamatory) |
| `？` | End of sentence (interrogative, rising pitch) |

Examples:

```
コンニチワ．
シャベ'ル＿＿＿ディーエス＿オリョーリナビ．
ヨ'２ーコソ＿シャベ'２ル＿ディーエス２＿オリョーリ２／ナ'２ビエ！
```

## How it works

The emulator maps arm9.bin at 0x02000000 (same as real NDS hardware), allocates an 80KB context on a fake heap, and stubs 5 OS functions (mutex lock/unlock, thread create/start, sleep) as no-ops. A patch at 0x0202C740 forces synchronous synthesis so we don't need to emulate the NDS threading model. An audio callback stub in ITCM space copies PCM chunks into an accumulation buffer as the engine calls it repeatedly with 480-sample blocks. When `SHTTS_Speak` returns, the buffer gets wrapped in a WAV header for playback and download.

The function addresses and voice/emotion enum values were found by reverse engineering arm9.bin in Ghidra.

## Legal

SHARP SHTTS v1.1.1 (the TTS engine, voice models, and synthesis code) is proprietary to SHARP Corporation. Shaberu! DS Oryouri Navi is (C) 2006 Nintendo / indieszero. This repository contains none of that -- users supply their own ROM at runtime. The internal addresses and API constants come from reverse engineering for interoperability purposes. Personal and educational use only.

## License

GPLv2, because Unicorn.js is GPLv2.

```
Copyright (C) 2025 shtts-web contributors

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.
```

| Component | License | Source |
|-----------|---------|--------|
| Unicorn Engine | GPLv2 | [unicorn-engine/unicorn](https://github.com/unicorn-engine/unicorn) |
| Unicorn.js (JS port) | GPLv2 | [AlexAltea/unicorn.js](https://github.com/AlexAltea/unicorn.js) |
