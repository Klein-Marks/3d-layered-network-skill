# 3D Layered Network Skill

Turn any layered system (architecture layers, pipelines, governance chains) into an interactive single-file 3D network visualization. Layers stack along the Z axis, main nodes expand into child nodes on click, every edge carries a semantic verb label, and a day/night theme pair ships by default.

Zero dependencies. Open the HTML files directly in a browser.

## What you get

- **Main-node skeleton first**: only the N layer cores are visible initially, connected by logic-word edges (调度/schedule, 沉淀/persist, 回卷/rollback, ...). Click a main node to expand its children.
- **Logic-word edges**: every edge renders a small label at its midpoint (translated along the edge normal, always horizontal and readable).
- **Progressive detail panel**: four-segment explanations per node — function / internal design logic / role in the workflow / trigger conditions.
- **Day & night themes**: dark default + beige daylight theme, node colors switch per theme, CSS-variable driven.
- **Guaranteed centering**: the 3D cloud stays screen-centered at any rotation angle (mean-center projection + bounding-box shift), a fix that survives perspective asymmetry.

## Files

```
SKILL.md                    The methodology as a reusable skill (Chinese)
examples/
  workflow-network.html     Full reference: 6-layer AI workflow, 38 nodes
  cicd-network.html         Feasibility demo: 4-layer CI/CD pipeline
```

## Quick start

1. Open `examples/workflow-network.html` in a browser. Drag to rotate, wheel to zoom, click a main node to expand/collapse its layer, click any node for details, double-click to focus, toggle ☀ for the daylight theme.
2. To build your own network, follow the five-step flow in `SKILL.md` (layer mapping → logic words → layout → interaction → themes), or copy an example and replace the data blocks (`G`, `N`, `E`, `INFO`).

## Theory

Sugiyama layered graph drawing (IEEE TSMC 1981) + radial tree layout + 3D layered extension (Hong & Nikolov 2005). Each layer occupies a Z plane; nodes within a layer sit on a concentric circle; main nodes spiral around the Z axis to avoid overlap.

## Known gotchas (documented in SKILL.md)

- Perspective must anchor to the moving cloud center, never absolute z=0 (nodes in negative z get magnified 2-3x and pushed off-screen).
- The scene container must stay `absolute;inset:0` — overriding position in JS collapses its height to 0 and throws every node above the viewport.
- `user-select:none` on the scene is required, or drag-rotate selects text.

## License

MIT
