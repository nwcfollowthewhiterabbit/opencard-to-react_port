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
- OpenCart theme color tokens from the live settings: `#333024`, `#F1F5F5`, `#91BB94`, `#7A9D7C`, `#4EAE33`, `#ADA479`, `#C1C3CC`.
- Product cards restyled toward the old theme with square white product image areas, uppercase titles, gold category tags, and flatter buttons.
- Product detail page restyled toward the old theme: breadcrumb strip, contained product image, square spec table, and light-green quote panel.
- Customer account page restyled toward the old theme: square panels, smaller OpenCart-like headings, flatter login state, and table-like summary blocks.
- Zero-count category badges are hidden in the visual port so loading or unmapped ERP departments do not look like empty OpenCart categories.
- Footer restyled to the old dark theme direction.
- Old favicon copied into the visual container.

## Legacy URL Compatibility

The visual container now includes a static OpenCart SEO alias export generated from the live OpenCart database:

- categories from `oc_url_alias` + `oc_category_description`
- products from `oc_url_alias` + `oc_product` / `oc_product_description`
- information pages from `oc_url_alias` + `oc_information_description`

Runtime behavior:

- product aliases resolve client-side to `/products/<sku>` when the OpenCart SKU/model can be mapped
- category aliases resolve to an exact ERP item group when possible
- category aliases without an exact ERP group fall back to `/catalog` instead of rendering an empty page
- information aliases fall back to the service/catalog surface

The alias bundle is lazy-loaded only for unknown legacy paths, so normal `/catalog`, `/products/*`, and `/account` visits do not load it.

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
- Visual smoke through SSH tunnel to `localhost:18081` after the latest product/account styling pass
- `/favicon.ico`
- legacy path shell checks, including `/bar-supplies` and `/reception-storage-unit-custom-design`
- existing React storefront on `http://139.99.171.55:18080` remained running separately

Known state:

- This is a first visual pass, not final pixel parity.
- Legacy URL alias support is initial and static; it should be regenerated after OpenCart catalog changes until the old site is retired.
- More work is needed on category/product detail pages, checkout/quote surfaces, mobile menu parity, and exact OpenCart spacing.
