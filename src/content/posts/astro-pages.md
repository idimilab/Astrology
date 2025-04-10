---
title: Pages
slug: pages
description: An introduction to Astro pages
category:
  - One
tags:
  - Component
  - Astro
  - Jamstack
pubDate: 2023-09-01
cover: https://images.unsplash.com/photo-1572283046480-e990be92d301?w=1960&h=1102&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8Mjd8fGJsYWNrfGVufDB8MHwwfHx8Mg%3D%3D
coverAlt: Astrology-Aliases
author: VV
---

**Pages** are files that live in the `src/pages/` subdirectory of your Astro project. They are responsible for handling routing, data loading, and overall page layout for every page in your website.

## Supported page files

Astro supports the following file types in the `src/pages/` directory:

- [`.astro`](#astro-pages)
- [`.md`](#markdownmdx-pages)
- `.mdx` (with the [MDX Integration installed](/en/guides/integrations-guide/mdx/#installation))
- [`.html`](#html-pages)
- `.js`/`.ts` (as [endpoints](/en/core-concepts/endpoints/))

## File-based routing

Astro leverages a routing strategy called **file-based routing**. Each file in your `src/pages/` directory becomes an endpoint on your site based on its file path.

A single file can also generate multiple pages using [dynamic routing](/en/core-concepts/routing/#dynamic-routes). This allows you to create pages even if your content lives outside of the special `/pages/` directory, such as in a [content collection](/en/guides/content-collections/) or a [CMS](/en/guides/cms/).

📚 Read more about [Routing in Astro](/en/core-concepts/routing/).

### Link between pages

Write standard HTML [`<a>` elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) in your Astro pages to link to other pages on your site. Use a **URL path relative to your root domain** as your link, not a relative file path.

For example, to link to `https://example.com/authors/sonali/` from any other page on `example.com`:

```astro title="src/pages/index.astro"
Read more <a href="/authors/sonali/">about Sonali</a>.
```

## Astro Pages

Astro pages use the `.astro` file extension and support the same features as [Astro components](/en/core-concepts/astro-components/).

```astro title="src/pages/index.astro"
---

---

<html lang="en">
  <head>
    <title>My Homepage</title>
  </head>
  <body>
    <h1>Welcome to my website!</h1>
  </body>
</html>
```

A page must produce a full HTML document. If not explicitly included, Astro will add the necessary `<!DOCTYPE html>` declaration and `<head>` content to any `.astro` component located within `src/pages/` by default. You can opt-out of this behavior on a per-component basis by marking it as a [partial](#page-partials) page.

To avoid repeating the same HTML elements on every page, you can move common `<head>` and `<body>` elements into your own [layout components](/en/core-concepts/layouts/). You can use as many or as few layout components as you'd like.

```astro {3} /</?MySiteLayout>/
---
// src/pages/index.astro
import MySiteLayout from "../layouts/MySiteLayout.astro";
---

<MySiteLayout>
  <p>My page content, wrapped in a layout!</p>
</MySiteLayout>
```

📚 Read more about [layout components](/en/core-concepts/layouts/) in Astro.

## Markdown/MDX Pages

Astro also treats any Markdown (`.md`) files inside of `src/pages/` as pages in your final website. If you have the [MDX Integration installed](/en/guides/integrations-guide/mdx/#installation), it also treats MDX (`.mdx`) files the same way. These are commonly used for text-heavy pages like blog posts and documentation.

[Collections of Markdown or MDX page content](/en/guides/content-collections/) in `src/content/` can be used to [generate pages dynamically](/en/core-concepts/routing/#dynamic-routes).

Page layouts are especially useful for [Markdown files](#markdownmdx-pages). Markdown files can use the special `layout` frontmatter property to specify a [layout component](/en/core-concepts/layouts/) that will wrap their Markdown content in a full `<html>...</html>` page document.

```md {3}
---
# Example: src/pages/page.md
layout: "../layouts/MySiteLayout.astro"
title: "My Markdown page"
---

# Title

This is my page, written in **Markdown.**
```

📚 Read more about [Markdown](/en/guides/markdown-content/) in Astro.

## HTML Pages

Files with the `.html` file extension can be placed in the `src/pages/` directory and used directly as pages on your site. Note that some key Astro features are not supported in [HTML Components](/en/core-concepts/astro-components/#html-components).

## Custom 404 Error Page

For a custom 404 error page, you can create a `404.astro` or `404.md` file in `/src/pages`.

This will build to a `404.html` page. Most [deploy services](/en/guides/deploy/) will find and use it.

## Page Partials

<p><Since v="3.4.0" /></p>

:::caution
Page partials are intended to be used in conjunction with a front-end library, such as [htmx](https://htmx.org/) or [Unpoly](https://unpoly.com/). You can also use them if you are comfortable writing low-level front-end JavaScript. For this reason they are an advanced feature.

Additionally, partials should not be used if the component contains scoped styles or scripts, as these elements will be stripped from the HTML output. If you need scoped styles, it is better to use regular, non-partial pages along with a frontend library that knows how to merge the contents into the head.
:::

Partials are page components located within `src/pages/` that are not intended to render as full pages.

Like components located outside of this folder, these files do not automatically include the `<!DOCTYPE html>` declaration, nor any `<head>` content such as scoped styles and scripts.

However, because they are located in the special `src/pages/` directory, the generated HTML is available at a URL corresponding to its file path. This allows a rendering library (e.g. htmx, Stimulus, jQuery) to access it on the client and load sections of HTML dynamically on a page without a browser refresh or page navigation.

Partials, when combined with a rendering library, provide an alternative to [Astro islands](/en/concepts/islands/) and [`<script>` tags](/en/guides/client-side-scripts/) for building dynamic content in Astro.

Page files that can export a value (e.g. `.astro` , `.mdx`) can be marked as partials.

Configure a file within the `src/pages/` directory to be a partial by adding the following export:

```astro title="src/pages/partial.astro" ins={2}
---
export const partial = true;
---

<li>I'm a partial!</li>
```

The `export const partial` must be identifiable statically. It can have the value of:

- The boolean **`true`**.
- An environment variable using import.meta.env such as `import.meta.env.USE_PARTIALS`.

### Using with a library

Partials are used to dynamically update a section of a page using a library such as [htmx](https://htmx.org/).

The following example shows an `hx-post` attribute set to a partial's URL. The content from the partial page will be used to update the targeted HTML element on this page.

```astro title="src/pages/index.astro" 'hx-post="/partials/clicked/"'
<html>
  <head>
    <title>My page</title>
    <script
      src="https://unpkg.com/htmx.org@1.9.6"
      integrity="sha384-FhXw7b6AlE/jyjlZH5iHa/tTe9EpJ1Y55RjcgPbjeWMskSxZt1v9qkxLJWNJaGni"
      crossorigin="anonymous"></script>
  </head>
</html>
<section>
  <div id="parent-div">Target here</div>

  <button
    hx-post="/partials/clicked/"
    hx-trigger="click"
    hx-target="#parent-div"
    hx-swap="innerHTML"
  >
    Click Me!
  </button>
</section>
```

The `.astro` partial must exist at the corresponding file path, and include an export defining the page as a partial:

```astro title="src/pages/partials/clicked.astro" {2}
---
export const partial = true;
---

<div>I was clicked!</div>
```

See the [htmx documentation](https://htmx.org/docs/) for more details on using htmx.
