# Kesari — connected events and booking application

Run `npm install`, then `npm run dev`. Open http://localhost:3000/#/. Node 24+ is required. `npm run build` builds the React frontend; `npm start` serves the build with the same API and SQLite database.

The server also listens on the local network by default. Startup prints the current private IPv4 URL and regenerates `public/network-qr.png`, which opens that address on another device. Connect the phone to the same Wi-Fi as this computer and scan that QR. The computer and server must remain running. Set `HOST=localhost` in `.env` to restrict access to this computer, or `HOST=0.0.0.0` for LAN access. Windows already has Node.js allow rules on this machine; no firewall settings were changed.

## Landing and catalogue

The landing fills the browser viewport with one current event photo. Glass at the top and bottom blurs that same photograph, with no extra decorative image over it. The supplied Kesari logo and a working navigation toggle replace the Event Jam heading. The top menu supports outside dismissal and Escape, and links to Events, Discover, My Profile, My Tickets and Team space.

The lower glass cards are navigation menus: Events, Discover, My Profile and My Tickets. Scroll or swipe this menu independently, then select a card to open that page. Keyboard arrows within the menu move through its options.

The pre-saffron UI has been restored from its edit history: royal blue, translucent blue glass, lime actions, white menu-icon circles and the original menu and event animations. Customer pages share the original venue/photo backdrop. On mobile the app fills the viewport without a decorative phone border or notch; desktop retains the recovered presentation. The later saffron and liquid-art theme is no longer imported. Restoration notes are in `DESIGN.md`.

Events, discovery, booking, profile and tickets use the restored content scrolling layout while the header and bottom dock stay visible. A booking button jumps to the form; UPI steps scroll into view and reserve space for QR generation. Confirmed passes use the original white ticket design. The team space uses the same palette with a wider management layout. Booking and new-event identifiers work over LAN HTTP without requiring the secure-context-only `crypto.randomUUID` API.

The photo carousel uses published upcoming events, ordered by featured priority and then nearest date. Drafts, past events and events with landing inclusion disabled are excluded. Bottom arrows, dots, keyboard arrows on the screen and photo swipes change the event photo and caption without changing the menus. Selecting the photo opens the displayed event's booking page. Each slide uses its custom landing poster or its event photo. No events produces an empty state over the venue photo; one event disables event arrows. The menus work in both cases. Event list, discovery and booking views read the same catalogue as the landing.

## Management

Open http://localhost:3000/#/admin. The existing local admin password was retained in `.local/admin-password.txt`. You can instead set `ADMIN_PASSWORD` in `.env`. Keep this password private.

Admin manages event titles, dates in India time, descriptions, per-event prices, capacity, public visibility, booking availability, booking provider, terms, event/landing images, featured priority and landing inclusion. A quick Feature button pins/unpins an event. The dashboard shows the calculated landing order. Settings manages the merchant UPI ID, verified payee name and contact details. Bookings shows guest requests and supports manual confirmation/rejection after checking the bank transaction. Admin content changes propagate across open tabs and public views also refresh on focus and periodically.

## Booking and tickets

For UPI events, the server validates event availability, calculates price × quantity, stores a booking request and generates a private retrieval key. QR and UPI intent use the same stored amount/payee/reference. A customer submits their transaction reference; the request remains pending until an admin verifies the bank transaction and confirms it. Opening or returning from a UPI app never confirms payment. Duplicate transaction references and duplicate submissions of the same booking form are rejected or deduplicated. Confirmations enforce capacity.

My Tickets retrieves requests using the private keys stored in this browser. Customers can resume an unpaid booking, refresh verification status and print a confirmed QR ticket. Clearing browser data requires staff help to retrieve a booking. No automatic bank webhooks, refunds, email delivery, OTP or entry redemption scanner are included. The QR identifies a confirmed booking; entry checking remains a staff process.

External-booking events use their configured HTTPS provider link. Closed, unpublished, past or sold-out events cannot create new UPI orders.

## Saved data

SQLite lives in `server/data/kesari.sqlite`. On first run, the existing database from `../16-10-26/kesari-react/server/data/kesari.sqlite` was migrated using SQLite's backup API, preserving records. The old project is unchanged. This application now uses its own migrated database; do not keep editing the old project as a second admin system. Database files, credentials and local QA artifacts are ignored by Git.

LAN preview is available while this computer and server are running. Public hosting, domain DNS, HTTPS, durable database backups and merchant/payment-policy approval remain deployment work.

## Verification

- `npm test`: ordering/exclusion, management APIs, price validation, authentication, duplicate requests/references, manual approval and capacity.
- `node scripts/verify-wiring.mjs`: full browser flow against an isolated in-memory test catalogue/merchant, including admin feature/price/artwork changes, landing-to-booking navigation, UPI QR generation, payment submission, resume, confirmation, persisted tickets and draft/empty/single-event states. It does not transact money or create live records.
- `node scripts/verify-landing.mjs`: read-only checks and screenshots against the actual local site at 306, 390 and 1440 px. Screenshots are saved to `.local/qa/`.
The native-layout-only assertions in `scripts/verify-studio.mjs` describe the superseded theme. Use `verify-landing.mjs` and `verify-wiring.mjs` for the restored UI.

Set `KESARI_PREVIEW_ORIGIN` to the printed LAN origin to run `verify-studio.mjs` against the network address. Set `TEST_NETWORK_HOST` to this computer's LAN IPv4 address to run the complete in-memory booking/admin flow over plain LAN HTTP, including browsers without `crypto.randomUUID`.

The copied design boards remain at `#/showcase`; their old ticket/payment examples are presentation samples, separate from the connected customer booking flow.
