---
name: Run and monitor a Weld ELT sync
description: Create an ELT sync from a source connection, add source streams, enable it, trigger a run, and monitor status using the Weld Connect API.
api: openapi/weld-connect-openapi.json
operations:
- ConnectionsOpenApiController_listConnections
- EltSyncsOpenApiController_createEltSync
- EltSyncsOpenApiController_getSourceStreams
- EltSyncsOpenApiController_addSourceStreams
- EltSyncsOpenApiController_enableEltSync
- EltStreamsOpenApiController_getEltStreams
- EltStreamsOpenApiController_requestRun
- EltSyncsOpenApiController_getEltSyncStatus
---

# Run and monitor a Weld ELT sync

Use the Weld Connect API (`https://connect.weld.app`) to stand up and operate an ELT sync.

## Auth & conventions
- Send your workspace API key in the `x-api-key` header on every request (create it in the Weld app under Settings -> API keys).
- List endpoints are cursor-paginated: pass `starting_after` (the id of the last item seen) and `limit`.
- Errors return `application/json` `{ "error_code": "...", "message": "..." }`. A `409` means a run/refresh is already in progress — poll status before retrying rather than re-issuing.
- No idempotency-key is documented; avoid blindly retrying POSTs — check state first.

## Steps
1. **Find the source connection.** `ConnectionsOpenApiController_listConnections` (GET `/connections`) and note the `id` of the source you want to sync from.
2. **Create the ELT sync.** `EltSyncsOpenApiController_createEltSync` (POST `/elt_syncs`) with the source connection and destination.
3. **Discover available streams.** `EltSyncsOpenApiController_getSourceStreams` (GET `/elt_syncs/{id}/available_source_streams`).
4. **Add the streams you want.** `EltSyncsOpenApiController_addSourceStreams` (POST `/elt_syncs/{id}/source_streams`).
5. **Enable the sync.** `EltSyncsOpenApiController_enableEltSync` (POST `/elt_syncs/{id}/enable`).
6. **Trigger a run.** List the sync's streams with `EltStreamsOpenApiController_getEltStreams` (GET `/elt_streams`), then `EltStreamsOpenApiController_requestRun` (POST `/elt_streams/{id}/request_run`).
7. **Monitor.** Poll `EltSyncsOpenApiController_getEltSyncStatus` (GET `/elt_syncs/{id}/status`) until the run completes.
