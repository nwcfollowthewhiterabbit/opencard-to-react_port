# Server Audit: Green Leaf OpenCart to React Port

Date: 2026-04-28

## Summary

The current production storefront at `greenleafpacific.com` is a native OpenCart installation. The new storefront and ERPNext stack are already running in Docker on the same server, but the new React storefront is visually separate from the OpenCart theme. A 1:1 visual port is feasible by rebuilding the React UI around the OpenCart Luxury theme assets, layout, and URL compatibility.

## Server Layout

Current OpenCart site:

- Path: `/home/yourwww/sites/greenleafpacific.com`
- Platform: OpenCart `2.1.0.2.1`
- PHP: `5.6.40`
- Theme: `catalog/view/theme/luxury`
- Database: `yourwww_default`
- Table prefix: `oc_`
- Public domain: `https://greenleafpacific.com`

New storefront:

- Path: `/opt/greenleaf/site`
- Compose file: `/opt/greenleaf/site/docker-compose.yml`
- Web container: `gl-new-website-web-1`
- API container: `gl-new-website-api-1`
- Public test port: `139.99.171.55:18080`
- Stack: React, Vite, Express, MySQL client

ERPNext:

- Path: `/opt/greenleaf/erp`
- Compose file: `/opt/greenleaf/erp/docker-compose.yml`
- Frontend public test port: `139.99.171.55:18092`
- Services: MariaDB, Redis, backend, frontend, workers, scheduler, websocket

## OpenCart Inventory

Production OpenCart counts:

- Products: `16060`
- Enabled products: `11139`
- Categories: `528`
- Manufacturers: `35`
- Customers: `172`
- SEO aliases: `16080`
- `image/catalog`: about `404M`
- `catalog/view/theme/luxury`: about `22M`

Theme and modules observed:

- Active template: `luxury`
- Theme vendor family: Octemplates Luxury
- Important modules: `oct_luxury`, `oct_megamenu`, `mega_filter`, `simplecheckout`, `oct_popup_*`, `oct_slideshow_plus`, `oct_product_tab`
- Fonts: Open Sans, Proxima Nova
- Main observed palette: `#251c28`, `#ADA479`, `#779979`, `#C1C3CC`

Top-level OpenCart categories:

- Foodservice
- Cleaning equipment
- Housekeeping and Room Service
- Furniture & Outdoor
- Towels & Bedding
- Eco-friendly
- Iced Tea
- Other Categories

## New Storefront State

The new storefront is live and connected to ERPNext.

Validation results:

- API health: OK
- ERPNext REST credentials: configured
- ERPNext database: reachable
- ERPNext custom fields: present
- Smoke test: passed when run with `SMOKE_BASE_URL=http://web` inside Docker
- Visual smoke: passed through local tunnel to `localhost:18080`

Observed live catalog:

- API total products: `22913`
- Price list: `Standard Selling`
- Storefront departments source: ERPNext Website Department
- Banners source: ERPNext Website Banner
- Catalog downloads source: ERPNext Website Catalog
- Manufacturers source: ERPNext Website Manufacturer

## Main Gap

The backend and ERPNext connection are in workable shape. The main remaining gap for a 1:1 migration is frontend parity:

- OpenCart uses a Bootstrap/jQuery Luxury theme with legacy Octemplates components.
- The new site uses a modern React UI with different typography, header, catalog cards, filters, product pages, and account surfaces.
- Current URL structures differ:
  - OpenCart examples: `/bar-supplies`, `/reception-storage-unit-custom-design`, `/about_us`
  - React examples: `/catalog/...`, `/products/<sku>`, `/account`

## Migration Plan

1. Capture OpenCart visual baselines for homepage, category page, product page, search, cart, checkout, account/login, and information pages.
2. Copy or map reusable OpenCart theme assets into the React project: fonts, logo, favicon, icons, theme images, product/category images.
3. Rebuild React components to match OpenCart Luxury layout:
   - topbar/header/search
   - mega menu
   - homepage modules
   - category grid and filters
   - product cards
   - product detail page
   - quote/cart flow
   - footer
   - mobile menu
4. Add legacy URL resolution from OpenCart `oc_url_alias` so old category, product, and information URLs continue to work.
5. Compare screenshots old vs new at desktop and mobile breakpoints until visual differences are acceptable.
6. Cut over nginx only after URL compatibility, visual parity, and quote/account flows are verified.

## Notes

- `/opt/greenleaf/site` and `/opt/greenleaf/erp` are not git checkouts. They are deployed working directories.
- The server copy of the new storefront differs slightly from the public `GL-website` repository in `docker-compose.yml` and `src/main.css`.
- No secrets are stored in this document.
