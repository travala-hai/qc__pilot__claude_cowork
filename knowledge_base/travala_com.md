# Travala.com — QC Autotest Knowledge Base

> Purpose: shared "common sense" reference so the autotest system knows what to expect
> on travala.com in every test case. Use this to expand terse test cases into explicit
> ACTION / CHECKPOINT steps.
>
> Captured: 2026-07-03, desktop viewport ~1217×823, logged out, USD currency.
> All timings/positions below were observed live and may drift — re-verify after site releases.

---

## 1. Global facts (apply to every test)

| Fact | Value |
|---|---|
| Canonical URL | `https://www.travala.com` (`travala.com` redirects to `www`) |
| Products (top nav) | Stays `/hotels`, Flights `/flights`, Car Rental `/cars`, Activities `/activities` |
| Homepage `/` | Same as Stays landing (hotel search hero) |
| Default currency | USD, selector in header (flag + "USD"), left of LOG IN |
| Default guests (Stays) | 2 Adults - 0 Child, 1 room |
| Default dates (Stays) | ~5–6 days in the future (observed: today+5 check-in, today+6 check-out, 1 night). May be session-remembered — do not hard-assert exact dates |
| Default dates (Flights) | today+4 depart, today+5 return, Round trip, 1 Passenger, Economy |
| Page title (home) | "Book Hotels, Flights, Tours & Car Rental with Crypto \| Travala" |
| SPA behavior | Client-rendered (Nuxt/Vue). Allow 2–4 s after navigation before asserting content |

### Always-present floating elements (can intercept clicks!)
- **Live-chat widget** (Button.io style), bottom-right corner: round chat button ~(1154, 775) plus a "Hi. Need any help?" tooltip bubble with its own X. It overlaps page content in the bottom-right — scroll targets there may be obstructed.
- **Cookie consent** (OneTrust): "Allow All" / "Reject All" banner + a "Privacy Preference Center" modal exist in the DOM. On fresh sessions the banner appears bottom of page; dismiss before interacting with footer elements.

### Popups / interrupts
- **Login/Register modal**: opens instantly (<1 s) when clicking LOG IN or REGISTER. See §4.
- **Unprompted signup popup**: NOT observed within 35 s on a cookied session. Treat as conditional — if a modal titled "LOG IN or REGISTER & SAVE up to 13%" appears at any point, close it via the X at top-right of the modal (~(1079, 191)) and continue. Tests should include a defensive "if popup appears, close it" step after first page load.
- **Login upsell banner** on search results: dark banner "You could be seeing lower prices" with LOG IN / REGISTER button — this is inline content, not a popup; do not try to close it.

---

## 2. Header (identical on all pages, sticky)

Left → right (y ≈ 31 px):
1. Travala logo (links to `/`) — x ≈ 116
2. Nav pills: **Stays** (263), **Flights** (341), **Car Rental** (437, "NEW!" badge), **Activities** (543)
3. Flag + **USD** currency selector — x ≈ 858
4. **LOG IN** button (outlined) — x ≈ 956
5. **REGISTER** button (filled blue) — x ≈ 1071
6. Hamburger menu (☰) — x ≈ 1162

Hamburger menu (logged out) opens a dropdown: "Hi there!", My Account, My Bookings, My WishList, Smart Program, Invite & Earn, My Credits, LOGIN / SIGN UP button.

---

## 3. Homepage / Stays search form

### Layout
- Hero heading: "BOOK HOTELS & SAVE UP TO 60%", subheading "Best Prices Guaranteed on 2,200,000+ Hotels Worldwide".
- Product tabs above the search bar: Stays (active/default on `/`), Flights, Car Rental (NEW!), Activities. Active tab has dark styling.
- Search bar (white rounded card, y ≈ 380–470 on load):
  - Destination input with magnifier icon — left section, x ≈ 84–380, input center ≈ (242, 426)
  - **X clear button** inside the field (≈ (415, 426)) — appears when the field has text
  - Check-in date (≈ (485–570, 426))
  - Check-out date (≈ (650–730, 426))
  - Guests/rooms selector (≈ (790–930, 426))
  - **SEARCH** button (blue, ≈ (1061, 426))
- Destination field may be pre-filled from a previous session (e.g. "Bangkok, Bangkok Province, Thailand"). Always clear it (X button or select-all+delete) before typing.
- Below hero: 4 value-prop cards (24/7 Customer Support, Multi-payment Options, Rewards & Discounts, Best Price Guarantee).
- Homepage sections (H2/H3): Worldwide Destinations (Most Visited Countries / Top Cities / Popular Hotels tabs), Top Unique Properties, FAQs, Explore the Best Hotels in the World.

### Destination autocomplete behavior (critical for search tests)
- Trigger: typing ≥2–3 chars in the destination field. Results render well within **3 s** (a 3 s wait is a safe standard).
- Dropdown anchors below the search bar, width ≈ 780 px, max **12 suggestions**, scrollable.
- Each suggestion = icon + text (query substring highlighted) + **category badge** on the right.
- **Badge taxonomy (complete, observed):** `City and vicinity`, `City`, `Neighborhood`, `Airport`, `Train station`, `Property`.
- Ordering rules (observed):
  1. Place-type results first (cities, vicinity, neighborhoods, airports, stations), then ~3 `Property` results at the bottom.
  2. Top result for a city query is usually "<City> (and vicinity)" with badge `City and vicinity` — **not** plain `City`. Example: "Hanoi (and vicinity), Vietnam | City and vicinity".
  3. Fuzzy matches are allowed mid-list (e.g. "Hanoi" also returns "Hanover, New Hampshire / Maryland / Ontario | City"). They rank below the exact-match block. Tests should assert exact matches rank above fuzzy ones — not that fuzzy ones are absent.
- Reference fixture — query `Hanoi` (12 rows, observed 2026-07-03):
  1. Hanoi (and vicinity), Vietnam — City and vicinity
  2. Hanoi, Vietnam (HAN-Noi Bai Intl.) — Airport
  3. Old Quarter, Hanoi, Vietnam — Neighborhood
  4. Hanoi, Vietnam — City
  5. Hoan Kiem, Hanoi, Vietnam — Neighborhood
  6. French Quarter, Hanoi, Vietnam — Neighborhood
  7. Hanover, New Hampshire, USA — City
  8. Hanover, Maryland, USA — City
  9. Hanover, Ontario, Canada — City
  10. Hanoi Hotel, Hanoi, Vietnam — Property
  11. hanoi, Bogotá, CO — Property
  12. Eden Hanoi, Hanoi, VN — Property
- Reference fixture — query `London`: includes `LHR-Heathrow`, `LON-All Airports`, `LGW-Gatwick` (Airport), "London Paddington Station … — Train station", plus fuzzy `London, Ontario` / `London, Kentucky`.
- Escape closes the dropdown. Clicking a suggestion fills the field with the full label (e.g. "Hanoi (and vicinity), Vietnam").

---

## 4. Login / Register modal

- Opens instantly on LOG IN or REGISTER click; centered overlay ~1030 px wide (x ≈ 90–1110, y ≈ 160–665). Page behind stays scrollable; modal stays fixed.
- Title: "LOG IN or REGISTER & SAVE up to 13%" / "with lower member prices, discounts & givebacks".
- **Close button**: X at modal top-right ≈ (1079, 191).
- Two columns:
  - Left "Connect with email:": single **Email** input (≈ (344, 460), ~489×52 px) + **CONTINUE** button (≈ (344, 530)). Email-first flow — the password step only appears after submitting an email; there is no visible password field initially. Login and Register use the same modal.
  - Right "Connect with:": stacked social buttons **FACEBOOK** (≈ y 475), **GOOGLE** (≈ y 539), **WECHAT** (≈ y 603), each ~433 px wide at x-center ≈ 858.

---

## 5. Stays search results page

- URL pattern: `/search?check_in=YYYY-MM-DD&check_out=YYYY-MM-DD&location=<id>&place_types=<type>&r1=<adults>&session_id=<...>&user_currency=USD`
  (e.g. `location=1428&place_types=multi_city_vicinity` = Hanoi and vicinity). Rooms/children append more `r*` params.
- Page title: "Search Hotels. Online Booking for Cheap Hotels Worldwide | Travala.com".
- Header gains a compact persistent search bar (destination, dates, guests, SEARCH) directly under the main header.
- Breadcrumb: `Home > Search results`.
- Results header: "<Destination> - N Properties found" (e.g. "Hanoi (and vicinity) - 1,286 Properties found") + "Best Price Guarantee" link on the right.
- **Sort chips** (horizontal, below header): `Most booked in last 6 months` (default, selected), `Recommended`, `Star rating ↓`, `Price ↑`, `Top rated by customers`, `Deals`.
- Left sidebar, top → bottom:
  - **SEE MAP VIEW** thumbnail (top-left, ≈ (159, 244))
  - **Popular Filters**: Price per night (histogram + min/max slider, e.g. US$7–US$3,784), Breakfast Included, Free Cancellation, Star Rating 4 and 5, Guest rating: Excellent/Exceptional, Member Price — each with result counts
  - **Filters**: Search Hotels (name text filter), Property Type (Hostel/Backpacker, Condo, Private vacation home, Aparthotel, Guesthouse, …), Star Rating (1–5), Guest reviews (Excellent, Very Good, Good, …), Payment type (Free Cancellation), Amenities (Swimming Pool, Free WiFi, Parking available, Bar/Lounge, Airport transfers, …), Room Boards (Breakfast Included, Room Only, Half Board, Full Board), Room Types (Honeymoon, Double, Triple, Twin, Family), Room Classes (Deluxe, Business, Luxury, Corner, Executive)
- **Hotel card anatomy** (list item):
  - Photo (left) with wishlist heart, "VIEW ALL" photo button
  - Name (link), star rating icons, address + "See map" link
  - Green tags: e.g. `Free Cancellation`, `Breakfast`
  - AVA giveback line: "Approx. US$X.XX in AVA giveback with FREE Smart membership"
  - Amenity icons + "Amenities" link
  - Right column: review label + score chip (e.g. "Wonderful 9.2", "990 Reviews"), promo badges (`20% OFF TODAY`, `ONLY 5 LEFT!`), strikethrough original price, final price "US$ NNN.NN", caption "Price per night, 1 room / Including taxes and fees", **CHOOSE** button
  - Review-label scale (approx.): Exceptional ≥9.5, Wonderful ≥9, Excellent ≥8.5, Very Good ≥8 — assert label+score pairing loosely
- **Clicking a hotel card opens the hotel detail page in a NEW TAB.** Autotests must switch tabs (or strip `target=_blank`) after clicking.
- Prices are dynamic (change between page loads and vs. detail page). Never assert exact prices; assert format `US$ <number>` and relative logic only.

---

## 6. Hotel detail page

- URL pattern: `/hotel/<slug>-<id>?check_in=...&check_out=...&location=...&r1=...&search_code=...&user_currency=USD` (e.g. `/hotel/apricot-hotel-9579363`).
- Page title: "<Hotel Name> | Travala.com".
- Breadcrumb: `Home > Hotels > <Country> > <City> > <Hotel Name>`.
- Above the fold: hotel name (H-style title), review score chip + label + review count, star icons, Share link, address + "See map", "from US$ NNN.NN / Price per night, 1 room", blue **SELECT ROOM** button (top right, ≈ (1082, 229) on load).
- Photo mosaic (1 large + 2 medium + row of thumbs) with "SEE ALL N IMAGES" overlay on the last thumb (N varies).
- Sticky sub-nav appears on scroll: `Overview | Rooms | Description | Amenities | Reviews` + "from US$…" + SELECT ROOM. SELECT ROOM scrolls to the Rooms section.
- Page sections (H2): Properties Nearby, Hotel Description, Amenities, Guest reviews.
- **Rooms section**: grouped by room type (e.g. "Deluxe Sketch, 1 King Bed with Internal window"), each group collapsible. Left: room photos carousel, "Room details and photos" link, size (sq ft / m²), bed type, "Pay with Card or Crypto". Right: one card per rate plan:
  - Plan name: `Room Only` / `Breakfast Included` + refundability (`Non-refundable` / `Free Cancellation`)
  - Occupancy ("1 Rooms | 2 Adults"), perks (Free WiFi, Free welcome drink), "Voucher Supported"
  - "See offer details" link
  - Price block: optional `20% OFF TODAY` badge, strikethrough price, final "US$ NNN.NN", "Price per night, 1 room / Including taxes and fees", AVA giveback line, **BOOK NOW** button
- Booking while logged out: BOOK NOW leads into checkout (guest/login flow) — not exercised in this KB.

---

## 7. Flights page (`/flights`)

- Hero: "BOOK FLIGHTS WORLDWIDE" / "Book Over 600 Airlines with 100+ Payment Options".
- Same product-tab strip; Flights tab active.
- Search form: radio row `Round trip` (default) / `One way` / `Multi-city`; fields `Flying from` ⇄ `Flying to` (with swap arrow), depart date (today+4), return date (today+5), `1 Passenger, Economy` selector, SEARCH button.
- Same header, value-prop cards, and footer as home.

## 8. Car Rental (`/cars`) and Activities (`/activities`)

- Reachable from top nav and product tabs (Car Rental carries a "NEW!" badge). Not yet profiled in detail — extend this KB before writing deep test cases for them.

---

## 9. Footer (identical on all pages)

Columns:
- **TRAVALA.COM**: Price Guarantee, Mobile App, Business Travel
- **SUPPORT**: Help Center, My Trip, Terms & Conditions, Privacy Policy, Cookie Policy, Contact Us, Concierge, Bug Report
- **USEFUL LINKS** (SEO sitemap): Sitemap, Accommodations, Countries, Regions, Cities, Landmarks, Airports, Hotel Chains, Hotel Themes, Flights From/To {Countries, Regions, Cities, Airports}, Flight Routes {Country to Country, City to City}, Flights by All Airlines, Flights To {Countries, Cities} by Airlines
- **RESOURCES**: Read Reviews, Careers
- **COMMUNITY**: Twitter, Facebook, Telegram, Instagram, Reddit, Linkedin, Discord
- Payment note "We accept Credit Card, Debit Card", Data Protection Representative, "© Copyright 2017 - 2026. Travala.com"

## 10. Sitemap summary

```
www.travala.com/
├── /                  Stays landing (default)
├── /hotels            Stays landing
├── /flights           Flights landing
├── /cars              Car Rental landing (NEW)
├── /activities        Activities landing
├── /search?…          Stays search results
├── /hotel/<slug>-<id> Hotel detail (opens in new tab from results)
└── footer utility pages (Help Center, Terms, Privacy, Contact, Sitemap, …)
```

---

## 11. Standard test-writing conventions derived from this KB

1. **Waits**: after navigation wait for the search bar to be visible (don't rely on fixed sleeps alone); after typing in autocomplete wait 3 s; after SEARCH allow up to 8 s for results.
2. **Defensive popup step**: after first page load, "If a login/signup modal appears, close it via its X; otherwise skip." Also dismiss the cookie banner (prefer Reject All) on fresh profiles.
3. **Clear before type**: destination field may carry prior text; click the in-field X or select-all+delete first.
4. **New-tab awareness**: hotel cards open detail pages in a new tab — add an explicit "switch to new tab" action.
5. **No exact-price/count assertions**: property counts, prices, discount badges, image counts and review counts are dynamic. Assert presence + format, not exact values.
6. **Badge assertions**: use the exact taxonomy {City and vicinity, City, Neighborhood, Airport, Train station, Property}; expect the top city suggestion to be the "(and vicinity)" entry; expect fuzzy matches below exact matches rather than absent.
7. **Chat widget**: bottom-right overlay can block clicks; scroll targets into the viewport center before clicking.
8. **Logged-out default**: all expectations above assume logged out + USD; member prices differ after login.
