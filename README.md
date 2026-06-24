# HydroBiz

A single-file, zero-build business website template for water/hydroponics businesses, built to run on GitHub Pages with no server, no build step, and no dependencies. Everything — the public site and the hidden admin dashboard — lives in `index.html`.

## Deploy your site (5 minutes)

1. **Create your repo.** Click "Use this template" (or fork/copy this repo) into a new repository under your own GitHub account, e.g. `yourbusiness-site`.
2. **Enable GitHub Pages.** In your new repo: Settings → Pages → Source: "Deploy from a branch" → Branch: `main` (or `master`) → folder `/ (root)` → Save.
3. **Visit your site.** GitHub will give you a URL like `https://yourusername.github.io/yourbusiness-site/`. It may take a minute to go live after enabling Pages.
4. **Open the admin dashboard.** Press and hold (long-press, ~2.5 seconds) the menu/hamburger icon in the top-left of the nav bar. Since no password has been set yet, this opens the admin dashboard directly.
5. **Set your password immediately.** In the dashboard sidebar, go to **Password** and set one right away — until you do, anyone who knows the long-press gesture can open your dashboard. Once a password is set, the same long-press will ask for it instead of opening straight in.
6. **Fill in your business.** Go through each section in the sidebar — Business Identity, Social Links, Products, Packages, Gallery, Testimonials, About, Appearance — and replace the demo content with your own. Every change autosaves to your browser's local storage and shows a "Saved" confirmation.
7. **Exit admin** via the button in the top-right of the dashboard to return to your live public site and see your changes.

That's it — no code, no terminal, no deployment pipeline. Repeat for each of your 30 sites, one repo per business.

## What's inside the admin dashboard

- **Business Identity** — name, tagline, logo, favicon, hero text/image, hours.
- **Social Links** — links and visibility toggles for Facebook, Instagram, X, TikTok, YouTube, LinkedIn, WhatsApp.
- **Products** — unlimited products, drag-to-reorder, optional photo *or* a YouTube video link per product (shown in place of the photo).
- **Packages** — bundled plans/subscriptions with bullet features (up to 20).
- **Gallery** — photo gallery with lightbox (up to 12 images).
- **Testimonials** — customer quotes with name/photo.
- **About** — your story and team.
- **Drawer** — the short highlights shown when someone taps (not long-presses) the menu icon.
- **Appearance** — switch between 3 page layouts (Classic, Split Showcase, Bold Editorial), toggle section visibility, edit your call-to-action button, toggle the floating WhatsApp button, set a footer tagline.
- **Integrations** — optional: paste a Firebase project config to also save every contact-form submission in real time to a Firestore `orders` collection. Leave this off and submissions go out by email as usual.
- **Password** — set/change your admin password. Forgotten passwords can't be recovered; use Data → Reset to start over.
- **Data** — export your full site content as a JSON backup, import it back in, or reset to the default demo content.

## Notes on storage

All content (text, images, settings) is stored in your browser's `localStorage`, which has a small capacity (a few MB). High-resolution images and videos can fill this quickly:

- Uploaded images are capped at 6MB each.
- For unlimited high-quality media without storage limits, use a YouTube link for product videos, and/or enable the optional Firebase integration in **Integrations**.
- If storage fills up, you'll see a clear "Storage is full" message — remove an image/video or enable Firebase to continue.

`localStorage` is per-browser, per-device. If you edit your site from a different computer or browser, use **Data → Export** on the original device and **Data → Import** on the new one to bring your content along.

## Self-test

Append `?selftest=1` to your site's URL to run a built-in console self-test (no setup required) — it checks the core utilities (hashing, image encoding, save/load, etc.) and prints a pass/fail summary to the browser console.
