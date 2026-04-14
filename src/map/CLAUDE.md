# Traccar Web Map Operations

This package (`src/map`) is the core MapLibre functionality providing live-tracking visualization and geofence drawing bounds.

## LOGISTICS.SY Context

- **MapLibre Translation**: Uses `mapbox-gl-draw` to process Geofence interactions. The mapping algorithms (calculating dynamic markers, drawing the latest Position tails via line vectors) are fundamental to adapting to LOGISTICS.SY's Angular Leaflet/MapLibre map component.
- **RTL Support on Maps**: Keep in mind Mapbox/MapLibre requires `@mapbox/mapbox-gl-rtl-text` for Arabic shapes/texts (roads/places names) to render correctly across Syrian geography. Use this plugin when porting over to Angular tracking maps.
- **Trip Replay UI**: Can be directly embedded via iframe per Blueprint Phase 2 strategies, reducing immediate implementation demands on historical path animations.
