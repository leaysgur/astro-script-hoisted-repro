# astro-script-hoisted-repro

## Overview

- Project structure

```
src
├── components
│   └── v1-only.astro
└── pages
    ├── [path].astro
    └── index.astro
```

- `pages/[path].astro` will render 2 patterns

```astro
---
import V1Only from "../components/v1-only.astro";

export const getStaticPaths = () => ([
  { params: { path: "v1-1" } },
  { params: { path: "v1-2" } },
  { params: { path: "v2-1" } },
  { params: { path: "v2-2" } },
]);

const { params: { path }} = Astro;
---

<h1>This is "{path}" page.</h1>

{path.startsWith("v1") ? <V1Only /> : <p>0 JS for v2!</p>}
```

- only `v1` page has `<V1Only />` component which includes `<script>`

```astro
<p>For v1, we need JS.</p>
<script>
  document.querySelector('p').textContent = 'JS is working for v1';
</script>
```

## Expect

- Rendered `/v1-*` page should have `<script>`
- Rendered `/v2-*` page should NOT have `<script>`

## Actual

- Both pages have `<script>`...

See [dist/v2/index.html](./dist/v2/index.html).

## Why?

This is current Astro's expected behavior(restriction).

> https://docs.astro.build/en/guides/client-side-scripts/#script-bundling
> https://docs.astro.build/en/guides/troubleshooting/#an-unexpected-script-or-style-is-included

## Work around 1

Use `is:inline` for `<script>`.

But this work around has some drawbacks.

- Inlined script is injected for every pages
- Out of scope from Astro's optimization(TS, npm import, etc)

## Work around 2

Split `pages/[path].astro` into 2 pages.

```
src
├── components
│   └── v1-only.astro
└── pages
    ├── [pathV1].astro
    ├── [pathV2].astro
    └── index.astro
```

Final outputs are merged into single path.

Then only `/v1-*` pages have `<script>`.
