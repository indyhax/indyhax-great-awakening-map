# Flag Discovery Guide

This guide walks you through discovering the hidden CTF flags in the IndyHAX Great Awakening Map site. There are **two independent flags** to find — one spread across the dossier pages and one hidden inside the interactive mindmap.

---

## Flag 1: Dossier Flag (Three-Part Assembly)

The challenge page (`challenge.html`) tells you that three fragments are hidden in HTML source comments across the site. Open each page and **view the page source** (right-click → "View Page Source", or press `Ctrl+U`).

### Step 1 — Fragment A (Home Page)

1. Open `index.html` in your browser.
2. View the page source.
3. Scroll to the bottom — look for an HTML comment near the footer:
   ```html
   <!-- FLAG PART A: INDYHAX{thorough_archive_ -->
   ```
4. Record the fragment: `INDYHAX{thorough_archive_`

### Step 2 — Fragment B (Methodology Page)

1. Open `methodology.html`.
2. View the page source.
3. Near the bottom, find the comment:
   ```html
   <!-- FLAG PART B: map_network_context_ -->
   ```
4. Record the fragment: `map_network_context_`

### Step 3 — Fragment C (Challenge Page)

1. Open `challenge.html`.
2. View the page source.
3. Find the comment:
   ```html
   <!-- FLAG PART C: fully_explained} -->
   ```
4. Record the fragment: `fully_explained}`

### Assemble the Dossier Flag

Concatenate the three parts in order (A + B + C):

```
INDYHAX{thorough_archive_map_network_context_fully_explained}
```

---

## Flag 2: Mindmap Flag (Multi-Layered Hiding)

The mindmap page (`mindmap.html`) hides a single flag using several steganographic techniques layered throughout the SVG and surrounding HTML/CSS. You can discover it through **any** of the methods below — each one leads to the same flag.

### Method A — Base64 Data Attribute

1. Open `mindmap.html` and view the page source.
2. Find the `<svg>` element and look at its `data-cfg` attribute:
   ```html
   <svg id="mindmap-svg" ... data-cfg="SU5EWUhBWHttMW5kbTRwX24wZDNzX3IzdjM0bF90aDNfcDR0aH0=">
   ```
3. Decode the base64 string. In a terminal:
   ```bash
   echo 'SU5EWUhBWHttMW5kbTRwX24wZDNzX3IzdjM0bF90aDNfcDR0aH0=' | base64 -d
   ```
   Or use the browser console:
   ```js
   atob('SU5EWUhBWHttMW5kbTRwX24wZDNzX3IzdjM0bF90aDNfcDR0aH0=')
   ```
4. Result: `INDYHAX{m1ndm4p_n0d3s_r3v34l_th3_p4th}`

### Method B — SVG Metadata

1. In the page source, locate the `<metadata>` block inside the `<svg>` element.
2. Look for the `<dc:version>` tag:
   ```xml
   <dc:version>INDYHAX{m1ndm4p_n0d3s_r3v34l_th3_p4th}</dc:version>
   ```
3. The flag is right there in plain text.

### Method C — Hex-Encoded HTML Comments

1. Search the page source for HTML comments containing hex strings. You will find two:
   - Near the mindmap container:
     ```html
     <!-- render-hash: 6d31 6e64 6d34 70 -->
     ```
   - Inside the JavaScript (injected into the center SVG group):
     ```js
     " system calibration: 494e44594841587b "
     ```
2. Decode the hex values. For example, in a terminal:
   ```bash
   echo '494e44594841587b' | xxd -r -p
   ```
   Result: `INDYHAX{`
3. The `render-hash` hex `6d31 6e64 6d34 70` decodes to `m1ndm4p` (part of the flag body).

### Method D — SVG `<desc>` Element with Hex

1. In the JavaScript source, find the `<desc>` element created for branch index 0:
   ```js
   desc0.textContent="system calibration: 494e44594841587b6d316e646d34705f6e3064";
   ```
2. Decode the hex string:
   ```bash
   echo '494e44594841587b6d316e646d34705f6e3064' | xxd -r -p
   ```
   Result: `INDYHAX{m1ndm4p_n0d` — the flag prefix and the beginning of the flag body.

### Method E — Invisible SVG Text Element

1. In the JavaScript source, find the hidden text element created for branch index 3 (Secret Space Program):
   ```js
   ht.textContent="n0d3s_r3v34l_th3_p4th";
   ```
   This element is rendered with `opacity: 0` and `font-size: 0.1`, making it invisible on screen.
2. Alternatively, use the browser DevTools (F12 → Elements tab) and search for `n0d3s` to find the invisible `<text>` node in the DOM.

### Method F — CSS Class Clue

1. In the `<style>` block, notice the suspicious CSS class:
   ```css
   .f-l-a-g{display:none;visibility:hidden;width:0;height:0;overflow:hidden}
   ```
2. The class name `.f-l-a-g` itself is a hint that hidden flag content exists on this page.

### The Mindmap Flag

All methods above converge on the same flag:

```
INDYHAX{m1ndm4p_n0d3s_r3v34l_th3_p4th}
```

---

## Tips and Techniques

| Technique | What to look for |
|---|---|
| **View Source** | `Ctrl+U` or right-click → View Page Source |
| **Search in source** | `Ctrl+F` for keywords like `FLAG`, `INDYHAX`, `<!--`, `data-` |
| **DevTools Elements** | `F12` → Elements tab to inspect the live DOM |
| **Base64 decode** | `atob()` in console or `base64 -d` in terminal |
| **Hex decode** | `xxd -r -p` in terminal or an online hex decoder |
| **Grep the repo** | `grep -rn "FLAG\|INDYHAX\|data-cfg" *.html` |
