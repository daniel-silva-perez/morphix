# MORPHIX

Live GLSL shader playground. Edit fragment shaders in real-time with instant preview, 8 built-in presets, shareable URLs, and a full suite of Shadertoy-style uniforms.

```
    ╭──────────────────────────────────────────╮
    │  ◈ MORPHIX ◈                            │
    │  Live GLSL Playground                    │
    │                                          │
    │  8 presets:                              │
    │  Wave Sin · Mandelbrot · Plasma          │
    │  Raymarcher · Voronoi · Galaxy           │
    │  Reaction-Diff · Warp Lens                │
    │                                          │
    │  Shadertoy-style uniforms:              │
    │  iResolution · iTime · iMouse            │
    │  iFrame · iTimeDelta · iFrameRate        │
    │  iSampleRate · iDate · iChannel*         │
    │                                          │
    │  Ctrl+Enter to compile                   │
    │  Tab to insert 2 spaces                   │
    │  Share button → encode code in URL       │
    ╰──────────────────────────────────────────╯
```

## Features

- **8 built-in presets** — Wave Sin, Mandelbrot (with click-to-zoom), Plasma, Raymarcher (fractal mountains), Voronoi (cellular noise), Galaxy (spiral arms), Reaction-Diffusion (Gray-Scott), Warp Lens (chromatic aberration tunnel)
- **Live editing** — `contenteditable` div as code editor, Tab inserts 2 spaces, `Ctrl+Enter` compiles
- **Shadertoy uniform suite** — `iResolution`, `iTime`, `iMouse`, `iFrame`, `iTimeDelta`, `iFrameRate`, `iSampleRate`, `iDate`, `iChannelTime[4]`, `iChannelResolution[4]`, `iChannel0–3`
- **Error bar** — GLSL compile errors shown in red bar below editor
- **Share system** — `share()` base64-encodes current code and writes to URL `?code=...`; loading a URL with `?code=` restores the shader
- **Mouse interaction** — `iMouse.z > 0` when mouse is down; Mandelbrot preset uses this for click-to-zoom
- **Preset chips** — click to instantly load a new preset, current highlighted in green

## Shader Format

Shaders use a `#define mainImage(O,FC)` convention — write the function body and the framework wraps it:

```glsl
void mainImage(out vec4 O, in vec2 FC) {
    vec2 uv = (FC - 0.5*iResolution.xy) / iResolution.y;
    float t = iTime * 0.8;
    float d = length(uv);
    float wave = sin(d * 20.0 - t * 4.0) * 0.5 + 0.5;
    O = vec4(col, 1.0);
}
```

## Controls

| Input | Effect |
|-------|--------|
| `Click preset chip` | Load that shader |
| `Edit code` | Modify active shader |
| `Ctrl+Enter` | Compile current shader |
| `▶ RUN button` | Compile current shader |
| `↺ TIME button` | Reset `iTime` to 0 |
| `⎘ SHARE button` | Encode + copy share URL |
| `Click canvas` | Send `iMouse.z > 0` to shader (interaction) |

## Tech Stack

- **Single HTML file** — no build step
- **WebGL** — raw `canvas.getContext('webgl')`, manual shader compilation + linking
- **No libraries** — pure WebGL, no Three.js
- **Custom editor** — `contenteditable` div

## Architecture

```
Morphix class
  ├─ buildQuad() — full-screen triangle via gl.TRIANGLE_STRIP
  ├─ compile() — vertex + fragment shader, link, bind aPos attribute
  ├─ getFragCode() — prepend FS_HEADER, append FS_WRAPPER
  ├─ setPreset(name) — load PRESETS[name] into editor
  ├─ loop() — update uniforms (time, mouse, date, frame, fps), draw
  └─ share() — btoa(code) → URL param

Uniforms set each frame:
  iResolution, iTime, iMouse[4], iTimeDelta, iFrame,
  iFrameRate (= 1/dt), iDate[4], iSampleRate, iChannelTime[4], iChannelResolution[4]
```

## License

MIT