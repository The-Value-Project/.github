# Value Model & Pricing Page Discoverability Specification

**The Value Project**

This document specifies how vendors should make their machine-readable value model pages and pricing pages discoverable by both humans and LLM pipelines. It covers four complementary layers: visible page links, HTML `<link>` metadata, `llms.txt` entries, and sitemap/robots hygiene.

---

## Overview

A conforming implementation publishes:

| Document | Recommended path pattern |
|----------|--------------------------|
| Value model page | `/value-model.md` or `/[product-slug]-value-model.md` |
| Pricing page | `/pricing.md` or `/[product-slug]-pricing.md` |
| BenchmarkClaims JSON | `/benchmark-claims.json` or `/[product-slug]-benchmark-claims.json` |

These paths should be stable. Treat them as canonical URLs — do not move them without a redirect.

---

## Layer 1 — Visible Links on Product and Pricing Pages

### Product page

Place a visible link to the value model page in a consistent location on every product page. The recommended location is near the bottom of the page, above the footer, in a section labelled **"For buyers and AI tools"** or **"Machine-readable data"**:

```html
<section class="machine-readable-links">
  <h2>For buyers and AI tools</h2>
  <ul>
    <li>
      <a href="/value-model.md" type="text/markdown">
        Value Model (machine-readable)
      </a>
      — structured value proposition for this product, conforming to
      <a href="https://github.com/The-Value-Project/value-models">The Value Project</a> standard.
    </li>
  </ul>
</section>
```

The `type="text/markdown"` attribute on the anchor signals the content type to any client that inspects link attributes.

### Pricing page

Place a visible link to the pricing page in the same **"For buyers and AI tools"** section:

```html
<section class="machine-readable-links">
  <h2>For buyers and AI tools</h2>
  <ul>
    <li>
      <a href="/pricing.md" type="text/markdown">
        Pricing Model (machine-readable)
      </a>
      — structured pricing model for this product, conforming to
      <a href="https://github.com/The-Value-Project/pricing-models">The Value Project</a> standard.
    </li>
    <li>
      <a href="/value-model.md" type="text/markdown">
        Value Model (machine-readable)
      </a>
      — structured value proposition, conforming to
      <a href="https://github.com/The-Value-Project/value-models">The Value Project</a> standard.
    </li>
  </ul>
</section>
```

**Placement rules:**
- MUST be visible to sighted users (not hidden via CSS)
- MUST be present in the rendered HTML, not injected by JavaScript after load
- SHOULD appear above the page footer
- SHOULD use the label text exactly as specified so LLMs that read the HTML can identify the links by label

---

## Layer 2 — HTML `<link>` Metadata

Add `<link>` elements to the `<head>` of every product page, pricing page, and the site home page. These follow the `rel="alternate"` convention used for RSS feeds, canonical URLs, and translated pages — already understood by crawlers and increasingly by LLM web-fetch pipelines.

### On the product page `<head>`:

```html
<!-- Value Project: machine-readable value model -->
<link
  rel="alternate"
  type="text/markdown"
  href="https://example.com/value-model.md"
  title="Value Model — The Value Project standard"
/>
```

### On the pricing page `<head>`:

```html
<!-- Value Project: machine-readable pricing model -->
<link
  rel="alternate"
  type="text/markdown"
  href="https://example.com/pricing.md"
  title="Pricing Model — The Value Project standard"
/>

<!-- Value Project: machine-readable value model -->
<link
  rel="alternate"
  type="text/markdown"
  href="https://example.com/value-model.md"
  title="Value Model — The Value Project standard"
/>
```

### On the site home page `<head>`:

```html
<!-- Value Project: machine-readable value model(s) -->
<link
  rel="alternate"
  type="text/markdown"
  href="https://example.com/value-model.md"
  title="Value Model — The Value Project standard"
/>

<!-- Value Project: machine-readable pricing model -->
<link
  rel="alternate"
  type="text/markdown"
  href="https://example.com/pricing.md"
  title="Pricing Model — The Value Project standard"
/>
```

**For multi-product sites**, add one `<link>` pair per product, using `title` to disambiguate:

```html
<link rel="alternate" type="text/markdown"
  href="https://example.com/platform/value-model.md"
  title="Platform — Value Model — The Value Project standard" />
<link rel="alternate" type="text/markdown"
  href="https://example.com/platform/pricing.md"
  title="Platform — Pricing Model — The Value Project standard" />
<link rel="alternate" type="text/markdown"
  href="https://example.com/analytics/value-model.md"
  title="Analytics — Value Model — The Value Project standard" />
```

**Why `rel="alternate"`:** It is the most universally understood relationship type for "another representation of this content." RSS readers, podcast apps, and translation tools have used it for decades. LLMs that fetch and parse HTML will recognise it. Using a proprietary `rel` value (e.g. `rel="value-model"`) would require explicit parser support and offers no discovery benefit until that support is widespread.

---

## Layer 3 — `llms.txt`

`llms.txt` is a plain-text file at the root of a domain (analogous to `robots.txt`) that explicitly lists documents an LLM pipeline should read to understand the site, product, or vendor. It is the most direct and reliable signal for LLM-native discovery.

Place `llms.txt` at: `https://example.com/llms.txt`

### Recommended format

```
# llms.txt — [Vendor Name]
# Machine-readable product and pricing data conforming to The Value Project standard.
# https://github.com/The-Value-Project

## Value Models
- [Product Name] Value Model: https://example.com/value-model.md
  Format: text/markdown; schema: https://github.com/The-Value-Project/value-models
  LLM interpreter: https://github.com/The-Value-Project/value-models/blob/main/spec/value-model-llm-reference.md

## Pricing Models
- [Product Name] Pricing Model: https://example.com/pricing.md
  Format: text/markdown; schema: https://github.com/The-Value-Project/pricing-models
  LLM interpreter: https://github.com/The-Value-Project/pricing-models/blob/main/spec/pricing-model-llm-reference.md

## Benchmark Claims
- [Product Name] Benchmark Claims: https://example.com/benchmark-claims.json
  Format: application/json; schema: https://github.com/The-Value-Project/value-models/blob/main/schemas/benchmark_claims.json
```

**For multi-product sites**, repeat the block per product under each section heading:

```
## Value Models
- Platform Value Model: https://example.com/platform/value-model.md
  Format: text/markdown; schema: https://github.com/The-Value-Project/value-models
  LLM interpreter: https://github.com/The-Value-Project/value-models/blob/main/spec/value-model-llm-reference.md
- Analytics Value Model: https://example.com/analytics/value-model.md
  Format: text/markdown; schema: https://github.com/The-Value-Project/value-models
  LLM interpreter: https://github.com/The-Value-Project/value-models/blob/main/spec/value-model-llm-reference.md
```

**Key design decisions:**
- The `LLM interpreter` line on each entry points directly to the relevant interpreter reference from The Value Project. An LLM that reads `llms.txt` and then fetches a listed document will find its interpretation instructions without needing to parse the page front matter first.
- Section headings (`## Value Models`, `## Pricing Models`, `## Benchmark Claims`) group documents by type, making the file skimmable by both humans and LLMs.
- The `Format` and `schema` inline annotations allow an LLM to pre-qualify documents before fetching them.

---

## Layer 4 — Sitemap and `robots.txt`

### `sitemap.xml`

Include the value model page and pricing page as entries with `changefreq` and `priority` set to signal their importance:

```xml
<url>
  <loc>https://example.com/value-model.md</loc>
  <changefreq>monthly</changefreq>
  <priority>0.8</priority>
</url>
<url>
  <loc>https://example.com/pricing.md</loc>
  <changefreq>monthly</changefreq>
  <priority>0.8</priority>
</url>
```

### `robots.txt`

Ensure these paths are explicitly allowed (the default is allow, but explicit is better for `.md` and `.json` paths that some robots configs accidentally block):

```
# Value Project machine-readable pages
Allow: /value-model.md
Allow: /pricing.md
Allow: /benchmark-claims.json
```

If you use a wildcard block on non-HTML file types (e.g. `Disallow: /*.md$`), add the explicit `Allow` lines above the wildcard so they take precedence.

---

## Summary: What Goes Where

| Location | What to add |
|----------|-------------|
| Product page HTML | Visible `<a>` link to `value-model.md` in "For buyers and AI tools" section |
| Pricing page HTML | Visible `<a>` links to both `pricing.md` and `value-model.md` |
| Product page `<head>` | `<link rel="alternate" type="text/markdown" href="/value-model.md">` |
| Pricing page `<head>` | `<link>` for both `pricing.md` and `value-model.md` |
| Site home page `<head>` | `<link>` for all value model and pricing pages |
| `/llms.txt` | Entries for all value models, pricing models, and benchmark claims with schema and interpreter URLs |
| `sitemap.xml` | `<url>` entries for `.md` and `.json` pages |
| `robots.txt` | Explicit `Allow` for `.md` and `.json` paths |

---

## Conformance

A vendor's implementation conforms to this specification if:

- [ ] At least one visible link to each machine-readable page exists on the corresponding human-readable page
- [ ] The visible link section is labelled "For buyers and AI tools" or "Machine-readable data"
- [ ] `<link rel="alternate" type="text/markdown">` elements are present in the `<head>` of product and pricing pages
- [ ] `<link>` elements are present in the `<head>` of the site home page
- [ ] `/llms.txt` exists and includes entries for all value model pages, pricing pages, and benchmark claims files
- [ ] Each `llms.txt` entry includes a `LLM interpreter` line pointing to the relevant Value Project interpreter reference
- [ ] Value model and pricing pages are included in `sitemap.xml`
- [ ] `robots.txt` does not block access to `.md` or `.json` files at these paths

---

*Part of [The Value Project](https://github.com/The-Value-Project) by [ValueIQ](https://valueiq.ai)*
*License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)*
