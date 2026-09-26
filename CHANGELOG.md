# Changelog

All notable changes to `@rathnasgala2/theme-minimal` are documented here.

## Unreleased

## 2.1.0 - 2026-09-26

### Fixed (THD-M6, 2026-09-26 fourth pass)

- `theme.json`'s `"package"` field named `@rathnasgala2/theme-minimal@2.0.0` even after
  `package.json`'s `version` was bumped to `2.1.0` — a consumer resolving
  this theme's identity from `theme.json` (rather than `package.json`)
  would throw `THEME_CONTRACT_IDENTITY_MISMATCH`. Fixed to `@2.1.0`;
  digest chain regenerated in this same commit (no `LOCAL_RUNNERS` script
  changed, so only `integrity`/`evidenceDigest` shift, not
  `fixtureDigest`). `@rathnasgala2/theme-tooling`'s
  `package-identity:check` (new `verify` step) catches this class from now
  on.
- `sbom.cdx.json` is no longer committed in this repository. It used to be
  `cyclonedx-npm` scanning `@rathnasgala2/theme-tooling`'s own
  `package-lock.json` and attributing the result to this theme's identity
  — a design that diverged between a local machine and CI three times
  running (see `theme-tooling`'s own CHANGELOG 0.2.0 for the root cause).
  `sbom:generate` now builds a self-contained CycloneDX document directly
  from this package's own `name`/`version` (zero dependency components —
  this theme ships none); `.github/workflows/release.yaml` generates it
  fresh at release time and uploads it as a build artifact instead of
  committing it. `@rathnasgala2/theme-tooling` sibling checkout re-pinned
  accordingly.

### Changed (2026-09-25 third review pass: `@rathnasgala2/theme-tooling` ae2ee49)

- CI: the `@rathnasgala2/theme-tooling` sibling checkout pin moves to
  `ae2ee4979a4e5f6a4335c99b869f80eacd359788` (still `0.1.0`, unpublished),
  which serves the shared visual-check fixture over loopback HTTP instead
  of `file://`. That resolves the dark-palette `:visited`/harness finding
  recorded below under the second review pass: `visual:check` is now
  clean across both palettes and all three viewports with no serious/
  critical axe violations and no horizontal overflow (previously logged
  as an unfixable, harness-level limitation — it was specific to loading
  the fixture from disk and does not reproduce over HTTP). `ae2ee49` also
  admits `text-decoration-skip-ink` (`auto|none|all`) to the CSS grammar
  catalog and adds `color-accent` on `color-code-canvas` as a new default
  non-text-UI contrast pair (≥3:1); this theme's existing accent/canvas
  colors already clear it in both palettes (light 4.55:1, dark 4.32:1) —
  no color values changed.
- `components.css`'s `a` rule gains `text-decoration-skip-ink: auto`, so
  underlines break around descenders instead of crossing them.
- `digest:generate` re-run after the above (asset byte-lengths/digests are
  unchanged in value but the cycle is re-emitted for the new tooling pin).

2026-09-25 code-discipline review remediation (THD-H5): this file
previously carried three dated sub-headings under `## Unreleased` above a
`## 2.0.0 - 2026-09-22` heading, even though `2.0.0` has been on the
registry since 2026-09-22 — a state that made no sense read either way
(see the review's own explanation). The three dated entries below are
folded into this one `Unreleased` section (Keep a Changelog: exactly one
`Unreleased` section, dated headings below it), and `2.0.0`'s heading now
carries its actual release date. Everything in this section ships as the
next version; bumping `package.json`'s `version` for that release is an
owner decision (recommended: `2.1.0`, since nothing below is a breaking
change to the token/CSS-hook contract).

### Changed (2026-09-25 second review pass: `@rathnasgala2/theme-tooling` 8fd9b36, THD-M10/M2/H8/L2, THM-H2)

- CI: the `@rathnasgala2/template` sibling checkout pin moves to
  `d2b2f0ffc38407851e293e5a8a92d8863a0182d1` (still contract 2.1.0,
  unreleased) and the `@rathnasgala2/theme-tooling` sibling checkout pin
  moves to `8fd9b36f85f4ae0a34dfb6ebb7c55319672071d2`, which adds
  contract-driven pseudo-class admission, the icon property set, three
  new contrast-pair floors, two-way `slotHooks` reconciliation and the
  `visual:check` Playwright/axe harness. `stylingContractDigest` is
  unchanged: the re-pinned contract's `catalogDigest` is byte-identical
  to the one already committed.
- **THD-M10**: `ci.yml` gained a `visual` job that installs Chromium and
  runs `tooling`'s new `visual:check` script, uploading screenshots as a
  build artifact; deliberately its own job, not folded into `verify` (see
  `theme-tooling`'s README).
- **THD-M2**: `color-accent` and `color-surface-raised` each cleared
  every pre-existing contrast pair but failed the three new floors
  (surface-raised/surface ≥1.3:1, accent/text ≥3:1, accent/surface ≥3:1)
  in both palettes — `color-accent` sat within a few percent of
  `color-text`, and `color-surface-raised` within a few percent of
  `color-surface`. Moved `color-accent` to a mid neutral gray (light
  `#6e6e6e`, dark `#808080`) and `color-surface-raised` to a more
  distinct step off the canvas (light `#dcdcdc`, dark `#323232`); both
  stay strictly achromatic (THM-M1's "minimality in weight, not in a
  recolouring pass" thesis holds), and `nav`'s actual link-text usage of
  `color-accent` stays above real 4.5:1 text contrast, not just the new
  pair's 3:1 non-text-UI minimum.
- **THD-H8**: one reference icon — a small, near-invisible square before
  `[data-gala-slot="article-end"]`'s content (`0.25rem`, muted-text
  color), using only the newly admitted icon property set (`content: ""`
  plus sizing properties) on an existing hook, no new `slotHooks` entry.
- **THM-H2**: `color-link-visited` was byte-identical to
  `color-text-muted` in the light palette (and nearly so in dark) — a
  visited link and plain muted text were indistinguishable. Gave it its
  own restrained, desaturated blue-gray in both palettes (light
  `#4a6f83`, dark `#95b0bc`), added `a:visited` (reads the token) and
  `a:hover` (a `text-decoration-thickness` change, no color shift).
  Focus is unchanged: it continues to come entirely from `gala-base`'s
  `:focus-visible` rule reading this theme's tokens.
- **THD-L2**: `print.css`'s `a` rule dropped `text-decoration-line:
underline` — identical to, and unaffected by print media, what
  `components.css`'s own `a` rule already sets in the same cascade
  layer; the print override now carries only the one declaration
  (`color`) that actually differs from screen.
- `visual:check` (Playwright + axe-core, 320/768/1440px, light/dark):
  clean, except one finding logged at the time — the shared fixture's
  self-referencing links (the skip link and every nav/breadcrumb link
  back to the fixture's own page) resolved to `:visited` once Chromium
  had navigated to that URL, and Chromium's privacy protection against
  history-sniffing made the true rendered `:visited` style unobservable
  to `getComputedStyle`/axe-core in dark mode. That was specific to
  loading the fixture from `file://`; the third review pass above
  (`theme-tooling` ae2ee49, fixture served over loopback HTTP) resolved
  it, and `visual:check` is now clean with no exceptions.

Recommended release for everything above: **`2.1.0`** (same as the first
review pass below — no removed public surface).

### Changed (2026-09-25 review follow-up: contract 2.1.0, THM-H1/H2/M1/L1/L2, THD-H7/H8/M1/M9)

- Adopted `@rathnasgala2/template` contract 2.1.0: `theme.json`'s
  `contractVersion` moves to `2.1.0` and `stylingContractDigest` is
  re-pinned to the new contract's own `catalogDigest`. `templateRange`
  stays `^2.0.0` (the template's own published version has not moved).
- **THM-H1**: rebuilt `components.css` as a true delta. The "Character"
  override block that restated the base rule set with the values it
  already resolved to (background-color no-ops on header/footer/select)
  is gone; its two effective changes (header/footer border colour,
  code-block background) are now the base rules themselves. Removed
  header/footer/main's own fixed padding (gala-base's new responsive
  padding steps would otherwise be shadowed by a same-specificity theme
  rule), this theme's own root-only `prefers-reduced-motion` rule
  (gala-base now covers it with broader selector reach), and the inert
  `#main-content { outline-color: Highlight }` forced-colors mapping.
- **THM-H2, THM-L2**: light-palette `color-link` (`#2b2b2b` → `#2c6f97`)
  and `color-focus` (`#2b2b2b` → `#b96900`) are distinct from body text
  and from each other again; focus now shares one semantic (amber) across
  both palettes instead of "same as text" in light and "amber" in dark.
- **THM-M1**: header/footer borders removed outright; pre/code lose their
  background and radius (`radius-medium` set to `0`); `content-measure`
  widens `38rem` → `44rem`; root `line-height` `1.6` → `1.7`;
  `weight-heading` `600` → `500` (h1-h3 now sit at 500, h4-h6/strong stay
  at 600).
- **THM-L1**: `space-1`..`space-8` now follow one `0.25rem`-per-index
  progression instead of two unrelated rules with a discontinuity between
  `space-3` and `space-4`.
- **THD-M1**: `color-accent` (`nav a`), `space-3` (blockquote indent) and
  `space-8` (article-end top margin) are now consumed; the remaining
  unused tokens are documented in the README with the specific reason each
  one has no consumer today.
- **THD-H7**: type scale, overflow containment, image sizing and
  responsive header/footer/main padding now come from the template's own
  `gala-base` layer (contract 2.1.0); no theme-specific refinement was
  needed on top of it.
- **THD-H8**: iconography stayed deferred as of this entry — the pinned
  `@rathnasgala2/theme-tooling` checkout's CSS grammar allowlist had no
  `content` property and no `background-size`/`background-repeat`/`width`
  alongside `background-image`, so no icon could be placed or sized within
  the closed grammar at the time; resolved in the section below once
  `theme-tooling` 8fd9b36 added the icon property set.
- `theme.json.slotHooks` drops `landmark-main-content` (its only rule was
  the inert forced-colors mapping removed above).
- CI: the `@rathnasgala2/template` sibling checkout pin moves to
  `e66d8771189966db1f8f876b005ab59d4f676bcb` (carries the unreleased
  contract 2.1.0), and the `@rathnasgala2/theme-tooling` sibling checkout
  pin moves to `68dceb301c071f3a60c2bf4c4f3215a6c3478502`.

Recommended release for everything above: **`2.1.0`** (no removed public
surface; the contract, token-value and CSS changes are all additive or
corrective).

### Changed (THD-H1, 2026-09-25)

- Removed the four inert `outline-color`/`outline-width` declaration pairs
  from `components.css` (`#main-content`, `a`, `select`) — `outline-style`
  was never set alongside them, so they painted nothing (the initial value
  of `outline-style` is `none`), and the previous README/CHANGELOG claim
  that they produced a themed visible focus ring was false. A themed ring
  needs `:focus-visible`, unavailable in this template contract version
  (TPL-H2); a single comment in `components.css` documents where it will
  be restored.

### Changed (THD-C1, 2026-09-25)

- Added a bare-root `[data-gala-publication-root]` block (light palette)
  plus a `@media (prefers-color-scheme: dark)` override to `tokens.css`,
  before the two resolved-mode blocks, so every `--gala-*` token still has
  a real value when `data-gala-resolved-color-mode` is not set (no
  JavaScript, a text-mode crawler, or a pre-hydration paint) — previously
  the theme applied no styling at all in that case.

### Changed (THD-M6, 2026-09-25)

- Replaced this repository's own copy of `tooling/scripts`/`tooling/test`
  (25 files, identical across all five theme repositories except one
  package-name literal, and the `tooling-drift.test.mjs` guard that
  skipped in every CI configuration these repositories had, THD-H3) with a
  dependency on the new `@rathnasgala2/theme-tooling` repository, which
  now implements every gate once. `tooling/` here carries only
  `run.mjs` and a trimmed `package.json`. See
  `../theme-tooling/CHANGELOG.md` for what moved and what changed in the
  process (THD-H2/M2/M3/M4/M5).
- Deleted the root `package-lock.json` (THD-L5): the published
  `package.json` has never had a dependency for it to lock.
- Widened the closed `package.json` shape to carry `repository` (THD-H6):
  `npm publish --provenance` derives the source repository from that
  field and refuses to build a provenance statement without it.

### Changed (SCHEMA-REPIN-2.11.0, 2026-09-22)

- `tooling/package.json` re-pins `@rathnasgala2/schemas` from the LOCAL-1
  local tarball (`file:../../../local-packages/rathnasgala2-schemas-2.8.0.tgz`)
  to the exact published registry version `2.11.0`
  (`https://registry.npmjs.org/@rathnasgala2/schemas/-/schemas-2.11.0.tgz`,
  integrity `sha512-5hXxpLaXqEKKhoRLBBEq98rzJQ9K4u3b8nDMt/ZZmV2gL4XRmTOCwjF1U618UJ1CgmFnlifhQWRuDvQQVyFfTA==`).
  This fixes CI, which was failing on every push because the `file:` path
  does not exist on GitHub Actions runners. `urn:gala:schema:theme-contract:2.0.0`
  and the `build-input` root this tooling validates against are
  byte-identical between 2.8.0 and 2.11.0 (contract re-pin packet,
  2026-09-19); `tooling/package-lock.json` and `sbom.cdx.json` regenerated
  accordingly; full `npm run verify` re-run and green.
- Added `tooling/scripts/check-no-local-schema-pin.mjs` (wired into
  `verify` as `schema-pin:check`) so a `file:`/`local-packages` specifier
  for `@rathnasgala2/schemas` can never silently return.

### Changed (THEMES-2.8.0, 2026-09-18)

- `tooling/package.json` pins `@rathnasgala2/schemas` to the packed
  `rathnasgala2-schemas-2.8.0.tgz` tarball (sha256
  `6352293855cdcff9054d43ced876740644f6b45bc813eda3990b646ec9bef563`,
  LOCAL-1), up from 2.6.1; `tooling/package-lock.json` integrity and
  `sbom.cdx.json` regenerated. `urn:gala:schema:theme-contract:2.0.0`, the
  `build-input` root and the published `examples/valid/build-input/canonical.json`
  this tooling validates against are byte-identical between 2.6.1 and 2.8.0;
  the 2.7.0-2.8.0 delta is confined to the deployment roots, the OpenAPI
  bundle and the App catalogs, none of which this repository consumes.

### Added (FOLLOW-UP SUPPLY-CHAIN-JS, 2026-09-17)

- `tooling/scripts/resolve-template-dir.mjs` and `tooling/test/tooling-drift.test.mjs`
  updated to `theme-default`'s canonical copies: `resolveTemplateDir()` now
  also honours `WORKSPACE_ROOT` (DEC-015 name), checked after the existing
  `GALA_TEMPLATE_DIR` override and before the fixed relative default
  (`<WORKSPACE_ROOT>/template`), fixing resolution from a git worktree one
  level deeper than the real checkout (LOCAL-38); `tooling-drift.test.mjs`
  resolves its `theme-default` canonical source the same way and reads
  `@rathnasgala2/theme-minimal`'s own package-identity literal from `package.json` at run time
  instead of a hardcoded name, and skips (with a printed reason) rather than
  failing when `theme-default` cannot be found. Added
  `tooling/test/resolve-template-dir.test.mjs`.

## 2.0.0 - Unreleased (task packet S2-T14)

### Added

- Visual character: Reduced chrome: a near-monochrome palette, a hairline `border-width`, near-square `radius-*`, tightened `space-*`, and `header`/`footer`/`pre`/`code`/`select`/`#gala-appearance-color-mode` surfaces blended into the canvas so only text and rules carry the page.
- Initial closed package file set: `package.json` (dependency-free,
  script-free, 4-key closed shape), `theme.json` (all 35 tokens for light
  and dark palettes, `stylesheets`/`cssLayers` three-file shape, the
  51-hook `slotHooks` subset this theme's CSS uses, budgets, and the
  digest chain), `tokens.css`/`components.css`/`print.css`, and a
  compact-JCS `LICENSE` license-evidence file (SPDX `Apache-2.0`).
- WCAG 2.2 AA contrast for every named token pair in both palettes,
  `outline-color`/`outline-width` on every interactive hook (no
  `outline-style` override, no `:focus` pseudo-class available in this
  template contract version — corrected 2026-09-25, THD-H1: these two
  longhands alone never painted a visible ring, and were removed),
  `forced-colors: active` system-color mappings, and a defensive
  `prefers-reduced-motion: reduce` rule.
- `tooling/` local dev/test/SBOM project (private, unpublished, its own
  lockfile) with the closed-hook CSS conformance test, the WCAG contrast
  test, the theme-contract schema test, the closed-package-file-set test,
  the forbidden-constructs absence test, the digest-cycle generator/test,
  the template-conformance byte-equality test (two builds against
  `@rathnasgala2/template`'s `main` branch, consumed by path), and a
  tooling-drift test asserting `tooling/scripts/*.mjs` is byte-identical
  to `theme-default`'s canonical copy except the one documented
  package-identity literal.
- SBOM (`sbom.cdx.json`, CycloneDX 1.6, generated for the published package
  surface — which has zero runtime dependencies).

### Notes

- `fixtureDigest`/`evidenceDigest` are this repository's own genuine local
  conformance evidence (five of the eight DEC-097 runner IDs:
  `schema`/`semantic`/`package`/`css`/`absence`), not the DEC-097-mandated
  _shared_ fixture release/result the not-yet-existing `S2-T11` reusable
  CI workflow will eventually produce and re-issue across all five theme
  packages. See README "What `fixtureDigest`/`evidenceDigest` are, and are
  not."
