# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-file frontend UI reference/demo site (`ui-elements-lab.html`). Catalogs the 32 UI elements from the CareerFoundry UI glossary (https://careerfoundry.com/en/blog/ui-design/ui-element-glossary/), each with a live preview plus its own HTML/CSS/JS source tabs. Purpose (from `README.md`): a portfolio/demo site for learning frontend component terminology and reusing elements in future projects.

No package manager, build step, linter, or test suite — it's one static `.html` file. No git repo (`git status` will not work here).

## Running / verifying changes

Open `ui-elements-lab.html` directly in a browser, or serve it locally:

```
python3 -m http.server 8934
# then open http://localhost:8934/ui-elements-lab.html
```

Note: the claude-in-chrome browser tool refuses `file://` URLs ("browser-internal or unparseable URLs") — serve over local HTTP to test with it.

## Architecture

Everything lives in `ui-elements-lab.html`: inline `<style>` for site chrome, inline `<script>` for data + logic. No external JS/CSS dependencies except Google Fonts (Fraunces, JetBrains Mono).

- `CATEGORY_ORDER` — the 6 fixed category labels, in display order: `Structure & Navigation`, `Menus`, `Actions & Controls`, `Input & Forms`, `Content Display`, `Feedback & Status`.
- `DATA` — array of 32 objects (`{name, category, desc, html, css, js}`), one per UI element, already sorted in category order. Each item's `html`/`css`/`js` is a **fully self-contained** component: it must not depend on the page's own chrome styles, because it's rendered in isolation.
- `renderMenu(filter)` — rebuilds the left nav, grouped by `CATEGORY_ORDER`, filtered by the search box; hides a category header entirely if no items match.
- `render(index)` — sets title/category/description/counter, sets the preview `<iframe>`'s `srcdoc` via `buildDoc(item)`, and calls `showCode()`.
- `buildDoc(item)` — wraps `item.css` + `item.html` + `item.js` into a full HTML document string for the sandboxed iframe (`sandbox="allow-scripts allow-same-origin"`). The iframe auto-resizes its height from `contentDocument.body.scrollHeight` on load.
- `showCode()` — renders whichever of `item.html` / `item.css` / `item.js` matches the active tab, HTML-escaped, into the code panel. **The visible tab must always match what the iframe actually renders** — this was previously a bug (CSS/JS tabs showed one generic placeholder snippet for every item regardless of selection); don't reintroduce that.
- Selected tab (`activeTab`) persists across navigating between items.

### Adding a new element

Append an object to `DATA` in the correct category position (keeps left-nav numbering and grouping correct). Give the component's `html`/`css`/`js` unique-enough class names for clarity, but collisions with other items' class names are harmless — each item renders in its own sandboxed iframe. If an element genuinely needs no JS, put an explanatory Korean comment in `js` (e.g. `// 정적 콘텐츠로 자바스크립트가 필요하지 않습니다.`) rather than leaving it blank or faking behavior.

## Design direction ("anti-slop")

Per `Prompt.md`, deliberately avoid typical AI-generated design tropes. Current implementation:

- Fonts: Fraunces (serif, headings/body) + JetBrains Mono (UI chrome, code, labels) — not Inter/Poppins/system-ui.
- Bright warm background (`#f3eee1`), near-black ink text, high contrast throughout.
- Hard 2px solid borders, offset (non-blurred) drop shadows, `border-radius: 0` except on functionally circular elements (avatars, dots, spinner).
- Single accent pair: orange-red (`--accent`) for chrome/active states, yellow (`--accent-soft`) for hover fills. No purple/blue gradients.
- No fake macOS traffic-light dots or other decorative browser-chrome clichés in the preview header.

Keep new components and any chrome changes consistent with this system rather than reverting to generic rounded-card/soft-shadow/gradient styling.
