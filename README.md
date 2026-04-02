# Lunar Phases

WebGL scene: textured **Moon** in a **starfield**, with a **phase slider** (0–1) that moves a **directional light** around the orbit so the terminator matches a simplified lunar-phase cycle. **OrbitControls** let the user rotate, pan, and zoom the view.

**Live site:** [https://content-interactives.github.io/lunar-phases](https://content-interactives.github.io/lunar-phases)

CK-12 placement and NGSS alignment: [Standards.md](Standards.md).

---

## How interaction works

| Control | Effect |
|---------|--------|
| **Range slider** (`0`–`1`, step `0.01`) | Drives `phase` state. Label maps `floor(phase * 8)` to names in `LUNAR_PHASES` (New Moon through Waning Crescent). At `phase === 1`, label shows **New Moon** (wrap). |
| **OrbitControls** | Rotate / pan / zoom the Three.js camera around the scene. |
| **Moon rotation** | `useFrame` slowly increments `rotation.y` on the Moon mesh for idle motion. |

Lighting: main **directional** light position is derived from `phase` (angle offset `-0.25` turns); intensity uses **`getLightIntensity`** (peaks near `phase === 0.5` full moon). A weak opposite **fill** directional light plus **ambient** keep the dark limb slightly visible.

---

## Stack

| Layer | Packages |
|--------|-----------|
| Runtime | React 19 |
| 3D | **three** ~0.174, **@react-three/fiber** 9, **@react-three/drei** ( `OrbitControls`, `Sphere` ) |
| Language | TypeScript 5.7 |
| Build | Vite 6, `@vitejs/plugin-react` |
| Lint | ESLint 9 + typescript-eslint |

Styling is plain **CSS** (`App.css`, `index.css`)—there is **no** Tailwind setup in this repo despite older notes elsewhere.

---

## Source layout

```
src/
  main.tsx           # createRoot, StrictMode
  App.tsx            # Canvas, Skybox, MoonSphere, PhaseControls, all phase logic
  App.css            # .phase-controls overlay (fixed bottom-center)
  index.css          # Global layout, #root full viewport
  textures/          # Expected by imports (see below)
embed-code.txt       # Sample 500×500 iframe snippet for hosts
vite.config.ts       # Default base `/` — set base for GitHub project Pages if assets 404
```

---

## Assets

`App.tsx` imports:

- `./textures/8k_moon.jpg` — diffuse map on `meshStandardMaterial`
- `./textures/8k_stars.jpg` — equirectangular-style use on an inverted `Sphere` skyball (`meshBasicMaterial`, `side: 1` = `BackSide`)

If those files are missing locally, the build will fail until they are added under `src/textures/` (or imports are updated). Large textures may be omitted from git; document their source in your team wiki.

---

## Build and deploy

| Command | Purpose |
|---------|---------|
| `npm install` | Install dependencies |
| `npm run dev` | Vite dev server |
| `npm run build` | `tsc -b` then Vite → `dist/` |
| `npm run preview` | Serve `dist/` |
| `npm run lint` | ESLint |

There is **no** `gh-pages` (or similar) script in `package.json`; publish `dist/` with your usual Pages workflow.

**`vite.config.ts`** does not set `base`. If the app is served from a subpath (e.g. `https://user.github.io/lunar-phases/`), set `base: '/lunar-phases/'` (or the correct slug) so hashed assets resolve.

---

## Embedding

See **`embed-code.txt`** for a **500×500** iframe example. The app itself uses **full viewport** (`100vw` / `100vh`); inside a small iframe the canvas scales down and controls remain usable.

---

## Maintenance ideas

- Phase naming at `phase === 1` duplicates New Moon at `phase === 0`; intentional wrap for slider end—document if you change step bounds.
- **StrictMode** double-invokes effects in dev; texture loading uses `useLoader` (should be fine).
- Remove unused Vite template rules from `App.css` / `index.css` if you want a minimal stylesheet.
