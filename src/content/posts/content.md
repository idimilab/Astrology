---
title: Astro content
slug: content-astro
description: Content collections help organize your Markdown and type-check your frontmatter with schemas.
category:
  - Two
tags:
  - Tailwind
  - Astro
  - Jamstack
pubDate: 2023-09-02
cover: https://images.unsplash.com/photo-1527607976958-7cbb4a6d0131?w=1960&h=1102&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8Mzh8fGJsYWNrfGVufDB8MHwwfHx8Mg%3D%3D
coverAlt: Astrology-Content Collections
author: VV
---

c is a perfect choice for your content-focused site: blogs, marketing sites, portfolios, and more!

Astro helps you author and present your content. You can write a blog post directly in Astro using Markdown/MDX, or fetch your content from a headless CMS. Astro lets you build a site around your content: you can add a layout to your pages, create an index of posts, and set up an RSS feed to allow readers to subscribe.

## Writing Content

In Astro, you can author your content in a variety of ways:

- In Markdown files (`.md` or [alternative extensions](/en/guides/markdown-content/)), designed to make it easy to write rich text content.
- In MDX (`.mdx`) files, which let you include components and dynamic expressions in your document.
- Using a third-party content management system (CMS), then pulling that content into a `.astro` page.
- Other options (less commonly used for content-heavy pages) include [`.astro` files](/en/core-concepts/astro-pages/#astro-pages) and [`.html` files](/en/core-concepts/astro-pages/#html-pages).

### Markdown Authoring

Markdown is a convenient syntax for writing rich text with basic formatting and common elements like headers, lists, and images. Astro has built-in support for Markdown files in your project.

Create and write a new `.md` file in your code editor or bring in an existing file written in your favorite Markdown editor. Some online Markdown editors like [StackEdit](https://stackedit.io/) and [Dillinger](https://dillinger.io) will even allow you to edit and sync your work with your Astro repository stored on GitHub.

📚 Learn more about [writing Markdown content in Astro](/en/guides/markdown-content/).

### MDX Authoring

If you add the MDX integration to your project, you can also write content using `.mdx` files, which let you include JavaScript expressions and custom components within your Markdown. This includes both static [Astro components](/en/core-concepts/astro-components/) and interactive [framework components](/en/core-concepts/framework-components/). Add UI elements such as a banner or an interactive carousel right in your text to turn your content into full-fledged web pages.

Write and edit `.mdx` files directly in your code editor, alongside your project files.

📚 Learn more about [using MDX with Astro](/en/guides/integrations-guide/mdx/).

### Headless CMS Authoring

Write blog posts in your existing Content Management System (CMS) such as Storyblok, WordPress, or Contentful. Some CMSes, like Storyblok, provide an official [Astro integration](https://www.storyblok.com/mp/announcing-storyblok-astro). Others expose a JavaScript SDK that Astro pages can use to [fetch your remote content](/en/guides/data-fetching/#fetch-from-a-headless-cms).

## Managing content pages

Markdown and MDX files that live in your `src/pages` directory will automatically generate pages on your site using Astro's [file-based routing](/en/core-concepts/routing/), built at a URL corresponding to the post's file path.

You can also choose to keep your Markdown and MDX files outside of the `src/pages` directory, and instead [import their content](/en/guides/markdown-content/#importing-markdown) into `.astro` pages.

If you're writing your content in a CMS, you can fetch your posts and use [dynamic routing](/en/core-concepts/routing/#dynamic-routes) to use one `.astro` file to generate a route for each post. In Astro's default static mode, these routes are generated at build time. If you opt-in to [SSR mode](/en/guides/server-side-rendering/), you respond to a request at runtime and fetch the content on demand.

## Showcasing your content

To build common features to organize and display your content, such as a blog archive or a page for each blog tag, Astro allows you to [fetch filenames and metadata](/en/reference/api-reference/#astroglob) from your Markdown and MDX frontmatter and use these to generate page content and routes.

## Community Integrations

In addition to the official [`@astrojs/mdx`](/en/guides/integrations-guide/mdx/) integration, there are several third-party [community integrations](https://astro.build/integrations/?search=&categories%5B%5D=css%2Bui) for working with content in your Astro project.
