# @rathnasgala2/theme-minimal

One of the five `@rathnasgala2/theme-*` presentation packages for
Galascribe portable publications (`@rathnasgala2/template@^2.0.0`). This
package is a **closed, passive file set**: JSON and CSS only, no
JavaScript, no build step, no runtime dependency, no lifecycle script.
Unlike `@rathnasgala2/theme-default`, this package is never the build's
fallback; a build resolves to this theme only when a publication's own
`appearance.theme` names it.

## What this package is

Per task packet S2-T14 (S2 "author-owned publication" brief §4, authority
DEC-017 / doc 13 `urn:gala:schema:theme-contract:2.0.0` / doc 26 §3 /
DEC-097 §4 — S2-T14 satisfies the identical closed-package/token/hook/
digest-cycle shape `@rathnasgala2/theme-default` (S2-T13) established,
with this theme's own distinct token values and component rules), the
packed regular-file set (after npm's mandatory `package/`
prefix is stripped) is exactly:

```text
package.json
theme.json
tokens.css
components.css
print.css
LICENSE
README.md
```

`utilities.css` is the optional fourth stylesheet in the two valid
`stylesheets`/`cssLayers` shapes; this theme uses the three-file shape
(`tokens.css`, `components.css`, `print.css`) with no `utilities.css` and no
`assets/` members (no binary or SVG passive assets are declared).

- **`package.json`** is DEC-097's closed, dependency-free, script-free
  object: exactly `name`, `version`, `license`, `files` (the unique
  UTF-8-byte-sorted set `["components.css","print.css","theme.json","tokens.css"]`).
  It has no `scripts`, no `dependencies`, no `devDependencies`, no `engines`
  field — those constraints belong to `tooling/` (below), never to the
  published package identity.
- **`theme.json`** binds the theme's schema (`theme-contract:2.0.0`),
  identity, compatible template range (`^2.0.0`), the exact stylesheet/layer
  projection, the closed subset of the template's 64 public styling hooks
  this theme's CSS actually targets, both palettes for all 35 closed
  tokens, the `text/css` asset inventory, declared budgets, and the
  `fixtureDigest`/`evidenceDigest`/`stylingContractDigest`/`integrity`
  digest chain (see "Digest cycle" below).
- **`LICENSE`** is not license _text_; it is compact-JCS (RFC 8785) license
  _evidence_ — `profile: "gala-theme-license-evidence-v2"`, the SPDX
  3.28.0 license-list version/digest this package's toolchain generated
  from, `packageExpression: "Apache-2.0"`, an empty `assetOverrides` (no
  per-asset license override; the whole package is one expression), and one
  `catalogEntries` row carrying the verbatim SPDX Apache-2.0 license text.
  This is the same shape DEC-097 §4/§8 calls the package's "compact-JCS
  `LICENSE` closure."

## Visual character

Reduced chrome, expressed subtractively rather than by recolouring a copy
of the base rule set (2026-09-25 review, THM-H1/THM-M1): no header/footer
border, no code-block background or corner radius, a wider content measure
(44rem) with more leading (1.7), a two-tier heading weight (500 for
h1-h3, 600 for h4-h6/strong, instead of one flat 600), a single 0.25rem
spacing progression, and a restrained but genuinely distinct link colour
(a muted blue) and focus colour (an amber, the same semantic in both
palettes) instead of collapsing every interactive colour onto body text.
See `tokens.css` for every token value and `components.css` for the full
component rule set — there is no separate "character" override block; the
theme-specific decisions above are the base rules themselves.

## Toolchain and how to run locally

Node `24.18.0` / npm `11.16.0` exactly (`.nvmrc`/`.node-version` at the repo
root pin this; the closed `package.json` above cannot carry an `engines`
field, so pinning lives in these files instead, exactly as this repository's
own tooling does).

Because the published `package.json` cannot carry `devDependencies` or
`scripts` (they would be packed into the published tarball, breaking the
closed shape DEC-097 requires), every lint/test/build/SBOM/release command
for this repository is implemented in **`@rathnasgala2/theme-tooling`**
(a separate repository shared by all five `@rathnasgala2/theme-*`
packages — 2026-09-25 code-discipline review, THD-M6: this used to be 25
files hand-copied into every theme's own `tooling/`, with the packed file
list hardcoded three times over, and guarded only by a `tooling/test/
tooling-drift.test.mjs` that skipped in every CI configuration these
repositories had, THD-H3). This repository's own `tooling/` directory now
carries only `run.mjs` (resolves the shared tooling and dispatches into
it) and a `package.json` whose scripts are one-line calls into it;
`tooling-drift.test.mjs` no longer exists — there is one copy of this
tooling now, so there is nothing left to drift:

```sh
source ~/.nvm/nvm.sh && nvm use 24.18.0
GALA_THEME_TOOLING_DIR=../../theme-tooling npm --prefix tooling run verify
```

`@rathnasgala2/theme-tooling` is not yet published to the npm registry
(publishing it is an owner decision), so it is resolved **only** through
the `GALA_THEME_TOOLING_DIR` environment variable, pointing at a checkout
of it — `run.mjs` fails closed with the exact fix if it is unset. See
`../theme-tooling/README.md` "Status: not yet published" for the full
explanation, what changes once it is published, and why a `file:`
specifier is not used instead (not acceptable for CI: it resolves to a
path that does not exist on a fresh checkout). In CI, every workflow in
`.github/workflows/` checks out `theme-tooling` to a pinned commit
alongside this repository, exactly like the `@rathnasgala2/template`
sibling checkout below.

`npm run verify` (via `tooling`, defined once in `theme-tooling/bin/cli.mjs`
so this list cannot drift from what actually runs — THD-M11) runs, in
order: `format:check`, `lint`, `schema:check` (theme.json structural
validation against `urn:gala:schema:theme-contract:2.0.0`), `css:check`
(the closed-hook selector conformance test), `grammar:check` (closed CSS
property/at-rule allowlist and a rule-count ceiling), `contrast:check`
(WCAG 2.2 AA contrast-ratio assertions for both palettes), `budgets:check`
(the declared `theme.json.budgets` ceilings, enforced), `package:check`
(closed file-set / `0644` mode / `package.json` shape), `absence:check`
(no JavaScript/executable/remote-reference constructs anywhere in the
packed set), `schema-pin:check`, `digest:check` (the digest cycle, checked
against the committed `theme.json` in a scratch copy — never regenerated
in place; see "Digest cycle" below), `test` (the full `node --test` suite,
including the template conformance test), `duplication`, `sbom:check`, and
`audit`. `workflows:check` runs as part of CI (a separate step; see
`.github/workflows/ci.yml`). `digest:generate` (rewrites the real
`theme.json`; run by hand after editing a stylesheet, then commit the
result) and `workflows:drift` are available but deliberately not part of
`verify`.

## The 35-token catalog and both palettes

`theme.json.tokens` carries exactly the 35 keys DEC-097 §4 and the S2 brief
§4 close the catalog to, in the fixed order the `theme-contract:2.0.0`
schema's `tokens` array enforces (`border-width`, the 17 `color-*` keys,
`content-measure`, `focus-width`, the 3 `font-*` keys, `radius-medium`,
`radius-small`, the 6 `space-*` keys, and the 4 `weight-*` keys). Every
`color-*` key differs by palette; every other key's `light`/`dark` value is
identical (its type does not carry a palette-dependent value). Every custom
property is `--gala-<key>`, only ever declared inside `tokens.css`'s
`@layer gala-tokens` block, scoped to the template's own
`resolvedPaletteSelectors` (`[data-gala-publication-root][data-gala-resolved-color-mode="light"]`
/ `="dark"`) — the same selectors published in
`@rathnasgala2/template`'s `contracts/theme-styling-contract.jcs`.

`tooling/test/tokens-contrast.test.mjs` computes WCAG relative-luminance contrast
ratios (the standard sRGB-linearized formula) for every token pair the S2
brief names — body text, muted text, links (unvisited/visited), danger/
warning/success status text, on-accent text, code text, and the border/
focus non-text UI pair — against their governing canvas/surface/accent
color, in **both** palettes independently (passing one palette never
substitutes for the other, per the brief). All body/link/status/code text
pairs clear 4.5:1; the non-text border/focus pairs clear 3:1.

Six of the 35 tokens are declared but never referenced by this theme's own
`components.css`/`print.css` (2026-09-25 review, THD-M1 — `color-accent`
and `space-3`/`space-8` were previously on this list and are now applied:
`color-accent` distinguishes primary-navigation links from body links
(`nav a`), `space-3` sets the blockquote indent, `space-8` sets the
article-end top margin). A theme is permitted to declare a token it does
not itself consume — nothing in the contract requires every declared
token to appear in that same theme's CSS — but each remaining one here is
unused for a specific, stated reason, not by omission:

- `color-on-accent`, `color-success`, `color-warning`, `color-surface-raised` —
  no published hook lets a theme apply a status colour or a raised surface
  to anything (the only page-kind hook is `page-error`, already wired to
  `color-danger`); a future contract revision that adds a `page-kind`
  value per status, or a raised-surface hook, would give these a home.
- `color-link-visited` — the contract's 2.1.0 `pseudoClasses` catalog now
  names `:visited` (see "CSS and the styling contract" below), but the
  pinned `@rathnasgala2/theme-tooling` checkout's `check-css-hooks.mjs`
  does not yet admit _any_ pseudo-class selector (only pseudo-elements),
  so `a:visited` would fail the closed-hook conformance gate today.
- `color-code-canvas`, `radius-medium` — this theme's own THM-M1 decision
  (no code-block background, no radius); the tokens stay declared at their
  contract-required position but resolve to values (`radius-medium: 0`)
  or values (`color-code-canvas`) this theme's CSS no longer reads.

## CSS and the 64-hook styling contract

`tokens.css`, `components.css` and `print.css` are each one outer `@layer`
block (`gala-tokens`, `gala-components`, `gala-print` respectively, matching
`theme.json.cssLayers`) and target **only** the template's published,
closed 64-entry `publicThemeSlotHooks` catalog from
`@rathnasgala2/template`'s `contracts/theme-styling-contract.jcs` — the
landmark/heading/prose/code/control/media/page-kind/slot hooks, always
scoped under the required root compound `[data-gala-publication-root]` (or
its resolved-palette variant), joined only by the contract's four closed
combinators (` `, `>`, `+`, `~`). Contract 2.1.0 publishes a five-member
`pseudoClasses` catalog (`:active`, `:disabled`, `:focus-visible`, `:hover`,
`:visited`) — up from 2.0.0's empty set — but the pinned
`@rathnasgala2/theme-tooling` checkout this repository's `tooling/run.mjs`
resolves does not yet admit any pseudo-class selector in
`check-css-hooks.mjs` (only the four pseudo-elements), so no theme can
actually author `:hover`/`:visited`/etc. yet; this theme's CSS declares
none. The real, paintable focus ring for every publication now comes from
the template's own `gala-base` cascade layer (contract 2.1.0, TPL-H3),
which applies `outline-style: solid` under `:focus-visible` using this
theme's `--gala-color-focus`/`--gala-focus-width` tokens — this theme's own
CSS sets neither `outline-color` nor `outline-width` anywhere, since
`gala-base` already supplies a real ring and a longhand here would only
duplicate it. `theme.json.slotHooks` is the exact sorted set of the hook
IDs this CSS actually uses (not the whole 64-hook catalog — only the
subset a theme actually styles is declared, per the S2 brief).

`tooling/test/css-hooks.test.mjs` parses every stylesheet with `postcss` (a pinned
exact version) and `postcss-selector-parser`, and fails the build if any
selector uses an attribute/class/id/type atom that is not one of the
template's published 64 `publicThemeSlotHooks` selector atoms or the
required root/palette scoping compounds.

`components.css`'s `::selection` rule (THD-M9), where this theme declares
one, is scoped to the root compound only
(`[data-gala-publication-root]::selection`), the only form the contract
admits — `::selection, *::selection` (the conventional, maximally portable
form) is not legal here, since `*` is not a hook atom and the first
compound must be the root. Current Chromium and Firefox inherit
highlight-pseudo styling down the originating-element chain from an
ancestor's `::selection`, which covers this case; Safari's behaviour has
historically differed. Nothing further is possible theme-side under
today's contract.

`tooling/.jscpd.json`'s duplication scan (`jscpd` 3% / 50 tokens,
implementer-rules gate) deliberately excludes `tokens.css`: its light/dark
`@layer gala-tokens` blocks repeat the same 35 custom-property _names_
against different literal color/length/font values by construction (a flat
custom-property declaration has no legitimate way to factor that
repetition out while keeping every value an independently-readable
literal, and it is data — token values — not logic). `components.css` and
`print.css` stay in scope and are refactored (grouped selectors, e.g.
`h4, h5` and the three muted-text slot hooks) wherever a real duplicate
declaration block existed.

## Accessibility posture

- **Contrast**: see above; asserted by test, both palettes, WCAG 2.2 AA.
- **Focus visibility**: this theme sets no `outline-*` property anywhere
  (2026-09-25 review, THD-H1/contract 2.1.0). Previous revisions shipped
  `outline-color`/`outline-width` longhands with no `outline-style`, which
  paint nothing (`outline-style`'s initial value is `none`) — those were
  removed rather than left as a claim the CSS did not back up. The real
  themed ring now comes from the template's own `gala-base` layer (see
  "CSS and the styling contract" above), which reads this theme's
  `--gala-color-focus`/`--gala-focus-width` tokens under a real
  `:focus-visible { outline-style: solid; ... }` rule that always wins over
  the browser's UA default.
- **`forced-colors: active`**: `components.css` maps links, select borders
  and the divider rule to system colors (`LinkText`, `ButtonBorder`,
  `CanvasText`) so meaning survives a forced-colors palette. (A fifth,
  inert `#main-content { outline-color: Highlight }` mapping — outline
  color with no outline style, painting nothing — was removed in the
  contract 2.1.0 adoption; `gala-base`'s own `:focus-visible` rule already
  covers `#main-content` like every other focusable element.)
- **`prefers-reduced-motion: reduce`**: this theme declares no rule of its
  own (removed in the contract 2.1.0 adoption); `gala-base` applies the
  same guard with broader `*`, `*::before`, `*::after` coverage on every
  page regardless of which theme is selected.
- **Zoom/reflow, type scale, overflow containment**: this theme sets no
  fixed pixel widths that would prevent 320px-wide reflow (`main`'s
  `max-width` is a `rem` content measure, never a lower bound). The type
  scale, `pre`/`article` overflow containment, `img` sizing and responsive
  header/footer/main padding that THD-H7 previously found entirely absent
  now come from the template's own `gala-base` layer (contract 2.1.0);
  this theme adds no refinement on top of it (no theme-specific need was
  identified). Playwright-driven 400% zoom/reflow, keyboard-journey and
  axe-core runs are S2-T22, explicitly out of this task's scope.
- **Iconography (THD-H8)**: **deferred.** The template's contract admits a
  theme-declared passive SVG asset (`internal/media/theme-svg-sanitizer.js`,
  TPL-C2) referenced from `::before`/`::after` `content: url(...)` or a
  sized `background-image`. The pinned `@rathnasgala2/theme-tooling`
  checkout's `check-css-grammar.mjs` closed property allowlist does not
  include `content` at all, and `background-image` has no accompanying
  `background-size`/`background-repeat`/`width` in that same allowlist, so
  there is no way to place or size an SVG mark within the closed CSS
  vocabulary this repository's pinned tooling currently admits. Shipping an
  icon needs a `theme-tooling` grammar change first (out of this
  repository's scope — `theme-tooling` is a separate, shared repository).

## Digest cycle (`fixtureDigest`, `evidenceDigest`, `integrity`)

`@rathnasgala2/theme-tooling`'s `scripts/generate-theme-digests.mjs`
implements DEC-097 §4/§8's acyclic digest construction using
the exact domain-separated SHA-256 formulas DEC-097 §8 publishes
(`GALA-THEME-FIXTURE-RELEASE-V2\0`, `GALA-THEME-CONFORMANCE-INPUT-V2\0`,
`GALA-THEME-CONFORMANCE-EVIDENCE-V2\0`, `GALA-THEME-PACKAGE-INTEGRITY-V2\0`)
over RFC 8785 JCS-canonicalized entries, reproduced independently in this
repository (the formulas are DEC-097's public specification; the reference
implementation in `@rathnasgala2/schemas`'s internal
`theme-composition-semantics.js` is not part of that package's published
`exports` map, so it cannot be imported as a dependency — only its
documented algorithm is reused here):

1. **`stylingContractDigest`** is copied verbatim from
   `@rathnasgala2/template`'s published `contracts/theme-styling-contract.jcs`
   `catalogDigest` field — the same value every theme package targeting
   this template version carries.
2. **`fixtureDigest`** is computed over a small **local** conformance
   fixture release this repository defines itself (`schema`, `semantic`,
   `package`, `css`, `absence` runners — see "What `fixtureDigest`/
   `evidenceDigest` are, and are not" below) — independent of this
   package's own file bytes.
3. **`themeConformanceInputDigest`** (an intermediate value, not itself a
   `theme.json` field) is computed over the packed package projection with
   `theme.json`'s `integrity` and `evidenceDigest` fields both absent.
4. **`evidenceDigest`** is computed over a conformance-result record
   naming `fixtureDigest` and `themeConformanceInputDigest`, once every
   local runner in step 2 has actually run against this package's real
   files and recorded a real pass.
5. **`integrity`** is computed last, over the packed package projection
   with only `integrity` itself absent (so it is the only field excluded
   from its own preimage) — the completed `theme.json` (steps 1–4 already
   written) is what gets hashed.

Running `digest:generate` twice in a row on an unchanged source tree
reproduces byte-identical `theme.json` bytes both times (idempotent; no
wall-clock, machine-identity, or non-deterministic input participates) —
`digest-cycle.test.mjs` asserts this directly, against a scratch copy,
independent of the whole-repository two-build-cycle conformance test
below. `digest:check` (part of `verify`) is a separate, stricter
assertion: it copies the packed file set into a scratch directory,
regenerates there, and diffs the result against the **committed**
`theme.json` — it never runs the generator over the real file (2026-09-25
review, THD-H2: the previous implementation ran the un-flagged
`digest:generate` immediately before `digest:check` in both `verify` and
CI, so `--check` compared two generations of the same run and could never
fail regardless of what was actually committed). `digest:generate` is
therefore no longer part of `verify` or CI at all — it is a local-dev-only
command, run by hand after editing a stylesheet, with the result committed.

### What `fixtureDigest`/`evidenceDigest` are, and are not

DEC-097 §4 describes `fixtureDigest`/`evidenceDigest` as binding "the exact
shared fixture release" and "the retained conformance result" produced by
the reusable `infra/.github/workflows/theme-release.yml` runner
(S2-T11: `schema`/`semantic`/`package`/`css`/`binary`/`browser`/`a11y`/
`absence`). That workflow is infra/CI scope, `REMOTE-ONLY` per the S2
digest, and does not exist in this local environment yet (LOCAL-4: do not
fabricate remote evidence). The digests this package ships are therefore
**genuine, locally-computed evidence from this repository's own real local
runners** (`schema`, `semantic`, `package`, `css`, `absence` — five of the
eight runner IDs; `binary` has nothing to validate since this theme
declares no non-CSS assets, and `browser`/`a11y` are Playwright/axe-core,
explicitly S2-T22's task, not this one) — not a fabricated stand-in for the
eventual shared CI fixture release, and not the DEC-097-mandated _shared_
release (which by definition must be identical bytes across all five theme
packages; this package's fixture release is this package's own, until
S2-T11 exists and re-issues a real shared one). This is called out
explicitly here, in `theme.json`'s own generation script, and in the
report handed to the orchestrator, so it is never mistaken for `S2-T11`
CI evidence.

## Consuming the template by path

`tooling`'s conformance test (`tooling/test/template-conformance.test.mjs`)
needs a real `@rathnasgala2/template@2.0.0` build to render against. Locally,
no registry-published `@rathnasgala2/template` tarball exists yet (LOCAL-4),
and — per independent review of an earlier version of this package — a
`file:` npm dependency on a git checkout is not durable release evidence
either (a worktree in particular is explicitly temporary: "never run `git
worktree remove`; the orchestrator does that after merge" governs the
_orchestrator's_ lifecycle for it, not this package's own dependency
graph, and pulling that checkout's own independent lockfile/`node_modules`
into `tooling`'s tree is exactly what polluted `npm ls`/SBOM output
before this fix).

So `@rathnasgala2/template` is resolved **by path, at test/script run
time, never as an npm dependency**: `resolve-template-dir.mjs` (in
`@rathnasgala2/theme-tooling`) exports `resolveTemplateDir()`, which
returns `$GALA_TEMPLATE_DIR` if that environment variable is set to a
non-empty value, else the default `../../template` resolved against the
current working directory (every consumer of this helper always runs with
this repository's `tooling/` as its cwd, so that default is the sibling
checkout `<workspace-root>/template`, matching how
`publish-kernel`/`publish-action` will eventually resolve a theme/template
package directory from an adapter-local extraction, not from
`node_modules`). Every consumer (`check-css-hooks.mjs`,
`rich-build-input.mjs`, `template-conformance.test.mjs`) reads
`contracts/*.jcs` directly off disk under that directory, and dynamically
`import()`s its `src/core/index.js` (the exact file its own `exports["."]`
maps to) by file URL for `renderPublication`/`computeBodyDigest` — no
test-internal helper or private module of the template repository is
imported, and no template code ever appears in this repository's own
`npm ls`/SBOM output.

Set `GALA_TEMPLATE_DIR` to override the default — for example, while a
local sibling checkout's currently-checked-out branch does not yet carry
the template revision this package's tests need.

`resolveTemplateDir()` also honours `WORKSPACE_ROOT` (DEC-015 name),
checked after `GALA_TEMPLATE_DIR` and before the fixed relative default:
when set to a non-empty value, the template checkout resolves as
`<WORKSPACE_ROOT>/template` instead of the fixed relative
`../../template`. This is the fix for running `tooling`'s commands from a
location where that fixed relative default cannot reach the sibling
`template` checkout (for example, a git worktree one level deeper than the
real checkout) without needing a one-off `GALA_TEMPLATE_DIR`: set
`WORKSPACE_ROOT=<the directory containing the sibling repository
checkouts>` once and every sibling resolves correctly. CI sets it to
`${{ github.workspace }}` (see `.github/workflows/ci.yml`).

The conformance test builds its own build-input fixture in
`tooling/test/fixtures/rich-build-input.mjs`, based on
`@rathnasgala2/schemas`'s own published `examples/valid/build-input/canonical.json`
(that example's `appearance.theme` already names
`@rathnasgala2/theme-minimal@2.0.0`), with a real render-policy identity
(reproducing `@rathnasgala2/template`'s own
`GALA-RENDER-POLICY-V2\0`-domain-separated digest over its published
`contracts/render-policy.jcs`, since that computation is not part of the
template's public `exports`) and a real `bodyDigest` (via the template's
own exported `computeBodyDigest`). It calls `renderPublication(buildInput,
{ outputDirectory, workDirectory, sourceDirectory, themeDirectory: <this
package's own repo root>, provenance })` twice into two fresh output
directories and asserts every rendered route/asset file is byte-identical
between the two runs.

## Forbidden constructs

`tooling/test/forbidden-constructs.test.mjs` and `tooling/test/package-file-set.test.mjs`
together assert: no `.js`/`.mjs`/`.cjs`/`.ts`/`.tsx`/`.jsx`/`.wasm`/`bin`
file anywhere in the packed set; no `<script>`, `javascript:`, `expression(`,
`@import`, external `url(...)` (only package-relative/normalized-path
`url()` values would be admitted, and this theme declares none), or
`-moz-binding` construct in any packed CSS byte; every packed member is a
regular `0644` file; `package.json` carries no `dependencies` and no
`scripts`.

## Not in scope here

- `@rathnasgala2/theme-default` (S2-T13, the canonical owner of `tooling/`)
  and the other three sibling themes (`theme-amaze`, `theme-flashy`, `theme-zebra`) — S2-T14
  covers all four together, each its own repository and commit.
- `infra/.github/workflows/theme-release.yml` and this repository's own
  `.github/workflows/release.yaml` caller — S2-T11 (infra, REMOTE-ONLY).
- Playwright/axe-core browser conformance, no-JS/keyboard/400%-zoom
  journeys, manual AT scripts — S2-T22.
