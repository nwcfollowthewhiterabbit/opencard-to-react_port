# OpenCart Visual Container

Date: 2026-04-28

## Goal

Run an isolated "new new" storefront that can be visually moved toward the current OpenCart site without touching the existing React storefront.

## Deployment

Existing React storefront remains unchanged:

- Compose project: `gl-new-website`
- Path: `/opt/greenleaf/site`
- Public test URL: `http://139.99.171.55:18080`

OpenCart visual port:

- Compose project: `gl-opencart-visual`
- Path: `/opt/greenleaf/site-opencart-visual`
- Public test URL: `http://139.99.171.55:18081`
- API health: `http://139.99.171.55:18081/health`
- ERPNext network: `erp-greenleafpacific-local_default`

## First Visual Pass

Implemented in the isolated visual copy:

- OpenCart-like dark top bar using the old Green Leaf contact details.
- Larger Green Leaf logo/header area.
- Header search form.
- Cart/quote button styled closer to the old OpenCart theme.
- Green horizontal category menu based on ERP website departments.
- Old 1920x500 slider assets as the hero surface.
- OpenCart Luxury theme fonts copied from the old server theme.
- OpenCart-like color tokens: dark purple, muted green, gold, silver.
- Product cards restyled toward the old theme with square white product image areas, uppercase titles, gold category tags, and flatter buttons.
- Footer restyled to the old dark theme direction.

## Runtime Fix

The first browser smoke exposed API fallback behavior caused by `304 Not Modified` responses on proxied API calls. The visual container nginx config now disables validator caching for `/api/*` and `/health`:

- clears `If-None-Match`
- clears `If-Modified-Since`
- hides proxied `ETag`
- adds `Cache-Control: no-store`

## Verification

Passed:

- Docker build for `gl-opencart-visual-web`
- API health
- ERPNext validation
- Full smoke test with `SMOKE_BASE_URL=http://web`
- Visual smoke through SSH tunnel to `localhost:18081`

Known state:

- This is a first visual pass, not final pixel parity.
- Legacy URL alias support is not implemented yet.
- Category counts may populate after live facet data returns.
- More work is needed on category/product detail pages, checkout/quote surfaces, mobile menu parity, and exact OpenCart spacing.
