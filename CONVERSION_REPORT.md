# FUNCTNL — Static Design to Shopify OS 2.0 Conversion Report

Source of truth: `index.html` (fully read: markup, CSS, JS, media queries, SVGs).
Deliverables: 13 self-contained sections in `sections/`, `templates/index.json`, `sections/header-group.json`, `sections/footer-group.json`, this report.

## Section list

| # | File | Purpose | Dynamic data | Interactions |
|---|------|---------|--------------|--------------|
| 1 | `hsn-announcement.liquid` | Rotating announcement bar | message blocks with optional links | JS rotator, block select in editor, reduced-motion shows all |
| 2 | `hsn-header.liquid` | Logo, nav, search, cart, mobile drawer, skip link | logo image, link_list, cart count | drawer (Esc/overlay close), search details, editor-safe |
| 3 | `hsn-hero.liquid` | Two-slide fade hero with dots | slide blocks: image, copy, CTA | autoplay + dots + swipe, block select, LCP eager/high |
| 4 | `hsn-marquee.liquid` | Infinite word band | word blocks | CSS transform marquee, pause on hover, reduced-motion static |
| 5 | `hsn-featured-collection.liquid` | Product grid + Shop All | collection picker, `collection.products` | quick add (`{% form 'product' %}`), hover image, badges |
| 6 | `hsn-stats.liquid` | Count-up figures band | stat blocks | IntersectionObserver count-up |
| 7 | `hsn-video-reviews.liquid` | Rating + video tile slider | review image blocks + links | scroll-snap slider, arrows, autoplay, center-scale |
| 8 | `hsn-bundle.liquid` | Promo split (image left) | image, inline_richtext, CTA | — |
| 9 | `hsn-featured-product.liquid` | Product spotlight + chips | product picker, stat/chip blocks | native add-to-cart form |
| 10 | `hsn-story.liquid` | Founder story split | image, inline_richtext, CTA | — |
| 11 | `hsn-journal.liquid` | Article cards | blog picker, `blog.articles` | scroll-snap + arrows |
| 12 | `hsn-social-grid.liquid` | Community image wall | image blocks, handle link | — |
| 13 | `hsn-footer.liquid` | Link columns, newsletter, socials, wordmark, legal | link_column blocks, `{% form 'customer' %}`, images | accessible newsletter with success/error states |

## Assumptions

1. **Fonts**: PP Monument Extended is a licensed custom font, not in Shopify's library. It is loaded from the same hosted WOFF files the static design uses (`@font-face`, `font-display: swap`) with the Archivo/system fallback stack. Outfit loads from Google Fonts with `display=swap` (toggle in the header section if your theme already loads it).
2. **Contrast**: brand blue `#7EB8D4` on white fails WCAG AA for text (≈2.1:1). Small blue text uses `#3E7EA0` ("accent text" settings, ≈4.6:1). Decorative blue fills keep the exact brand blue with black text (≈10:1). Set the accent settings to `#7EB8D4` if you prefer pixel-exact over contrast.
3. **Star ratings** render only from the product reviews rating metafield (`product.metafields.reviews.rating.value`) — never fake stars.
4. **Announcement rotator without JS** shows the first message; `prefers-reduced-motion` shows all messages stacked.
5. **Quick add** appears only for single-variant products (native form, no-JS safe); multi-variant products get a "Choose options" link to the product page.
6. **Journal arrows** always render (they double as scroll controls on mobile where the grid becomes horizontal snap-scroll).
7. **Hero dots** are keyboard-accessible buttons (44px effective target); arrows were intentionally absent from the final design.

## Admin setup required

- **Menus**: `main-menu` (header + drawer) and `footer` (footer columns + legal bar). Create or reassign via the section settings.
- **Collections**: pick the collection for Best Sellers (e.g. a "best sellers" automated collection). Optional product tag `best seller` drives the black badge.
- **Metafields** (optional): `reviews.rating` (type `rating`) on products enables star display.
- **Images**: hero slide backgrounds (~2200px), bundle/story/social/wordmark images; wordmark is the large footer logo.
- **Home page**: assign `templates/index.json` (already the default for index if uploaded with the template).
- **Groups**: add `sections/header-group.json` and `sections/footer-group.json` to the theme; they mount the announcement/header and footer.

## Design notes / closest solutions

- The static page's JS-driven transform video slider was rebuilt as native **scroll-snap + arrows + autoplay** (per performance rules), keeping the center-tile scale effect via IntersectionObserver.
- Product-card hover image swap uses `product.images[1]` when present.
- Cart drawer/predictive search are native Shopify flows and intentionally not reimplemented; header cart links to `routes.cart_url`.
- PP Monument Extended has no `font_picker` equivalent (not in Shopify's library), so it is served as a hosted `@font-face` — this is the closest faithful solution.

## Self-audit

For every section file: Liquid tags balanced and validated; `{% schema %}` parses as strict JSON with presets, grouped settings and in-range defaults; CSS fully scoped under `.section-{{ section.id }}` with `hsn-*` BEM classes, zero global selectors, zero `!important`; JS is a scoped IIFE guarded by `data-hsn-init`, re-runs on section re-render, uses event delegation and `prefers-reduced-motion` guards; native flows untouched; images use `image_url`/`image_tag` with `widths`/`sizes`, explicit sizing via aspect-ratio, lazy below the fold and eager/high priority on the hero LCP; focus-visible styles in every interactive section; placeholders (`placeholder_svg_tag`) render when collections/blogs/images are unset.
