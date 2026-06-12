# Chromatic Weather

An [Owlbear Rodeo](https://www.owlbear.rodeo/) extension that adds colourful atmospheric particle effects to your maps. Attach an effect to any map, then tune its look in real time with a full control panel: ten effect types, per-effect colour presets, custom colours, and live sliders for particle count, speed, and size.

All visuals are GPU shaders (SkSL) rendered directly in the scene, so effects are smooth and lightweight.

## Effects

| Effect | Description |
| --- | --- |
| Embers | Rising, drifting sparks with layered size variation. |
| Bloom | Falling leaves with birch, maple, and oak shapes that spin, sway, and shrink as they descend. |
| Fireflies | Wandering glow points with species-based flash patterns. |
| Snow | Multi-size flakes with an optional blizzard whiteout at high counts. |
| Rain | Falling rain with a desaturated storm palette. |
| Top Down Rain | An overhead view where drops converge inward toward a centre zone, with splash rings. |
| Cloud | Soft drifting cloud cover that lightens the map rather than greying it out. |
| Fog | Roiling ground fog, from thin wispy tendrils up to thick rolling banks. |
| Dust | Slow floating motes that wander and twinkle. Suited to interiors, dungeons, and shafts of light. |
| Bubbles | Bubbles surfacing in water, rising toward the camera and popping at the surface. |

## Features

- Ten distinct particle effects, each independently tuned.
- Per-effect colour presets plus up to three fully custom colours.
- Live sliders for particle count, speed, and size.
- A direction control for effects where travel direction is meaningful (automatically greyed out for effects that ignore it, such as Fireflies, Top Down Rain, and Dust).
- Option to remember settings on a per map, and per effect basis
- A draggable control panel that floats over the map so you can see your changes as you make them.
- Grid-relative particle sizing, so particles stay a consistent real-world size regardless of how large the map is.

## Installation

In Owlbear Rodeo, open the extensions menu, choose to add a custom extension, and enter the manifest URL:

```
https://chromatic-weather.netlify.app/manifest.json
```

## Usage

1. Right-click a map on the Map or Fog layer and choose **Add Chromatic Weather**. This applies a default effect and opens the control panel.
2. Pick an effect type, choose a colour preset or set custom colours, and adjust the count, speed, and size sliders. Changes apply live.
3. To reopen the panel later, right-click the same map and choose **Chromatic Weather**.
4. Use **Remove Effect** in the panel to clear the effect from that map.

The control panel writes its settings into the map item's metadata. The background process watches that metadata and updates the visuals, so multiple people viewing the scene all see the same effect.

## Project structure

```
obr-chromatic-weather/
  manifest.json      Extension manifest read by Owlbear Rodeo.
  background.html    The engine: all shaders, the calibration tables, and the
                     reconcile logic that keeps effects in sync with item metadata.
  menu.html          The control panel UI shown in the popover.
  icon.svg           Extension icon.
  _headers           Host headers (for example for Netlify).
```

The two HTML files communicate only through the map item's metadata. `menu.html` writes settings; `background.html` reads them and renders. There is no build step; the files run as written.

## Customising and extending

The code is commented throughout in plain English. The most useful places to edit:

- **`menu.html` -> `EFFECTS`**: the list of effect buttons. Adding an entry here requires matching entries elsewhere (see below).
- **`menu.html` -> `PRESETS`**: the colour-preset chips per effect. Purely cosmetic; no shader changes needed.
- **`menu.html` -> `EFFECT_DEFAULTS`**: the starting count, speed, and size per effect.
- **`menu.html` -> `INERT_CONTROLS`**: which controls to grey out per effect.
- **`background.html` -> `CALIBRATION`**: converts the raw sliders into the values each shader expects. This is the usual place to tune how an effect feels. Calibration is per-effect, so changing one effect never affects another.

To add a new effect, create its `SHADER_` string in `background.html`, register it in the `SHADERS` map and `CALIBRATION` table, then add matching entries in `menu.html`'s `EFFECTS`, `PRESETS`, and `EFFECT_DEFAULTS`.

A note on editing shaders: each shader is a JavaScript template literal delimited by backticks, so a stray backtick inside a shader string (even in a comment) will end the string early and break the file. After editing, validate with `node --check` on the extracted script. Shaders only compile at runtime inside Owlbear Rodeo, so test changes in a live scene.

## Known limitations

- The fixed-count effects (Bloom, Dust, Bubbles) are tuned for rectangular maps. On irregular or hand-drawn shapes their particles can cluster or fail to appear, because an attachment effect fills the item's rectangular bounds and the particle placement does not yet account for the shape's transform. The space-filling effects (Embers, Fireflies, Snow, Rain, Cloud, Fog) work on any shape.

## Credits

Created by Pitrik. Built on the Owlbear Rodeo SDK.

This project derives from Owlbear Rodeo's official weather plugin, which is licensed under GPLv3. In particular, the Cloud and Fog effects were developed by studying and adapting the rendering approach of that plugin (its noise functions, domain warping, and layer structure). Copyright (C) 2024 Owlbear Rodeo for the original weather plugin portions.

## License

This project is licensed under the GNU General Public License v3.0 (GPLv3).

Because it is a derivative work of Owlbear Rodeo's GPLv3-licensed weather plugin, it is distributed under the same license. You are free to use, study, share, and modify it, provided that derivative works are also released under GPLv3 and source code remains available. See the `LICENSE` file for the full text.
