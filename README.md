# Action Packed — live calendar edition

Custom month calendar with selected-day event details, Eastern time, recurring events, all-day dates, cancellations, loading/error/empty states, and five-minute refresh. Brand colors: #d14533 and #ffff64.

## Google Calendar

The example calendar from the supplied link is configured: connorlafferty0@gmail.com. Its public iCalendar feed was verified to return valid event data. No Google API key is needed. The server fetches only the public feed for the configured calendar; it never uses a secret/private calendar URL.

To switch to the store:
1. Create a dedicated customer-facing calendar owned by the store.
2. In Google Calendar settings, make it public with event details visible.
3. Share it with staff's own Google accounts and grant event-editing permission.
4. Copy its Calendar ID from Settings → Integrate calendar.
5. Replace GOOGLE_CALENDAR_ID in wrangler.jsonc and deploy, or change that environment variable in the Worker's Cloudflare settings and deploy the settings change. Keep the local configuration aligned with dashboard changes.

Staff can then create, edit or cancel events in Google Calendar without touching the website. Events are fetched on demand; the Worker caches each month's response for five minutes and open pages refresh every five minutes. Changes can take roughly 5–10 minutes plus any Google feed delay. Public events are appropriate; keep internal notes on a separate private calendar.

## Deploy this source to the existing Cloudflare Worker

This release includes server functionality. Uploading only dist/client through static-file drag-and-drop will NOT connect the calendar.

Use Node 22.13 or newer. From this project directory:

```
npm ci
npm run build
npx wrangler login
npx wrangler deploy --config wrangler.jsonc
```

Check that you are logged into the Cloudflare account owning the existing `actionpacked` Worker. The config targets that exact name. Deploying replaces its site/code and retains the Worker identity. Verify the domain settings after deployment.

Alternatively, the companion prebuilt deployment ZIP includes a bundled worker.js, public assets, and wrangler.jsonc. Unzip it, run `npx wrangler login`, then `npx wrangler deploy --config wrangler.jsonc` from that folder. No website build is needed for the prebuilt package. The ZIP is not for the static-only dashboard upload.

## Develop

```
npm run dev
```

The local preview uses the same parser and public example calendar. The Vite configuration includes a local /api/events endpoint. Production uses calendar/worker.mjs, independently from the site's static export. wrangler.preview.jsonc belongs only to the framework preview/build; deploy with wrangler.jsonc.

- app/event-calendar.tsx — interactive month and event-details view
- calendar/feed.mjs — public-feed fetching and iCalendar parsing
- calendar/worker.mjs — production endpoint, caching and static assets
- app/globals.css — palette and comic-book styling
- app/page.tsx — other site content and sample inventory

Run `node --test tests/calendar.test.mjs` for recurrence, exclusions, rescheduling, cancellation, all-day, daylight-saving and feed-error checks.

Inventory remains sample content. Google Fonts are loaded remotely with system fallbacks. This update does not publish itself; deploy using the steps above.
