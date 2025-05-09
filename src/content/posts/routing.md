---
title: Routing
slug: routing
description: An intro to routing with Astro.
category:
  - One
tags:
  - Tailwind
  - Astro
  - Jamstack
pubDate: 2023-09-01
cover: https://images.unsplash.com/photo-1501791187590-9ef2612ba1eb?w=1960&h=1102&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8NzJ8fGJsYWNrfGVufDB8MHwwfHx8Mg%3D%3D
coverAlt: Astrology-Aliases
author: VV
---

Astro uses **file-based routing** to generate your build URLs based on the file layout of your project `src/pages/` directory.

## Navigating between pages

Astro uses standard HTML [`<a>` elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) to navigate between routes. There is no framework-specific `<Link>` component provided.

```astro title="src/pages/index.astro"
<p>Read more <a href="/about/">about</a> Astro!</p>
```

## Static routes

`.astro` [page components](/en/core-concepts/astro-pages/) as well as Markdown and MDX Files (`.md`, `.mdx`) within the `src/pages/` directory **automatically become pages on your website**. Each page’s route corresponds to its path and filename within the `src/pages/` directory.

```diff
# Example: Static routes
src/pages/index.astro        -> mysite.com/
src/pages/about.astro        -> mysite.com/about
src/pages/about/index.astro  -> mysite.com/about
src/pages/about/me.astro     -> mysite.com/about/me
src/pages/posts/1.md         -> mysite.com/posts/1
```

:::tip
There is no separate "routing config" to maintain in an Astro project! When you add a file to the `src/pages/` directory, a new route is automatically created for you. In static builds, you can customize the file output format using the [`build.format`](/en/reference/configuration-reference/#buildformat) configuration option.
:::

## Dynamic routes

An Astro page file can specify dynamic route parameters in its filename to generate multiple, matching pages. For example, `src/pages/authors/[author].astro` generates a bio page for every author on your blog. `author` becomes a _parameter_ that you can access from inside the page.

In Astro's default static output mode, these pages are generated at build time, and so you must predetermine the list of `author`s that get a corresponding file. In SSR mode, a page will be generated on request for any route that matches.

### Static (SSG) Mode

Because all routes must be determined at build time, a dynamic route must export a `getStaticPaths()` that returns an array of objects with a `params` property. Each of these objects will generate a corresponding route.

`[dog].astro` defines the dynamic `dog` parameter in its filename, so the objects returned by `getStaticPaths()` must include `dog` in their `params`. The page can then access this parameter using `Astro.params`.

```astro title="src/pages/dogs/[dog].astro"
---
export function getStaticPaths() {
  return [
    { params: { dog: "clifford" } },
    { params: { dog: "rover" } },
    { params: { dog: "spot" } },
  ];
}

const { dog } = Astro.params;
---

<div>Good dog, {dog}!</div>
```

This will generate three pages: `/dogs/clifford`, `/dogs/rover`, and `/dogs/spot`, each displaying the corresponding dog name.

The filename can include multiple parameters, which must all be included in the `params` objects in `getStaticPaths()`:

```astro title="src/pages/[lang]-[version]/info.astro"
---
export function getStaticPaths() {
  return [
    { params: { lang: "en", version: "v1" } },
    { params: { lang: "fr", version: "v2" } },
  ];
}

const { lang, version } = Astro.params;
---

...
```

This will generate `/en-v1/info` and `/fr-v2/info`.

Parameters can be included in separate parts of the path. For example, the file `src/pages/[lang]/[version]/info.astro` with the same `getStaticPaths()` above will generate the routes `/en/v1/info` and `/fr/v2/info`.

📚 Learn more about [`getStaticPaths()`](/en/reference/api-reference/#getstaticpaths).

<RecipeLinks slugs={["en/recipes/i18n"]} />

#### Rest parameters

If you need more flexibility in your URL routing, you can use a [rest parameter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) (`[...path]`) in your `.astro` filename to match file paths of any depth:

```astro title="src/pages/sequences/[...path].astro"
---
export function getStaticPaths() {
  return [
    { params: { path: "one/two/three" } },
    { params: { path: "four" } },
    { params: { path: undefined } },
  ];
}

const { path } = Astro.params;
---

...
```

This will generate `/sequences/one/two/three`, `/sequences/four`, and `/sequences`. (Setting the rest parameter to `undefined` allows it to match the top level page.)

Rest parameters can be used with **other named parameters**. For example, GitHub's file viewer can be represented with the following dynamic route:

```
/[org]/[repo]/tree/[branch]/[...file]
```

In this example, a request for `/withastro/astro/tree/main/docs/public/favicon.svg` would be split into the following named parameters:

```js
{
	org: 'withastro',
	repo: 'astro',
	branch: 'main',
	file: 'docs/public/favicon.svg'
}
```

#### Example: Dynamic pages at multiple levels

In the following example, a rest parameter (`[...slug]`) and the [`props`](/en/reference/api-reference/#data-passing-with-props) feature of `getStaticPaths()` generate pages for slugs of different depths.

```astro title="src/pages/[...slug].astro"
---
export async function getStaticPaths() {
  const pages = [
    {
      slug: undefined,
      title: "Astro Store",
      text: "Welcome to the Astro store!",
    },
    {
      slug: "products",
      title: "Astro products",
      text: "We have lots of products for you",
    },
    {
      slug: "products/astro-handbook",
      title: "The ultimate Astro handbook",
      text: "If you want to learn Astro, you must read this book.",
    },
  ];
  return pages.map(({ slug, title, text }) => {
    return {
      params: { slug },
      props: { title, text },
    };
  });
}

const { title, text } = Astro.props;
---

<html>
  <head>
    <title>{title}</title>
  </head>
  <body>
    <h1>{title}</h1>
    <p>{text}</p>
  </body>
</html>
```

### Server (SSR) Mode

In [SSR mode](/en/guides/server-side-rendering/), dynamic routes are defined the same way: include `[param]` or `[...path]` brackets in your file names to match arbitrary strings or paths. But because the routes are no longer built ahead of time, the page will be served to any matching route. Since these are not "static" routes, `getStaticPaths` should not be used.

```astro title="src/pages/resources/[resource]/[id].astro"
---
const { resource, id } = Astro.params;
---

<h1>{resource}: {id}</h1>
```

This page will be served for any value of `resource` and `id`: `resources/users/1`, `resources/colors/blue`, etc.

#### Modifying the `[...slug]` example for SSR

Because SSR pages can't use `getStaticPaths()`, they can't receive props. The [previous example](#example-dynamic-pages-at-multiple-levels) can be adapted for SSR mode by looking up the value of the `slug` param in an object. If the route is at the root ("/"), the slug param will be `undefined`. If the value doesn't exist in the object, we redirect to a 404 page.

```astro title="src/pages/[...slug].astro"
---
const pages = [
  {
    slug: undefined,
    title: "Astro Store",
    text: "Welcome to the Astro store!",
  },
  {
    slug: "products",
    title: "Astro products",
    text: "We have lots of products for you",
  },
  {
    slug: "products/astro-handbook",
    title: "The ultimate Astro handbook",
    text: "If you want to learn Astro, you must read this book.",
  },
];

const { slug } = Astro.params;
const page = pages.find((page) => page.slug === slug);
if (!page) return Astro.redirect("/404");
const { title, text } = page;
---

<html>
  <head>
    <title>{title}</title>
  </head>
  <body>
    <h1>{title}</h1>
    <p>{text}</p>
  </body>
</html>
```

## Redirects

Sometimes you will need to redirect your readers to a new page, either permanently because your site structure has changed or in response to an action such as logging in to an authenticated route.

You can define rules to [redirect users to permanently-moved pages](#configured-redirects) in your Astro config. Or, [redirect users dynamically](#dynamic-redirects) as they use your site.

### Configured Redirects

<p><Since v="2.9.0" /></p>

You can specify a mapping of permanent redirects in your Astro config with the `redirects` value. For most redirects, this is a mapping of an old route to the new route:

```js title="astro.config.mjs" {4-6}
import { defineConfig } from "astro/config";

export default defineConfig({
  redirects: {
    "/old-page": "/new-page",
  },
});
```

These redirects follow the same rules as file-based routes. Dynamic routes are allowed as long as both the new and old routes contain the same parameters, for example:

```js
{
  "/blog/[...slug]": "/articles/[...slug]"
}
```

Using SSR or a static adapter, you can also provide an object as the value, allowing you to specify the `status` code in addition to the new `destination`:

```js title="astro.config.mjs" {5-8}
import { defineConfig } from "astro/config";

export default defineConfig({
  redirects: {
    "/old-page": {
      status: 302,
      destination: "/new-page",
    },
  },
});
```

When running `astro build`, Astro will output HTML files with the [meta refresh](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#examples) tag by default. Supported adapters will instead write out the host's configuration file with the redirects.

The status code is `301` by default. If building to HTML files the status code is not used by the server.

### Dynamic redirects

On the `Astro` global, the `Astro.redirect` method allows you to redirect to another page dynamically. You might do this after checking if the user is logged in by getting their session from a cookie.

```astro title="src/pages/account.astro" {8}
---
import { isLoggedIn } from "../utils";

const cookie = Astro.request.headers.get("cookie");

// If the user is not logged in, redirect them to the login page
if (!isLoggedIn(cookie)) {
  return Astro.redirect("/login");
}
---

<html>
  <!-- Page here... -->
</html>
```

## Route Priority Order

It's possible for multiple routes to match the same URL path. For example each of these routes would match `/posts/create`:

<FileTree>
- src/pages/
  - posts/
    - create.astro
    - [pid].astro
    - [...slug].astro
</FileTree>

Astro needs to know which route should be used to build the page. To do so, it sorts them according to the following rules:

- Static routes without path parameters will take precedence over all other routes
- Dynamic routes using named parameters take precedence over rest parameters
- Pre-rendered dynamic routes take precedence over server dynamic routes
- Rest parameters have the lowest priority
- Endpoints always take precedence over pages
- Ties are resolved alphabetically

Given the example above, here are a few examples of how the rules will match a requested URL to the route used to build the HTML:

- `pages/posts/create.astro` - Will build `/posts/create`
- `pages/posts/[pid].astro` - Will build `/posts/1`, `/posts/abc`, etc. But not `/posts/create`
- `pages/posts/[...slug].astro` - Will build `/posts/1/2`, `/posts/a/b/c`, etc. But not `/posts/create`, `/posts/1`, `/posts/abc`

Redirects also follow the same rules, but are prioritized _last_; if there is a file-based route and a redirect with the same route priority level, the file-based route is chosen.

## Pagination

Astro supports built-in pagination for large collections of data that need to be split into multiple pages. Astro will generate common pagination properties, including previous/next page URLs, total number of pages, and more.

Paginated route names should use the same `[bracket]` syntax as a standard dynamic route. For instance, the file name `/astronauts/[page].astro` will generate routes for `/astronauts/1`, `/astronauts/2`, etc, where `[page]` is the generated page number.

You can use the `paginate()` function to generate these pages for an array of values like so:

```astro /{ (paginate) }/ /paginate(.*)/ /(?<=const.*)(page)/ /page.[a-zA-Z]+/
---
// src/pages/astronauts/[page].astro
export async function getStaticPaths({ paginate }) {
  const astronautPages = [
    {
      astronaut: "Neil Armstrong",
    },
    {
      astronaut: "Buzz Aldrin",
    },
    {
      astronaut: "Sally Ride",
    },
    {
      astronaut: "John Glenn",
    },
  ];
  // Generate pages from our array of astronauts, with 2 to a page
  return paginate(astronautPages, { pageSize: 2 });
}
// All paginated data is passed on the "page" prop
const { page } = Astro.props;
---

<!--Display the current page number. Astro.params.page can also be used!-->
<h1>Page {page.currentPage}</h1>
<ul>
  <!--List the array of astronaut info-->
  {page.data.map(({ astronaut }) => <li>{astronaut}</li>)}
</ul>
```

This generates the following pages, with 2 items to a page:

- `/astronauts/1` - Page 1: Displays "Neil Armstrong" and "Buzz Aldrin"
- `/astronauts/2` - Page 2: Displays "Sally Ride" and "John Glenn"

### The `page` prop

When you use the `paginate()` function, each page will be passed its data via a `page` prop. The `page` prop has many useful properties, but here are the highlights:

- **page.data** - array containing the page’s slice of data that you passed to the `paginate()` function
- **page.url.next** - link to the next page in the set
- **page.url.prev** - link to the previous page in the set

```astro /(?<=const.*)(page)/ /page.[a-zA-Z]+(?:.(?:prev|next))?/
---
// src/pages/astronauts/[page].astro
// Paginate same list of { astronaut } objects as the previous example
export async function getStaticPaths({ paginate }) {
  /* ... */
}
const { page } = Astro.props;
---

<h1>Page {page.currentPage}</h1>
<ul>
  {page.data.map(({ astronaut }) => <li>{astronaut}</li>)}
</ul>
{page.url.prev ? <a href={page.url.prev}>Previous</a> : null}
{page.url.next ? <a href={page.url.next}>Next</a> : null}
```

#### Complete API reference

```ts
interface Page<T = any> {
  /** result */
  data: T[];
  /** metadata */
  /** the count of the first item on the page, starting from 0 */
  start: number;
  /** the count of the last item on the page, starting from 0 */
  end: number;
  /** total number of results */
  total: number;
  /** the current page number, starting from 1 */
  currentPage: number;
  /** number of items per page (default: 25) */
  size: number;
  /** number of last page */
  lastPage: number;
  url: {
    /** url of the current page */
    current: string;
    /** url of the previous page (if there is one) */
    prev: string | undefined;
    /** url of the next page (if there is one) */
    next: string | undefined;
  };
}
```

### Nested Pagination

A more advanced use-case for pagination is **nested pagination.** This is when pagination is combined with other dynamic route params. You can use nested pagination to group your paginated collection by some property or tag.

For example, if you want to group your paginated Markdown posts by some tag, you would use nested pagination by creating a `/src/pages/[tag]/[page].astro` page that would match the following URLS:

- `/red/1` (tag=red)
- `/red/2` (tag=red)
- `/blue/1` (tag=blue)
- `/green/1` (tag=green)

Nested pagination works by returning an array of `paginate()` results from `getStaticPaths()`, one for each grouping.

In the following example, we will implement nested pagination to build the URLs listed above:

```astro /(?:[(]|=== )(tag)/ "params: { tag }" /const [{ ]*(page|params)/
---
// src/pages/[tag]/[page].astro
export async function getStaticPaths({ paginate }) {
  const allTags = ["red", "blue", "green"];
  const allPosts = await Astro.glob("../../posts/*.md");
  // For every tag, return a paginate() result.
  // Make sure that you pass `{params: {tag}}` to `paginate()`
  // so that Astro knows which tag grouping the result is for.
  return allTags.flatMap((tag) => {
    const filteredPosts = allPosts.filter(
      (post) => post.frontmatter.tag === tag,
    );
    return paginate(filteredPosts, {
      params: { tag },
      pageSize: 10,
    });
  });
}
const { page } = Astro.props;
const params = Astro.params;
---
```

## Excluding pages

You can exclude pages or directories from being built by prefixing their names with an underscore (`_`). Files with the `_` prefix won't be recognized by the router and won't be placed into the `dist/` directory.

You can use this to temporarily disable pages, and also to put tests, utilities, and components in the same folder as their related pages.

In this example, only `src/pages/index.astro` and `src/pages/posts/post1.md` will be built as page routes and HTML files.

<FileTree>
- src/pages/
  - _hidden-directory/
    - page1.md
    - page2.md
  - _hidden-page.astro
  - **index.astro**
  - posts/
    - _SomeComponent.astro
    - _utils.js
    - **post1.md**
</FileTree>
