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
- Tool pages include a fixed-position back button in the top-left corner (see Back Button Pattern below)
- Pressing Escape returns to `index.html` - add this handler to each tool:
```javascript
document.addEventListener('keydown', (e) => {
    if (e.key === 'Escape') {
        window.location.href = 'index.html';
    }
});
```
- Light/dark mode toggle is shared across all pages using `localStorage` key `theme-preference`. Add this to each tool:

**HTML** (place after back button):
```html
<button class="theme-toggle" id="theme-toggle" aria-label="Toggle theme">
    <i class="fa-solid fa-sun"></i>
</button>
```

**CSS** (theme toggle button styles):
```css
.theme-toggle {
    position: fixed;
    top: 1.5rem;
    right: 1.5rem;
    background: rgba(255, 255, 255, 0.2);
    border: none;
    color: white;
    width: 44px;
    height: 44px;
    border-radius: 50%;
    cursor: pointer;
    font-size: 1.2rem;
    transition: all 0.2s ease;
}
body.light-mode .theme-toggle {
    background: rgba(0, 0, 0, 0.1);
    color: #333;
}
```

**JavaScript** (place at start of script):
```javascript
const THEME_KEY = 'theme-preference';
const themeToggle = document.getElementById('theme-toggle');
const themeIcon = themeToggle.querySelector('i');

function setTheme(isLight) {
    document.body.classList.toggle('light-mode', isLight);
    themeIcon.className = isLight ? 'fa-solid fa-moon' : 'fa-solid fa-sun';
    localStorage.setItem(THEME_KEY, isLight ? 'light' : 'dark');
}

const savedTheme = localStorage.getItem(THEME_KEY);
if (savedTheme === 'light') setTheme(true);

themeToggle.addEventListener('click', () => {
    setTheme(!document.body.classList.contains('light-mode'));
});
```

## Back Button Pattern

Each tool page has a fixed-position back button in the top-left corner with icon + text label.

**HTML** (place at start of body):
```html
<a href="index.html" class="back-btn">
    <i class="fa-solid fa-arrow-left"></i>
    <span>Back to Tools</span>
</a>
```

**CSS** (back button styles):
```css
.back-btn {
    position: fixed;
    top: 1.5rem;
    left: 1.5rem;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    color: white;
    text-decoration: none;
    font-size: 1rem;
    padding: 0.5rem 1rem;
    border-radius: 8px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.2);
    transition: background 0.2s ease;
}

.back-btn:hover {
    background: rgba(255, 255, 255, 0.2);
}

body.light-mode .back-btn {
    background: rgba(<accent-rgb>, 0.1);
    border-color: transparent;
    color: <accent-color>;
}

body.light-mode .back-btn:hover {
    background: rgba(<accent-rgb>, 0.2);
}
```

## Copy Button Pattern

Use clipboard icons (`fa-copy`) for copy functionality. On successful copy, change the icon to a checkmark (`fa-check`) with visual feedback.

**Primary actions** (standalone buttons, main copy action): Use icon + text label
```html
<button class="copy-btn">
    <i class="fa-solid fa-copy"></i>
    <span>Copy</span>
</button>
```

**Inline/secondary actions** (per-field copy, compact UI): Use icon only
```html
<button class="copy-field-btn"><i class="fa-solid fa-copy"></i></button>
```

**JavaScript:**
```javascript
async function copyToClipboard(text, button) {
    try {
        await navigator.clipboard.writeText(text);
        const icon = button.querySelector('i');
        const span = button.querySelector('span');
        icon.className = 'fa-solid fa-check';
        button.classList.add('copied');
        if (span) span.textContent = 'Copied!';
        setTimeout(() => {
            icon.className = 'fa-solid fa-copy';
            button.classList.remove('copied');
            if (span) span.textContent = 'Copy';
        }, 1500);
    } catch (err) {
        console.error('Failed to copy:', err);
    }
}
```

## Tool-Specific Theming

Each tool should have its own accent color (the same color used for its icon on `index.html`). Apply this color to theming as follows:

**Dark mode background:** Use a gradient based on the tool's accent color:
```css
body {
    background: linear-gradient(135deg, <darker-shade> 0%, <medium-shade> 50%, <accent-color> 100%);
}
```

**Light mode:** Use the standard light background, but style interactive elements (buttons, back button, theme toggle) with a transparent background and the accent color as text:
```css
body.light-mode {
    background: linear-gradient(135deg, #f5f7fa 0%, #e4e8ec 50%, #d5dbe1 100%);
    color: #1a1a2e;
}

body.light-mode .back-btn,
body.light-mode .theme-toggle,
body.light-mode button {
    background: rgba(<accent-rgb>, 0.1);
    border-color: transparent;
    color: <accent-color>;
}

body.light-mode .back-btn:hover,
body.light-mode .theme-toggle:hover,
body.light-mode button:hover {
    background: rgba(<accent-rgb>, 0.2);
}
```

Example for scratchpad (accent color `#f0ad4e` / `rgb(240, 173, 78)`):
- Dark mode gradient: `#c48a2a` → `#d9993a` → `#f0ad4e`
- Light mode buttons: `background: rgba(240, 173, 78, 0.1)`, `color: #f0ad4e`, hover: `rgba(240, 173, 78, 0.2)`

## Git Commits

Use conventional commits format:

**Tool-specific changes:** `type(tool-name): description`
```
feat(pomodoro-timer): add youtube audio support
fix(scratchpad): add support for tabs
```

**General changes:** `type: description`
```
docs: add readme
chore: add mise toml
```

**Types:**
- `feat` - new feature or functionality
- `fix` - bug fix
- `docs` - documentation only
- `chore` - maintenance, config, or non-code changes

Keep descriptions lowercase and concise. Single line only - no body or extended description.
