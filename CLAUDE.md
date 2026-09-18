# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Personal site / project blog for Jackson Dean, built with Jekyll and served by GitHub Pages at the custom domain in `CNAME` (`jacksondean.space`). Pushing to `main` deploys it. There is no GitHub Actions workflow; Pages builds it with the classic `github-pages` gem, so only plugins on the Pages allowlist work.

## Commands

```sh
bundle install              # first time
bundle exec jekyll serve    # local preview at http://localhost:4000 (add --livereload to auto-refresh)
bundle exec jekyll build    # build into _site/ (gitignored)
```

There are no tests or linters. To check a change, build or serve the site and look at the rendered page.

- `_config.yml` is **not** reloaded by `jekyll serve`. Restart the server after editing it.
- The `Gemfile` uses the `github-pages` gem (v232: Jekyll 3.10.0, minima 2.5.1) rather than a standalone `jekyll` gem, so local builds match production. Keep it that way. `bundle update` moves every pin to whatever `github-pages` specifies.
- `Gemfile.lock` only lists the `x64-mingw-ucrt` (Windows) platform. On Linux or macOS, run `bundle lock --add-platform <platform>` first.
- **Ruby version:** use Ruby 3.3 to match GitHub Pages, which builds with Ruby 3.3 (see `.ruby-version` in github/pages-gem). On Windows that is `winget install RubyInstallerTeam.RubyWithDevKit.3.3`. You need the DevKit because `wdm`, `eventmachine`, and `unf_ext` compile native extensions.
- **`exclude:` in `_config.yml`:** GitHub Pages turns any root-level Markdown file into a public page, even one with no front matter. Add repo-only docs such as this file to `exclude:`. In Jekyll 3.x that list replaces the default excludes rather than adding to them, so keep `Gemfile`, `CNAME`, and the rest listed there.

## Architecture

- **The theme is the `minima` gem, not local files.** The repo has no `_layouts/`, `_includes/`, or `_sass/`. To change a layout or a partial, copy that file from the installed minima 2.5.1 gem (`bundle info --path minima`) to the same relative path in this repo; the local copy then overrides the gem's. Styling for individual pages is currently done with inline `<style>` blocks.
- **Navigation:** `header_pages` in `_config.yml` sets which pages appear in the header, and right now that is only `about.markdown`. A new page only shows up in the nav if you add it to that list.
- **Site variables:** `_config.yml` defines a custom `site.portrait_url` key that pages read through Liquid. The resume was deliberately removed from the site; don't add it back.
- **Config keys that do nothing:** `minima.skin` is a minima 3.x setting, and nothing reads `substack_username`. Neither has any effect under minima 2.5.1.
- **`rickroll.html`** (permalink `/rickroll/`, page title "Contact") is a standalone joke page that is not in the nav. Its inline CSS hides the minima header. JavaScript locks scrolling until the visitor clicks the arrow button, and that click sets the YouTube iframe's `src` (autoplay needs a user gesture). Below the video are links to `assets/other/contact.vcf` and `/about/`.

## Post conventions

Posts live in `_posts/` as `YYYY-MM-DD-slug.markdown`. Copy the structure of the existing posts:

```yaml
---
layout: post
title:  "Post Title"
date:   YYYY-MM-DD HH:MM:SS -0600
categories: projects
exerpt: "One-sentence summary."
images-path: /assets/images/posts/YYYY-MM-DD-slug/
---

_{{ page.exerpt }}_

![alt]({{ page.images-path | append: "glamour-shot.jpg" }}){:class="img-responsive"}
```

- `exerpt` is intentionally spelled that way. It is a custom front-matter key, separate from Jekyll's built-in `excerpt`, and every post body reads it as `{{ page.exerpt }}`. Do not "fix" the spelling in just one place.
- A post's images go in `assets/images/posts/<same filename stem as the post>/` and are referenced through `page.images-path | append: "..."`. Each post starts with a "glamour shot" image right after the italic summary.
- Section headings are written as `#### Heading` followed by a line containing `<hr>`. YouTube videos are embedded as raw `<iframe width="100%" height="400" ...>` tags.
