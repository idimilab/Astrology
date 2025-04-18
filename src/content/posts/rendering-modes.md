---
title: Rendering Modes
slug: rendering-modes
description: Rendering Modes
category:
  - One
tags:
  - Mollis
  - Astro
  - Jamstack
pubDate: 2023-09-01
cover: https://images.unsplash.com/photo-1486492440844-ebc195542a40?w=1960&h=1102&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8NjZ8fGJsYWNrfGVufDB8MHwwfHx8Mg%3D%3D
coverAlt: Astrology-Aliases
author: VV
---

Your Astro project code must be **rendered** to HTML in order to be displayed on the web.

Astro pages, routes, and API endpoints can be either [pre-rendered at build time](#pre-rendered) or [rendered on demand by a server](#on-demand-rendered) when a route is requested. With [Astro islands](/en/concepts/islands/), you can also include some client-side rendering when necessary.

In Astro, most of the processing occurs on the server, instead of in the browser. This generally makes your site or app faster than client-side rendering when viewed on less-powerful devices or on slower internet connections. Server-rendered HTML is fast, SEO friendly, and accessible by default.

## Server `output` modes

You can configure how your pages are rendered in your [`output` configuration](/en/reference/configuration-reference/#output).

### Pre-rendered

The **default rendering mode is **`output: 'static'`\*\*\*\*, which creates the HTML for all your page routes at build time.

In this mode, **your entire site will be pre-rendered** and the server will have all pages built ahead of time and ready to send to the browser. The same HTML document is sent to the browser for every visitor, and a full-site rebuild is required to update the contents of the page. This method is also known as **static site generation (SSG)**.

By default, all Astro projects are configured to be pre-rendered at build time (statically-generated) to provide the most lightweight browser experience. The browser does not need to wait for any HTML to build because the server does not need to generate any pages on demand. Your site is not dependent on the performance of a backend data source, and once built, will remain available to visitors as a static site as long as your server is functioning.

Static sites can include [Astro islands](/en/concepts/islands/) for interactive UI components (or even entire embedded client-side rendered apps!) written in the [UI framework of your choice](/en/core-concepts/framework-components/) in an otherwise static, pre-rendered page.

Astro's [View Transitions API](/en/guides/view-transitions/) are also available in `static` mode for animation and state persistence across page navigation. Static sites can also use [middleware](/en/guides/middleware/) to intercept and transform response data from a request.

:::tip
Astro's default `static` mode is a powerful, modern-feeling choice for content-heavy sites that update infrequently, and display the same page content to all visitors.
:::

### On-demand rendered

Astro's other two output modes can be configured to enable **on-demand rendering of some or all of your pages, routes or API endpoints**:

- **`output: 'server'`** for highly dynamic sites with most or all on-demand routes.
- **`output: 'hybrid'`** for mostly static sites with some on-demand routes.

Since they are generated per visit, these routes can be customized for each viewer. For example, a page rendered on demand can show a logged-in user their account information or display freshly updated data without requiring a full-site rebuild. On-demand rendering on the server at request time is also known as **server-side rendering (SSR)**.

[Consider enabling `server` or `hybrid` mode](/en/guides/server-side-rendering/#enable-on-demand-server-rendering) in your Astro project if you need the following:

- **API endpoints**: Create specific pages that function as API endpoints for tasks like database access, authentication, and authorization while keeping sensitive data hidden from the client.

- **Protected pages**: Restrict access to a page based on user privileges, by handling user access on the server.

- **Frequently changing content**: Generate individual pages without requiring a static rebuild of your site. This is useful when the content of a page updates frequently, for example displaying data from an API called dynamically with `fetch()`.

Both `server` and `hybrid` output modes allow you to include [Astro islands](/en/concepts/islands/) for interactivity (or even entire embedded client-side rendered apps!) in your choice of [UI frameworks](/en/core-concepts/framework-components/). With [middleware](/en/guides/middleware/) and Astro's [View Transitions API](/en/guides/view-transitions/) for animations and preserving state across route navigations, even highly interactive apps are possible.

:::tip
On demand server-rendering in Astro provides a true app experience without the JavaScript overhead of a client-side, single-page application.
:::
