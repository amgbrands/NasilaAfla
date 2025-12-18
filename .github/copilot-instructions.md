# GitHub Copilot Instructions — Nasila Afla

Purpose: provide an AI coding agent with the minimum, actionable context to be productive in this small static website repo.

## Big picture
- Static single-page site with a small store prototype. Primary files: `index.html`, `cart.html`, and `styles.css`.
- No backend: cart and product state are stored in browser `localStorage` and payment instructions are manual (M-PESA details shown in the UI).

## Key components & data flows
- `index.html`
  - Landing, projects, and store UI.
  - Product add-to-cart buttons: elements with `.buy-btn` and a `data-price` attribute. When clicked they read the product `h3` text for the product name and `data-price` for the price and update `localStorage` (key: `cart`), then redirect to `cart.html`.
- `cart.html`
  - Reads/writes `localStorage.getItem('cart')` and manages quantities, totals and order placement.
  - Has an in-file `products` array used to initialize the cart when `localStorage` is empty: keep this array in sync with products used in `index.html`.
  - `placeOrder()` shows a modal with payment instructions and calls `clearOrder()` (resets quantities to 0) only when the user closes the modal.
- `styles.css`
  - Central styling using CSS variables (e.g., `--primary`, `--accent`). Inline styles are also used in the HTML for small page-specific tweaks.

## Project-specific conventions & gotchas (essential)
- Persistent key: `localStorage` key is `cart`. Any refactor touching cart persistence must respect that key or migrate existing data.
- Product identity: product items are matched by exact `name` string (no stable id). If you change a product's display `h3` text in `index.html`, also update `cart.html` `products` initial array or move to a single source of truth (recommendation: introduce a small `products.json` or `data.js` to centralize product ids/names/prices).
- Price mismatch: some prices are inconsistent between `index.html` data-price and `cart.html` `products` — fix both places when changing prices.
- No build step: this repo is served as static files. Edits are tested by opening `index.html`/`cart.html` in a browser or running a static server.
- Inline JS: much behavior is implemented as inline scripts inside HTML files — prefer moving to `.js` files when altering logic for better testability and reuse.
- Special characters: product names use non-ASCII characters (e.g., `Caffè Macchiato`). Use UTF-8 and be careful when comparing strings.
- Payment info: MPESA number and Till are present in `cart.html`. Do not modify payment contact info without confirmation from a repository owner.

## How to run & verify locally (quick commands)
- Quick test (no tools): open `index.html` in your browser (double-click or use editor "Open in Browser").
- Using a local static server (recommended to avoid some Chrome local-file restrictions):
  - Node: `npx http-server` (visit http://localhost:8080)
  - Python: `python -m http.server 8000` (visit http://localhost:8000)

Manual test checklist for any change that touches store/cart behavior:
1. Start server and open site.
2. Click a product `Add to Cart` on `index.html` and confirm redirect to `cart.html`.
3. Verify `localStorage` key `cart` contains expected items (`Application` tab in DevTools).
4. Change quantities, use `Place Order` to validate the modal displays exact total, then confirm cart resets when closing modal.
5. Try navigation away and confirm `onbeforeunload` prompt triggers when cart has items.

## Debugging tips
- Use browser DevTools console to inspect `localStorage` and JS errors.
- To simulate different product states, set `localStorage.setItem('cart', JSON.stringify([...]))` in the console.
- Watch for string equality when matching `name` (case-sensitive). Prefer adding a stable `id` if you add product management features.

## Suggested small refactors an AI agent can do (safe, high-value)
- Move inline JS into `src/` or `assets/` JS files and add a small `main.js`, keeping the page `<script src="...">` entry. Update `README` with local test instructions.
- Centralize product data into a single file (e.g., `data/products.json` or `data.js`) and update `index.html`/`cart.html` to consume it. Ensure backward-compatible `localStorage` migration when changing the object shape.
- Unify price sources so you don't have inconsistent pricing between pages.

## Rules for AI changes (strict)
- Do not invent or change payment contact information or phone numbers without explicit human confirmation.
- Keep behavior backward-compatible: preserve `localStorage` key `cart` unless migrating with an explicit migration step and tests documented in the PR description.
- When changing product identifiers, update both `index.html` and `cart.html` or centralize the data first.

---
Please review this file and tell me if you want more detail on any of the areas (e.g., a suggested migration snippet for `localStorage`, or an example `products.json`).
