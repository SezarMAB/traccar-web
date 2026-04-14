# Traccar Web Common & Localization

This package (`src/common` & `src/resources`) houses multi-lingual support mechanisms and shared abstractions.

## LOGISTICS.SY Context

- **Arabic Target**: `resources/l10n/ar.json` serves as the primary dictionary for domain-specific definitions (like "Speed", "Event", "Offline", "Geofence") when constructing the LOGISTICS.SY Arabic interface.
- **RTL Direction Handling**: Uses `stylis-plugin-rtl` via Emotion Cache. When establishing RTL patterns in LOGISTICS.SY, follow `angular/cdk/bidi` approaches but cross-verify edge-cases evaluated here for icons and grid reflections, guaranteeing optimal Arabic-default interface layout as strictly mandated by the Blueprint MVP.
