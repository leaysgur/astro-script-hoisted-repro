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
  { params: { path: "v1" } },
  { params: { path: "v2" } },
]);

const { params: { path }} = Astro;
---

<h1>This is "{path}" page.</h1>

{path === "v1" ? <V1Only /> : <p>0 JS for v2!</p>}
```

- only `v1` page has `<V1Only />` component which includes `<script>`

```astro
<p>For v1, we need JS.</p>
<script>
  document.querySelector('p').textContent = 'JS is working for v1';
</script>
```

## Expect

- Rendered `/v1` page should have `<script>`
- Rendered `/v2` page should NOT have `<script>`

## Actual

- Both pages have `<script>`...

See [dist/v2/index.html](./dist/v2/index.html).
