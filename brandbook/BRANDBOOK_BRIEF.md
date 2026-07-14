# BrandBook — Project Brief for Claude Code

## What this is
A web/mobile app for an architecture studio: an interior styling brand book (furniture/material
brands + their products, browsable by brand, catalog type, and color) that will eventually sit
alongside a sourcing map (Amap-linked, showing where each brand was seen in person during
studio trips to Hangzhou/Shanghai/Foshan) and other studio tools (client comms, self-study).
BrandBook is the first module.

## Current state
- Single-file HTML/CSS/JS prototype: `brand-book-v2.html` (attached in this folder).
- No build tooling, no backend — everything is a hardcoded JS array in the file.
- 164 brands total: 49 have real catalogued items (pulled from the studio's own MJ inventory
  sheet — real names/colors/SKUs), 115 are "Directory" entries (logo/name/origin/official site
  link only, no items yet, sourced from two brand-wall photos the user shared).
- 2 items have been verified against live official product pages (Fritz Hansen Egg Chair,
  B&B Italia Tufty-Time) with real links — proof of concept for the "verified" research process.
- All images are placeholder (picsum.photos) — see Copyright section below, this is intentional.

## Design direction (already decided, follow this — don't relitigate)
- Visual style: pure black & white / monochrome UI chrome, edge-to-edge square tile grid with
  1px hairlines (background-as-gap technique), no rounded corners, no color accents. Inspired by
  gufram.it (bold, editorial, one hero item represents a brand) crossed with goat.com (dense,
  image-forward product grid). Small color-swatch dots are the one exception — they're
  functional data (colorways / color filter), not decoration.
- Signature interaction: hovering any tile strobes/inverts every other tile from the same brand
  (data-brand attribute + CSS `filter: invert()` animation) — ties the whole grid together and
  doubles as a "what else does this brand make" glance.
- Typography: Space Grotesk throughout, bold uppercase for names/labels.
- Three views: Brands (landing grid, 1 hero image per brand), By Catalog, By Color — plus a
  brand-detail drill-in with its own category/color filter chips.
- Title: "BrandBook" (was "Source" in an earlier draft — renamed per user request).

## Copyright — read this before touching images
This came up repeatedly and the boundary is firm, not a style preference:
- Cannot download, screenshot, or rehost brand product photography or social media images into
  the app, even for personal/internal/non-commercial use, even with visible watermarks or
  attribution. This applies regardless of who's asking or how the request is framed.
- Checked several brands' asset libraries (Vitra, GUBI) — both are registration-gated and
  explicitly remain brand property, restricted to press/trade partners, not general reuse.
- **Agreed path forward (in order of value):**
  1. User's own photography from showroom visits — best option, zero rights issues, most useful.
  2. Studio registers directly with brands' trade/professional portals (e.g. GUBI's "Professional"
     program requires being a designer/architect/interior professional with a VAT number — the
     user's studio qualifies). Real licensed assets, properly authorized. Claude can't do this
     registration on the user's behalf, but can compile which brands offer it + direct links.
  3. Link-out only, no embedded image (current default) — tap a tile, jump to the real official
     product page.
- User has started manually downloading photos themselves (their own action, their machine) and
  will upload them into this chat to be wired into the app — that's fine, it's the user curating
  their own reference tool, not Claude scraping at scale. Expect batches of images tagged by
  brand/item to arrive; match them into the data structure and swap out the placeholder `imgUrl()`
  calls for the real file paths.
- Every item modal already discloses "Photo: placeholder — copyrighted by [Brand], not
  reproduced here" — keep this pattern (or an equivalent) once real photos are wired in for any
  that are still placeholder, so the two states stay visually distinguishable.

## Data model (in the HTML file's `<script>`)
- `PALETTE` — fixed set of ~16 named colors → hex, used for color filtering/swatches.
- `CATALOGS` — fixed list of category names (Sofa, Lounge Chair, Bed, Dining Table, Side Table,
  Lighting, Storage, Rug, Mirror, Decor, Tableware, Cart).
- `BRANDS` — array of `{id, name, origin, site, items:[[name, category, [colors], link?], ...]}`.
  `link` is optional — a verified real product-page URL; falls back to brand root `site`.
- `DIRECTORY` — array of `[name, origin, site, verify?]` tuples for logo-only brands, mapped into
  the same shape as BRANDS with `items: []` and `directory: true`.
- Brands flagged `verify: true` have an origin/domain Claude wasn't fully certain of — a handful
  even fall back to a Google search URL instead of a guessed domain. These need real
  confirmation, not silent removal.
- 4 brands from the inventory sheet (ConcreteCat, DaDa, KAR, Ninefact) have real item data but
  unconfirmed origin (couldn't tell if Chinese or international) — currently Directory-only with
  origin "Unconfirmed". 2 others (BIAS, Finelumen) were judged likely Chinese-origin and excluded
  from this non-Chinese book entirely — flag to the user if that call seems wrong.

## Open threads / next steps (in the order the user cared about them)
1. **Photo intake** — build the workflow to receive user-uploaded photos (folder-per-brand,
   filename ~ product name) and wire them into item cards, replacing placeholders. This was about
   to start when the handoff happened.
2. **Scale up brand research** — verify more brands' real flagship products against live sites in
   batches of ~15–20 (like Fritz Hansen/B&B Italia), each with: confirmed origin, real product
   name, real product-page link, and — new ask — a note on whether that brand has a trade/
   professional asset portal + its registration link.
3. **Amap sourcing map** — not started yet. Should show pins per store/showroom (Hangzhou/
   Shanghai/Foshan), linked to which brands/items were seen there. Needs a decision: pin per
   store (with a list of stocked brands) vs. tag each item with the store(s) it was seen in.
   Note: Amap requires an ICP filing for mainland-hosted domains; deep-linking out to the Amap
   app/web via coordinates is simpler for v1 than embedding the full JS SDK.
4. **Real app / deployment** — currently just a static HTML file. Discussed with the user:
   - Quick path: host as-is on Netlify/Vercel/GitHub Pages for a real URL today.
   - Better path: move brand/item data out of hardcoded JS into an editable store (Airtable/
     Supabase/Google Sheet) so the studio can add entries without touching code — this is also
     what enables the phone quick-add flow (photo + brand + category + color while out sourcing).
   - Long-term: BrandBook, the Amap sourcing map, and the studio's client-comms/self-study tools
     should live as modules/tabs of one app under one login.
5. Brand landing grid should show exactly 1 representative image per brand (already implemented
   — first item in that brand's array is the hero image).

## User context
Architect running their own studio, actively sourcing furniture/materials on trips to Hangzhou,
Shanghai, and Foshan (mix of Chinese and international brands). Wants BrandBook usable on both
phone and web. Non-technical framing throughout — explain infrastructure choices in plain terms,
don't assume they want to hand-edit code themselves day to day.
