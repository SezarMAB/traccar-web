# Traccar Web Redux Integrations

This package (`src/store`) implements Redux Toolkit slices handling state mapping over the Traccar Rest APIs.

## LOGISTICS.SY Context

- **Reference Only**: This application acts as an internal reference admin view. LOGISTICS.SY is an Angular 18 project. Do not implement new store functions expecting them to naturally link with the shipper/carrier UI.
- **WebSocket to State Mapping**: The `events`, `devices`, and `positions` slices exhibit how Traccar's `/api/socket` inbound feeds should update state atomically without rest-polling. This applies directly to the intended `tracking` Module event-driven methodology inside LOGISTICS.SY's NgRx/Signals state structure. 
- **Caching & Hydration**: This architecture retrieves initial state upon login. Replicate this carefully in the Angular client to avoid cold-start memory traps on large fleets by virtualizing data and utilizing pagination instead of full fetches.
