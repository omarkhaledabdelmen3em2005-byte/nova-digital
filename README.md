# Plinth — Digital Products Store (Frontend Prototype)

A full, clickable front-end for a premium digital-products marketplace. Pure HTML/CSS/vanilla JS —
no build step. Open `index.html` in a browser or run a local server (recommended, see below) and
click through the whole flow: browse → product → cart → checkout → order success → account.

## Design concept
Digital products are presented like gallery exhibits: every product carries a catalog "Object No.",
sits on a soft "plinth" shadow, and gets a warm spotlight glow on hover. Palette: cool gallery-white
(`--wall`), charcoal ink (`--ink`), spotlight amber (`--spotlight`), verdigris/patina teal
(`--verdigris`). Type: Fraunces (display) + Inter (body) + IBM Plex Mono (prices, specs, catalog
numbers) — see `css/style.css` `:root` for every token.

## Run it
Just double-clicking `index.html` mostly works, but browsers restrict some things over `file://`.
Best to serve it locally:

```bash
cd plinth
python3 -m http.server 8000
# then open http://localhost:8000
```

or with Node: `npx serve .`

## File map
```
index.html        Homepage
store.html         Catalog: search, category/price/rating filters, sort
product.html        Product detail — ?slug=<slug> (see js/data.js for slugs)
cart.html            Cart: qty, remove, save-for-later, coupon (try code PLINTH10)
checkout.html        Checkout form -> creates an order in localStorage
success.html         Order confirmation + "downloads"
account.html          Customer dashboard: overview/purchases/downloads/wishlist/orders/profile/settings/support
wishlist.html          Saved products
bundles.html            Bundle packages with value comparison
deals.html               Flash sale page with live countdown
about.html, faq.html, contact.html, blog.html, terms.html, privacy.html
admin.html                Simplified store-owner dashboard (stats, products, orders, etc.)

css/style.css       All design tokens + component styles (single file, well-commented)
js/data.js            Mock product/bundle/review/blog data — this is your "database"
js/main.js             Shared logic: header/footer render, cart & wishlist (localStorage),
                        toasts, product card renderer, AI Product Finder modal, countdown helper
```

## How data flows
There's no backend. `js/data.js` is the single source of truth for products, bundles, categories,
reviews and blog posts — edit it and every page picks it up automatically. Cart, wishlist, saved
items, orders and profile are stored in the browser's `localStorage` via the small `Store` helper
in `js/main.js`. Swap `Store`/`Cart`/`Wishlist` for real API calls when you connect a backend.

## Known simplifications (read before wiring up real payments)
- **Payments are not real.** Checkout just validates the form and writes a fake order to
  localStorage. Wire up Stripe/Paddle/etc. in the `complete-btn` click handler in `checkout.html`.
- **Downloads are simulated.** "Download" buttons show a toast, not an actual file.
- **Bundles add their individual products to the cart at full price**, not the discounted bundle
  price — the bundle price is only shown on the Bundles page itself. If you need bundles to
  charge correctly through checkout, give bundle line-items a distinct id in `Cart` and special-case
  the price lookup in `Cart.subtotal()`.
- **Admin dashboard reads only orders placed in your own browser** (localStorage), so it starts
  empty until you complete a demo checkout.
- **AI Product Finder** picks from mock products based on your answers — it's a rules-based quiz,
  not a real model call. Swap the logic in `pickRecs()` inside `js/main.js` for a real API call if
  you want actual AI recommendations.
- Statistics like "10,000+ creators" on the About page are placeholders — replace with real numbers.

## Extending it
- **Add a product**: add an object to `PRODUCTS` in `js/data.js` (copy an existing one — every
  field is used somewhere on the product page).
- **Add a category**: add to `CATEGORIES` in `js/data.js`, it shows up in store filters and the
  nav automatically.
- **Change the palette/fonts**: edit the `:root` block at the top of `css/style.css`.
- **Add a real backend**: replace the `Store`/`Cart`/`Wishlist`/order-writing code with `fetch()`
  calls to your API; the rendering functions (`productCard`, `renderHeader`, etc.) don't care where
  the data comes from.
