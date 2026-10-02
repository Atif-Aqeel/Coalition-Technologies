# Coalition Technologies Shopify Theme Assessment

This project uses the supplied Dawn 15.3.0 ZIP and the supplied `products_export (ctrecruiting).csv`. The ZIP was extracted into this theme directory and the extracted file set was checked against the archive. This assessment README replaces the upstream Dawn README. The standard `collection.json` template and Dawn's default collection section are unchanged.

## Import the supplied products

1. In the Shopify development store, open **Products > Import**.
2. Upload `products_export (ctrecruiting).csv` and complete the import. The CSV is stored beside the theme directory in the assessment project.
3. Confirm that product `womens-long-cardigan` and its variants and images imported.

The CSV contains one active product and 16 variants: four sizes (XS, S, M, L) across White, Green, Blue, and Red. The first row declares the options `Size` and `Color`; Color is option 2. All 16 variants list a variant image and a price of $205.40. No compare-at prices are supplied. All 16 rows have inventory quantity 1 with inventory policy `deny`, so this export does not provide a sold-out variant case. Treat imported inventory status as the store reports it after import; inventory behavior can depend on the store's inventory settings.

## Create a collection and assign the template

1. Keep the existing collection on the standard template for comparison.
2. Create a second manual collection containing `womens-long-cardigan`.
3. Upload this theme as a development or unpublished theme; do not publish it.
4. In **Online Store > Themes**, open **Customize** for this theme and navigate to the second collection.
5. In the collection's template selector, choose **with-variants** (the template file is `templates/collection.with-variants.liquid`). Save.
6. Open both collection storefront URLs to compare the standard and custom views.

The custom template renders Dawn's collection banner followed by the dedicated `main-collection-product-grid-with-variants` section. Dawn's normal `collection.json` and `main-collection-product-grid.liquid` remain unchanged.

## How color cards are chosen

- The section scans the product's option names and matches `Color` after lowercasing, so `Color`, `color`, and `COLOR` are recognized. Products without that option are passed to Dawn's normal `card-product` snippet once.
- For products with a Color option, it emits one card per distinct color value. Size variants are not separate cards. Color values are compared without case, while the displayed label preserves the imported value.
- When several size variants exist for a color, the card represents the first available variant in Shopify's variant order. If no variant for that color is available, it represents the first variant for that color and shows Dawn's sold-out badge. This makes a color card available if at least one of its sizes is available.
- The card uses the representative variant's image, falling back to the product featured image, and links to `product.url?variant=<variant-id>`.
- The displayed price is the representative variant's price. If its compare-at price is greater than its price, the card shows Dawn's sale styling and both prices. Pricing can differ by size; the card deliberately shows the chosen representative size's price rather than a price range.

## Validation and test cases

| Case | Supplied data or check | Result |
| --- | --- | --- |
| Multiple colors and sizes | 16 variants: 4 colors × 4 sizes | Rendering logic emits one grid card per color, not one per size; expected four cards. |
| Variant images | All 16 CSV rows have a `Variant Image` value | Variant image is used when present. |
| Missing variant image | Not present in the CSV | The Liquid card falls back to the product featured image; needs storefront verification. |
| Sale price | No compare-at prices in the CSV | Compare-at markup and Dawn sale badge are conditional; needs a sale variant in the store to verify visually. |
| Sold-out color | No confirmed sold-out variant case in the CSV | If all sizes for a color are unavailable, the first size is shown with Dawn's sold-out state; needs inventory setup in the store to verify. |
| No Color option | No such product in the CSV | The custom section delegates it to Dawn's normal product card once; needs a second product to verify on a storefront. |
| Standard collection | Reviewed default `collection.json` and section | Left unchanged. |
| Shopify Theme validation | The custom template, grid section, and variant card snippet pass Shopify theme validation. |
| Local Shopify Theme Check | The supplied Dawn base has existing errors and warnings in untouched files. Resolve only findings introduced by the custom template, grid section, or card snippet. |

The CSV product import and first comparison collection were completed in Shopify Admin. Uploading the custom theme, assigning `with-variants`, storefront comparison, temporary variant-state checks, and desktop/mobile checks remain to be completed.

## Known Liquid and theme behavior limits

- Shopify Liquid limits a `for` loop to 50 iterations. Products with more than 50 variants can therefore have colors beyond the loop window omitted; the supplied product has 16 variants.
- Dawn paginates collection products before this section expands each product into color cards. A page can show more cards than the configured product page size when products have multiple colors.
- Collection filters and sorting still operate on Shopify collection products. The cards expand those products by color after the product list is selected; filters do not independently paginate color cards.
- Availability is based on Shopify's `variant.available` value, which reflects store inventory policy and settings.


