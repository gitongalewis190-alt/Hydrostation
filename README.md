# HydroBiz

A single-file, zero-build business website template for water/hydroponics businesses, built to run on GitHub Pages with no server, no build step, and no dependencies. Everything — the public site and the hidden admin dashboard — lives in `index.html`.

## Deploy your site (5 minutes)

1. **Create your repo.** Click "Use this template" (or fork/copy this repo) into a new repository under your own GitHub account, e.g. `yourbusiness-site`.
2. **Enable GitHub Pages.** In your new repo: Settings → Pages → Source: "Deploy from a branch" → Branch: `main` (or `master`) → folder `/ (root)` → Save.
3. **Visit your site.** GitHub will give you a URL like `https://yourusername.github.io/yourbusiness-site/`. It may take a minute to go live after enabling Pages.
4. **Open the admin dashboard.** Press and hold the menu/hamburger icon in the top-left of the nav bar (about half a second, until it glows), then — still holding — drag it downward. Since no password has been set yet, this opens the admin dashboard directly. (A plain tap instead opens the public quick-info drawer.)
5. **Set your password immediately.** In the dashboard sidebar, go to **Password** and set one right away — until you do, anyone who knows the hold-and-pull gesture can open your dashboard. Once a password is set, the same gesture will ask for it instead of opening straight in. While you're there, also set up your two **security questions** — that's what lets you recover a forgotten password yourself later (see the Password bullet below). The dashboard also auto-locks after 5 minutes of inactivity, and destructive actions (like Reset) ask you to re-enter your password.
6. **Fill in your business.** Go through each section in the sidebar — Business Identity, Social Links, Products, Packages, Gallery, Testimonials, About, Appearance — and replace the demo content with your own. Every change autosaves to your browser's local storage and shows a "Saved" confirmation.
7. **Exit admin** via the button in the top-right of the dashboard to return to your live public site and see your changes.

That's it — no code, no terminal, no deployment pipeline. Repeat for each of your 30 sites, one repo per business.

## What's inside the admin dashboard

- **Business Identity** — name, tagline, logo, favicon, hero text/image, hours.
- **Social Links** — links and visibility toggles for Facebook, Instagram, X, TikTok, YouTube, LinkedIn, WhatsApp.
- **Products** — unlimited products, drag-to-reorder, optional photo *or* a YouTube video link per product (shown in place of the photo).
- **Packages** — bundled plans/subscriptions with bullet features (up to 20).
- **Cart & Checkout** — visitors can add products/packages to a cart (floating cart button, badge count, slide-in drawer with quantity steppers), then check out by choosing **WhatsApp**, **SMS**, **Email**, or **tapping to call** directly — no payment processing, just a fast way for customers to reach you with their order. Each option appears automatically once you've set the matching contact info (phone, WhatsApp link, and/or email). The same WhatsApp/SMS/Email choice is offered on the Contact form too.
- **Gallery** — photo gallery with lightbox (up to 12 images).
- **Testimonials** — customer quotes with name/photo.
- **About** — your story and team.
- **Drawer** — the short highlights (plus your business hours) shown when someone gives the menu icon a plain tap (not the hold-and-pull admin gesture).
- **Appearance** — switch between 3 page layouts (Classic, Split Showcase, Bold Editorial), toggle section visibility, edit your call-to-action button, toggle the floating WhatsApp button, set a footer tagline, and customize your theme colors, fonts, and icon color live.
- **QR Code** — a styleable, printable QR code (admin preview and a public modal) that always points at your site's own live URL.
- **Orders** — every enquiry and cart checkout is logged here, filterable by customer name and date range, showing items, total, and which channel (WhatsApp/SMS/Email/Call) was used.
- **Password** — set/change your admin password (with a show/hide eye toggle on every password field, on both the login screen and here). Set up two security questions here right away — if you ever forget your password, tap "Forgot password?" on the lock screen, answer them correctly, and you can set a brand-new password yourself instantly, no developer needed. Without security questions set up, a forgotten password can't be recovered; use Data → Reset to start over instead.
- **Data** — export your full site content as a JSON backup, import it back in, or reset to the default demo content.
- **Virtual Manual** — tap the book icon in the dashboard's top bar any time for a searchable, illustrated guide to every section, plus optional guided coachmark tours. From there, **Print / Save as PDF** generates a branded, printable owner's guide (your logo, business name, and a full walkthrough of every admin section) that you can hand to staff or keep for reference — it never needs an internet connection once printed.

## Notes on storage

All content (text, images, settings) is stored in your browser's `localStorage`, which has a small capacity (typically 5-10MB total, shared across everything on the site). Images are the main thing that fills it — an uploaded photo takes up roughly a third more space as stored text (base64) than its original file size.

**Why "Storage is full" happens:** too many/too-large images saved across Products, Packages, Gallery, About/Team, and your Logo all add up against that one shared limit.

**Fix it right now (what worked for you already):**
1. Go to the section with the most or biggest images first — usually **Gallery** or **Products** — and delete a couple of the largest/least-important ones.
2. Save. The error clears immediately once you're back under the limit; nothing else is lost.

**Avoid hitting it again:**
- Resize photos to about 1200px wide before uploading (a phone photo straight from the camera is often 5-10x larger than it needs to be on screen).
- Use JPG for photos (not PNG) — JPG is much smaller for the same look.
- For product videos, use a **YouTube link** (Products → video field) instead of uploading a video file — videos are by far the fastest way to fill storage, and a YouTube link uses zero local storage.
- Keep Gallery to your best 8-12 shots rather than everything you have — quality over quantity.
- Uploaded images are hard-capped at 6MB each regardless, as a safety limit.

**Backup before you trim:** if you're not sure you want to delete something permanently, run **Data → Export** first to save a full JSON backup of everything (including the images). You can delete freely knowing you can pull anything back in later via **Data → Import** if needed.

`localStorage` is also per-browser, per-device — it doesn't sync anywhere by itself. If you edit your site from a different computer or browser, use **Data → Export** on the original device and **Data → Import** on the new one to bring your content along. (If you want changes to appear instantly for visitors across every device without manual export/import, turn on **Cloud Sync** in the admin sidebar — see below.)

## Setting up a client's device (quick checklist)

Once a site is built and content is filled in, handing it to the business owner on their own phone/tablet/laptop takes a few minutes:

1. **Open the live URL** on their device (the `https://yourusername.github.io/yourbusiness-site/` link from step 3 above) in their normal browser (Chrome/Safari).
2. **Add it to their home screen** so it opens like an app: browser menu → "Add to Home Screen" (iOS Safari) or "Install app"/"Add to Home Screen" (Android Chrome). This is optional but makes daily use easier.
3. **Confirm the admin password is the one you handed off** — open the admin gesture (hold the hamburger icon ~0.5s, then drag down) and log in with the password you set for them, not any test/default password.
4. **Walk them through one real edit** (e.g. updating Business Hours or a product) so they see the autosave/"Saved" confirmation firsthand.
5. **Tell them two rules:** don't clear their browser's site data/history for this site (it deletes their content, since it's stored locally), and if they ever edit from a second device, use **Data → Export / Import** to carry content over — unless Cloud Sync is enabled (see below), in which case every device stays in sync automatically.
6. **Leave them the password and a copy of the exported JSON backup** (Data → Export) somewhere safe — if the password is ever lost it cannot be recovered, only reset (which wipes content back to defaults).

That's the whole handoff — there's nothing to install, no accounts to create, and no ongoing hosting cost.

## Scaling to more clients

Each client is a fully independent copy of this same template — there is no shared backend or shared database between them:

- **One GitHub repo per business**, each deployed to its own free GitHub Pages URL. Onboarding client #2, #3, ... #30 is the exact same 7-step "Deploy your site" process above, repeated per business, using the same `index.html` template each time.
- **No cross-client data ever mixes.** Each site's content lives in that visitor/owner's own browser `localStorage` (and, optionally, that business's *own* Cloud Sync project — see below) — never in a shared database you'd have to manage, secure, or pay to scale.
- **Zero added hosting cost per client.** GitHub Pages is free per public repo, so the 30th client costs the same to host as the 1st.
- **Each business sets its own independent password** — there's no master password and no central account system, so one business's credentials or data are never exposed to another's.
- **Optional real-time sync is opt-in and per-business.** If a client wants their changes to appear instantly for site visitors across multiple devices (rather than relying on the default localStorage-per-device behavior), they turn on **Cloud Sync** in their own admin dashboard and plug in their *own* free Firebase project keys. This is entirely optional, isolated per business, and the site works fully without it.
- **Updating the template itself** (a new feature or fix to `index.html`) doesn't automatically reach already-deployed clients — since each is its own repo/copy, you'd re-apply the updated file to each client repo you want to upgrade. This is the tradeoff for total isolation and zero shared infrastructure.

## Self-test

Append `?selftest=1` to your site's URL to run a built-in console self-test (no setup required) — it checks the core utilities (hashing, image encoding, save/load, etc.) and prints a pass/fail summary to the browser console.
