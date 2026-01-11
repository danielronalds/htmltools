# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a collection of standalone HTML tools/utilities. Each tool is a self-contained HTML file with embedded CSS and JavaScript - no build system, bundler, or package manager.

## Development

**To preview:** Open any HTML file directly in a browser, or use a local server:
```bash
python3 -m http.server 8000
```

**No build or test commands** - this is a static HTML project.

## Architecture

- `index.html` - Home page with a searchable grid of tool cards
- Each tool is a separate HTML file (e.g., `pomodoro.html`) linked from the index
- All tools use Font Awesome icons via CDN (`https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css`)
- Shared visual style: dark gradient background, glassmorphism cards, consistent color palette

## Adding a New Tool

1. Create a new HTML file at the root (e.g., `newtool.html`)
2. Add a tool card to `index.html` in the `.tools-grid` div:
```html
<a href="newtool.html" class="tool-card newtool">
    <i class="fa-solid fa-icon-name tool-icon"></i>
    <div class="tool-name">Tool Name</div>
    <div class="tool-description">Brief description</div>
</a>
```
3. Add color styling for the icon in the `<style>` section:
```css
.tool-card.newtool .tool-icon {
    color: #hexcolor;
}
```
4. Include a "Back to Tools" link in the new tool pointing to `index.html`

## Patterns

- State persistence uses `localStorage` with a tool-specific key (e.g., `pomodoro-state`)
- Tool pages include a fixed-position back button in the top-left corner
