# Catalog and commerce (only for accounts selling products)

*"Are my products syncing?", "Why do my catalog ads underdeliver?", "Is product X in the feed?"*

Chain:

1. `adsap_list_catalogs`.
2. `adsap_get_catalog_details`: name, vertical, product count. Confirm it is the right catalog.
3. `adsap_get_catalog_diagnostics`: Meta's own issue list. Start here for "something's wrong".
4. `adsap_get_catalog_data_sources`: feed status, last upload.
5. Spot-check items via `adsap_search_catalog_products`, `adsap_get_product_set_products` or `adsap_get_product_details`.

`adsap_get_pixel_health` checks whether the pixel is wired correctly for dynamic and catalog ads (event to catalog matching, missing parameters). Reach for it when catalog ads underdeliver and the catalog itself looks healthy.
