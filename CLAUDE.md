# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**HydroBiz** is a single-file, zero-build, zero-dependency business website template (`index.html`) designed to run on GitHub Pages. It includes a public-facing business site (products, gallery, testimonials, contact) and a hidden SHA-256-protected admin dashboard for non-technical business owners to manage their content entirely via browser localStorage.

Key constraints:
- **Single file only** — everything (HTML markup, CSS, vanilla JS) lives in `index.html`
- **Zero build process** — no Node, npm, webpack, or transpilation
- **No dependencies** — only Web APIs and vanilla JS (no frameworks)
- **GitHub Pages hosting** — each business gets a fully isolated copy of the template in its own repo
- **localStorage persistence** — ~5–10MB quota per site, images stored as base64

## Architecture & Code Structure

### Markup (HTML, ~1050 lines)
- `<head>`: meta tags, Google Fonts CDN (Inter, DM Sans, JetBrains Mono), dynamic favicon via `<link>`
- `<body>`: preloader animation, public site sections (9 sections: nav, hero, products, packages, about, gallery, testimonials, contact, footer), cart drawer, quick-info drawer, modals (admin login, customer info, confirm password), lightbox, toast notification
- DOM elements get filled by JS render functions; a few key IDs to know: `#site-nav`, `#admin-dashboard`, `#cart-drawer`, `#customer-info-modal`, `#admin-body`

### CSS (~650 lines, organized into blocks)
Blocks are clearly marked with `/* ============ LABEL ============ */` comments:
- `:root` token definitions (colors, fonts, glass effects, easing curves)
- Utility classes (`.card`, `.btn`, `.glass-panel`, `.field`)
- Layout/component blocks (nav, hero, products grid, gallery, testimonials, contact, footer, admin dashboard, modals)
- 3 layout variants scoped via `body[data-layout="classic|split|editorial"]` attribute selectors
- No CSS-in-JS; all responsive via `@media` queries (mobile-first, breakpoints: 480/620/860/980/1280px)

### JavaScript (~3700 lines, organized into logical sections)
Sections (marked with comment blocks):

1. **CONSTANTS** — `CONFIG_KEY`, entity caps (`MAX_GALLERY=12`), `QTY_UNITS`, default theme colors
2. **UTILS** — `sha256()` (Web Crypto), `fileToBase64()`, `debounce()`, `escapeHTML()`, `deepMerge()`, `getPath()`/`setPath()` for nested object mutations, id generator `uid()`
3. **STATE** — `CONFIG` (loaded from localStorage), `MASTERY` (onboarding tour progress, localStorage, excluded from backup), `CART` (shopping cart array)
4. **CLOUD SYNC** — optional Firebase Firestore integration, opt-in per business, handled via `cloudSyncInit()`, `cloudSyncPush()`, `applyRemoteConfig()`
5. **RENDER LAYER** — functions that read `CONFIG` and populate the public site: `renderAll()` (master entry), `renderNavCta()`, `renderHero()`, `renderProducts()`, `renderCart()`, etc. These fire on every CONFIG change so edits appear instantly
6. **ADMIN DASHBOARD** — `openAdminDashboard()`, `selectAdminSection()`, `rerenderAdminBody()`, and for each section: `panel<Section>()` (builds HTML) + `bind<Section>Actions()` (attaches listeners)
7. **ADMIN SECTIONS** — identity, socials, products, packages, gallery, testimonials, about, drawer, appearance, cloud sync, QR code, orders, analytics, notifications, trash, password, data
8. **CART UI** — cart state, customer info modal, checkout flow
9. **AUTHENTICATION** — SHA-256 password hash, login modal, grace period (re-entry within 5 min without re-password), security questions, password recovery
10. **UTILITIES** (misc.) — lightbox, QR code generation, phone validation, ripple divider SVG, icon inline SVGs, toast notifications

### State Management

**CONFIG object** — user-editable site content, persisted to localStorage under key `'hydrobiz_config'`. Structure:
```
identity: {name, tagline, logo_b64, favicon_b64, email, phone, hours, hero_headline, hero_sub, hero_image_b64, location_address, ...}
socials: {facebook, instagram, twitter, tiktok, youtube, linkedin, whatsapp, visibility}
products: [{id, name, price, currency, description, category, quantity, stock, image_b64, video_url, ...}]
packages: [{id, name, price, currency, features, ...}]
gallery, testimonials, about: {story, team: [...]}, drawer: {highlights}
appearance: {layout, sections_visible, whatsapp_btn, cta_label, cta_anchor, footer_tagline, theme, fonts, qr_style, order_msg_template}
orders, notifications: [{...}] (logged internally)
admin_security: {idle_timeout_min}
password_hash: null | sha256_hex_string
password_recovery: {q1, q2, a1_hash, a2_hash}
cloud_sync: {enabled, api_key, ...firebase_config}
```

**MASTERY object** — per-device UI-familiarity signal for onboarding tour, localStorage only, excluded from export/import/reset.

**CART array** — `[{id, type ('product'|'package'), name, qty, price, currency, image_b64, ...}]`, in-memory, NOT persisted (fresh on page load).

### Key Patterns

**Save pipeline:** `CONFIG` mutation → `commitField(path, value)` (calls `setPath()` + `debouncedSaveAndToast()`) OR `commitNow(msg)` (immediate + optional toast). Both call `saveConfig()` which persists to localStorage, logs to `orders` if it's a checkout, and triggers cloud sync if enabled.

**Rollback-on-failure:** entity add/edit in admin saves a backup of the existing entity; if `commitNow()` returns false (storage full), reverts the in-memory mutation before re-render, ensuring the admin UI never shows a deceptive "saved" entity that didn't persist.

**Phone validation:** `phoneFormatWarning(value)` returns a user-facing string if the phone is invalid format (no leading 0, no +, no spaces, etc.), or `''` if valid. Used in identity phone field, customer info modal, and as live validation on multiple input fields.

**Event delegation:** checkout, product management, gallery reorder, and other grid operations use delegated listeners on container elements (e.g. `#products-grid` or `#admin-body`), not individual elements, to handle dynamic content efficiently.

**Modal flow:** customer info modal (`#customer-info-modal`) opens before checkout, validates name/address/phone, stores in `customerInfo` variable, then calls `proceedToCheckout(channel)` which builds the order message using `buildOrderMessage()` (interpolates the template `CONFIG.appearance.order_msg_template` with placeholders `{business}`, `{items}`, `{total}`, `{name}`, `{address}`, `{phone}`).

**Image handling:** images stored as base64 data URLs in `CONFIG` (e.g. `logo_b64`, `image_b64`). Size validation happens in `fileToBase64()` (max 6MB). On upload, a draft variable is set immediately (e.g. `heroImageDraft`) so preview updates instantly without wasting a CONFIG save/render cycle; final persist happens on form submit.

**Soft delete:** products, packages, gallery, testimonials, about.team, drawer.highlights, and orders can have a `deleted_at` timestamp. `activeList(path)` filters them out. 30-day retention before auto-purge via `purgeOldTrashedItems()`.

## Running and Testing

### Local Development

**Serve locally:**
```bash
python3 -m http.server 8910  # from repo root
# Site: http://localhost:8910/index.html
```

**Self-test (built-in):**
```
http://localhost:8910/index.html?selftest=1
# Checks: sha256, fileToBase64, debounce, deepMerge, config save/load round-trip, malformed JSON rejection, soft-delete.
# Results printed to browser console.
```

**Playwright regression tests** (examples in scratchpad):
```bash
# Requires: node, playwright (chromium cached at /opt/pw-browsers/chromium-1194)
node /path/to/test_customer_info.js
node /path/to/test_mobile_audit.js
# Each test sets up a fresh page, clears localStorage, populates CONFIG, and checks specific flows.
# Exit code 0 = pass, 1 = fail.
```

Common test patterns:
- `await page.evaluate(() => { CONFIG.x = y; renderAll(); })` to set state
- `await page.evaluate(() => { MASTERY.welcomed = true; saveMastery(); })` to skip onboarding
- `await page.waitForTimeout(200)` between interactions (debounce/render latency)
- `await page.fill('#input-id', 'value')` for form input
- `await page.click('#button-id')` to trigger actions

### Git Workflow

**Development branch:** `claude/hydrobiz-github-pages-kwfjw2`

**Commit guidelines:**
- Always create NEW commits (never amend, since pre-commit hooks might fail)
- Clear message summarizing the change (e.g. "Add customer info validation, fix checkout buttons")
- Co-author line with session link
- Push with `git push -u origin <branch>`

**No destructive operations** without explicit user permission (git reset --hard, force push, etc.).

## Important Constraints & Decisions

1. **Single file forever** — anything added must stay inside `index.html`. If a change would benefit from a separate file, refactor the inline code instead (clearer sections, more comments, helper functions).

2. **No localStorage assumptions** — quota can be exceeded (warn users), and storage.setItem() can throw. Always wrap in try-catch when saving large data.

3. **No external dependencies** — Google Fonts CDN is loaded via plain `<link>`, Firebase SDK loaded conditionally via `<script>` tag only if Cloud Sync is enabled. No npm, no bundler.

4. **Phone format is strict** — must be digit-only with country code (no +, no leading 0, no spaces, 10–15 digits). `waLink()` auto-normalizes 00-prefix to country code for WhatsApp.

5. **localStorage is per-device** — if a user edits on phone A, then phone B, they need to manually `export` from A and `import` on B. Cloud Sync is opt-in to avoid this.

6. **Admin password is not recoverable** — only the SHA-256 hash is stored (never plaintext). Security questions let the owner set a new password themselves. If both password and security questions are lost, only reset (wipe to defaults) is available.

7. **Soft-delete retention is 30 days** — `TRASH_RETENTION_MS = 30 * 24 * 60 * 60 * 1000`. Permanent deletion happens on next page load after the window expires.

8. **Order messages use a template** — `CONFIG.appearance.order_msg_template` is customizable by the admin (Appearance section, textarea). Placeholders: `{business}`, `{items}`, `{total}`, `{name}`, `{address}`, `{phone}`. All checkout channels (WhatsApp, SMS, Email, Call) interpolate the same template via `buildOrderMessage()`.

9. **Rollback-on-failure is mandatory** — any admin save (entity add/edit) that calls `commitNow()` must store a backup beforehand and restore if `commitNow()` returns false. This prevents silently losing work due to storage quota.

10. **Auto-lock after idle** — admin dashboard locks after `CONFIG.admin_security.idle_timeout_min` minutes (default 5). Timer resets on any interaction. Can be disabled by setting to 0 (not recommended for security, but supported).

## Common Tasks

**Adding a new admin section:**
1. Add fields to `DEFAULT_CONFIG` (e.g. `CONFIG.newsection = {...}`)
2. Create `panelNewsection()` function (returns HTML string)
3. Create `bindNewSectionActions()` function (attach event listeners)
4. Add to `ADMIN_SECTIONS` array and `ADMIN_BINDERS` object
5. Add to `ADMIN_HELP` if onboarding guide needed

**Adding a phone number field:**
- Wrap in `<div class="a-field">` with label
- Add a `data-path="path.to.phone"` attribute (auto-picked up by delegated save listener)
- Optionally add a sibling `<div class="field-warn" id="some-warn"></div>` for live validation
- In bind function, attach `input` listener that calls `phoneFormatWarning()` and updates the warning

**Persisting a new field:**
- Make sure it's in `DEFAULT_CONFIG` structure
- If it's a scalar: `commitField(path, value)` handles save + debounce + toast
- If it's a form input: add `data-path="x.y.z"` and the delegated listener in `bindAdminPanelActions()` will auto-save
- If it's a file (image): use `fileToBase64()`, set a draft variable for instant preview, then save on form submit

**Testing a new feature:**
1. Start local server: `python3 -m http.server 8910`
2. Open `http://localhost:8910/index.html` in browser
3. If testing admin: long-press hamburger icon, set password, log in
4. For automated tests: write a Playwright script in scratchpad (see examples), run with `node`

**Debugging localStorage issues:**
- Open DevTools → Application → Storage → Local Storage
- Look for key `hydrobiz_config` (site data) and `hb_admin_mastery` (tour progress)
- Can manually delete to reset (clears all content, not just one field)
- `?selftest=1` in URL verifies round-trip save/load works

## Schema Notes

- `id` fields: generated via `uid()` (date timestamp + random hex)
- `_b64` fields: base64 data URLs (e.g. `data:image/png;base64,...`)
- `deleted_at`: unix timestamp (ms) when soft-deleted; omit = not deleted
- `currency` on products/packages: any string (e.g. 'KES', 'USD'), used in checkout message and analytics
- `category` on products: single string per product, used for filtering (category tabs)
- `photo_b64` on testimonials: optional, omit or empty string if no photo
- `*_hash` on password recovery: SHA-256 hex string (answers are hashed before storing)

## Performance & Storage Notes

- Debounce delay on `commitField()`: 500ms (batches rapid edits)
- Toast duration: 2.4s (success) or 4.8s (error), extended to 5s if action button present
- Lightbox zoom: 1–4x smoothly, stores current scale in `dataset`
- QR code generation: client-side only, no API call
- Cloud Sync: updates are queued if admin dashboard is open (to avoid clobbering unsaved edits), applied on close if newer than `lastLocalEditAt`
- Image size guard: 6MB hard limit per file, warns if total content approaches quota

## Resources

- **README.md** — user-facing docs, deployment steps, storage guidance, client handoff checklist
- **Self-test** — `?selftest=1` parameter runs built-in console checks
- **Virtual Manual** — admin dashboard book icon links to searchable guide + coachmark tours (data in `ADMIN_HELP`)
- **Printable manual** — Data section exports a branded PDF-able HTML manual (all sections, business info, contact details)
