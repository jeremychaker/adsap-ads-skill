# Tracking health

*"Is my pixel working?", "Conversions look wrong", "Set up a tracking check"*

Chain:

1. `adsap_get_pixel_details`: advanced matching config, approximate match rate, last fired time. A dead pixel shows here first.
2. `adsap_get_pixel_stats`: event volume by type over time.
3. `adsap_list_custom_conversions`: what counts as a conversion.
4. `adsap_get_account_health`: deeper audit including CAPI and server-side events.

`adsap_list_pixel_events` lists tracked events when choosing a conversion event for a new ad set.

For catalog and dynamic ads, the pixel to catalog wiring check lives in `references/catalog.md`.
