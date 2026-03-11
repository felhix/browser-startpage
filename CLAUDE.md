# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm install        # Install dev dependencies (sass, uglify-js, concurrently)
npm run build      # Compile SCSS to CSS + minify all JS (cleans dist/ first)
npm run watch      # Watch SCSS for changes and recompile automatically
```

Individual build steps:
```bash
npm run build:scss       # Compile SCSS to dist/css/compiled.css (unminified)
npm run build:scss:min   # Compile SCSS to dist/css/compiled.min.css (minified)
npm run build:js         # Minify all JS in assets/js/ to dist/js/
```

## Architecture

This is a static browser startpage — a single `index.html` file with no framework or bundler. It loads only `dist/js/async-loader.min.js` at startup; everything else is loaded on demand.

### Feature activation via DOM classes

Features are enabled by adding CSS classes to `<html>` or `<body>`:

- **Theme** — set on `<html>`: `default eldenring-ranni`, `default eldenring-start`, `default vapor`, `default halo`, etc.
- **Add-ons** — set on `<body>`: `fade-in`, `jquery`, `search`, `stars`, `ripples`
- **Slick carousel** — set on `.links`: `slick-start` (activates tabbed bookmark groups), `slick-single-arrow`
- **Privacy blur** — set on `<ul>`: `blur`

### Async loader (`assets/js/async-loader.js`)

`async-loader.js` inspects the DOM on load and conditionally injects `<script>` tags:
1. If `.jquery` class found → load jQuery, then run `jQueryScripts()`
2. `jQueryScripts()` loads slick carousel (if `.slick-start`) and jQuery Ripples (if `.ripples`)
3. Vanilla JS: loads `datetime.min.js` (if `#Date`), `stars.min.js` (if `.stars`), `search.min.js` (if `.textarea`)

### SCSS structure

`assets/scss/main.scss` is the entry point — it `@use`s three partials: `style`, `slick`, `slick-theme`. All custom styles are in `style.scss`. Compiled output goes to `dist/css/compiled.min.css`.

### Theming system

Themes are defined as CSS variable overrides on `html.theme-name` in `style.scss` under `/* Themes */`. The `--hue-rotate` variable drives the global color shift via `filter: hue-rotate()` on the `html` element. The image shown in the left panel is set via `--image`. Background images (separate from the panel image) use `--background-image` with opacity control via `--background-opacity`.

### Bookmark layout

Bookmarks are organized as `<nav class="bookmarks">` containing `<div class="category">` > `<div class="links">` > `<ul>`. Multiple `<ul>` inside a `.links.slick-start` div creates tabbed/carousel navigation. Multiple `.bookmarks` rows are supported.

### Source vs. dist

- Edit source files in `assets/js/` and `assets/scss/` — never edit `dist/` directly.
- `index.html` loads `dist/css/compiled.min.css` and `dist/js/async-loader.min.js`.
- After any JS or SCSS change, run `npm run build` to update `dist/`.
