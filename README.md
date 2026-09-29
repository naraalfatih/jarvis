# KESTREL — Holographic Tactical Interface

A single-file (`index.html`), zero-dependency, voice-controlled holographic assistant UI.
No build step, no external fonts, images, scripts or libraries.

## Run it

```bash
python3 -m http.server 8000     # any static server works
# open http://localhost:8000 and press INITIALIZE
```

Serving over `localhost`/`https` is recommended: browsers remember the microphone
and location permission there, whereas `file://` may re-prompt on every recognition restart.
Best in Chrome, Edge or Safari (they ship the Web Speech API). Firefox has no speech
recognition — the on-screen command line and command chips work everywhere.

## Voice commands

Just say them (optionally prefixed with "Kestrel"), or type them:

| Say | Does |
| --- | --- |
| "show weather" | Local conditions + 12 h chart. Uses geolocation + [Open-Meteo](https://open-meteo.com) (no key). If the location/network is unavailable it falls back to data that is **clearly labelled and spoken as simulated**. |
| "run diagnostics" | Animated subsystem check mixing real readings (fps, mic/synth support, memory, battery, connectivity) with simulated ones |
| "scan system" | Radar sweep + real environment report (cores, memory, display, locale, network, battery, mic permission) |
| "locate me" | Pins your position on the globe |
| "what time is it" | Chronometer panel |
| "red alert" / "stand down" | Retints the whole interface amber-red / back to cyan |
| "globe" | Planetary view — pings every node |
| "clear panels", "mute" / "unmute", "help" | Housekeeping |

Un-addressed chatter that matches no command is ignored silently; if you address it
("Kestrel, …") it tells you it has no protocol for that.

## How it works

* **Scene** — one Canvas 2D layer: rotating concentric rings, two tilted 3-D gyro orbits, a
  wireframe globe with land dots (an embedded 2° Natural Earth bitmask, ~2.7 KB) and great-circle
  data arcs, a perspective floor grid, particles, and an additive-blended core.
* **Voice-reactive core** — while *you* speak, the core follows the real microphone level
  (`AnalyserNode`). While *Kestrel* speaks, `speechSynthesis` exposes no audio, so it is driven by a
  syllable-rate envelope kicked by the utterance's word-boundary events.
* **HUD** — DOM panels with a glitch/scan-line entry (`clip-path` + transform keyframes, sweep bar),
  a smooth-scrolling telemetry stream, and canvas strip-displacement glitch on command.
* **Echo safety** — recognition results are ignored while the assistant is talking (plus a short
  cool-down and a spoken-text echo check).
* **Performance** — per-frame JS is ~2 ms. Glow uses cached sprites (no `shadowBlur`), depth bucketed
  batch fills, and a single static CRT layer. If frame time exceeds ~24 ms it steps the render
  resolution down (and back up if calm). Append `#hq` to the URL to pin full resolution.

`prefers-reduced-motion` disables the CRT roll and screen glitches.
