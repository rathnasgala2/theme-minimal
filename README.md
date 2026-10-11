# @rathnasgala2/theme-minimal

The Minimal presentation theme for Galascribe portable publications
(`@rathnasgala2/template@^4.0.0`): monochrome, quiet, hairline rules and
generous whitespace. It is used only when a publication's own
`appearance.theme` names it; the build fallback is
`@rathnasgala2/theme-default`. This package is a **closed, passive file
set**: JSON and CSS only, no JavaScript, no build step, no runtime
dependency, no lifecycle script.

In generation 4 the template owns page layout and component structure. This
theme selects the Personal composition and supplies its established
116-token Minimal palette plus a small amount of skin CSS.

## Composition

`theme.json` embeds the complete generation-4 Personal composition:

- chronological home
- prose article
- biography author page
- dated-row listings
- compact header and footer
- landscape media
- spacious density

## Files

- `package.json`: exactly `name`, `version`, `license`, `repository`, `files`.
- `theme.json`: validated against `urn:gala:schema:theme-contract:2.0.0`. It
  carries the contract-4 identity and Personal composition,
  `templateRange` (`^4.0.0`), the stylesheet/layer/hook projection, both
  palettes for all 116 tokens, asset digests, size budgets, and the
  generation-4 `stylingContractDigest`.
- `tokens.css`: generated from `theme.json` by `tokens:generate`. Never edit
  it by hand; `tokens:check` fails if it drifts from `theme.json`.
- `components.css`: a small skin layer (cards, chips, avatars, strong text)
  that uses tokens only.
- `print.css`: print rules.
- `LICENSE`, `README.md`.

## Visual character

Monochrome ink on white (near-black on near-black in dark mode), no accent
hue, no shadows, no page decoration. Cards are borderless with no inset;
sections are separated by hairline row dividers. Images render greyscale.
Labels are lower-case monospace; headings are lighter (620 and 600) with
tighter radii (4 to 8px). Code blocks are a pale grey in light mode.

## Commands

Node 24.18.0 (see `.nvmrc`). `tooling/` is private and never packed; every
gate lives in `@rathnasgala2/theme-tooling` and is reached through
`tooling/run.mjs`.

```sh
npm --prefix ../theme-tooling run vendor:check
npm --prefix ../theme-tooling ci
GALA_THEME_TOOLING_DIR=../../theme-tooling npm --prefix tooling run tokens:generate
GALA_THEME_TOOLING_DIR=../../theme-tooling npm --prefix tooling run digest:generate
GALA_THEME_TOOLING_DIR=../../theme-tooling npm --prefix tooling run verify
```

Re-run `digest:generate` after editing `theme.json`, `tokens.css`,
`components.css` or `print.css`. It recomputes asset digests and the chain
`fixtureDigest`, `evidenceDigest`, `integrity`.

Only theme tooling is consumed from an explicit source checkout. Before
installation, it verifies its committed Schema 3.8.0, Template 4.0.0, and six
Theme 4.0.0 archives against `vendor/manifest.json` and the lockfile; Schema
and Template then resolve from those integrity-checked installed archives.
`GALA_SCHEMA_DIR` and `GALA_TEMPLATE_DIR` are development overrides, not part
of this flow. None is a runtime dependency, and these commands do not publish
the package.

## Rules

CSS is limited to the root scope and the template's published styling hooks,
contains no `@import`, external `url()`, `outline: none` or script
constructs, and every stylesheet is one `@layer` block matching `theme.json`.
Contrast (WCAG 2.2 AA) is checked for both palettes by `contrast:check`.
The shared tooling README describes each gate.
