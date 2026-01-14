# HTML Tools

A collection of standalone HTML utilities. Each tool is a self-contained HTML file with embedded CSS and JavaScript - no build system or dependencies required.

All tools are exclusively created through the use of AI. In fact I haven't looked at the code for most of them.

## Usage

The tools are hosted on GitHub pages, [here](https://danielronalds.github.io/htmltools/)

Optionally, they can be run locally. Each HTML file is completely self contained, and can be hosted statically.

## Development

With [mise](https://mise.jdx.dev/) installed, run the development server with live reloading:

```bash
mise run dev
```

Optionally specify a port:

```bash
mise run dev port=3000
```

## Features

- Dark/light mode toggle
- Keyboard navigation (Escape returns to home)
- State persistence via localStorage
- Minimal external dependencies (Font Awesome icons, lz-string compression, YouTube API)
