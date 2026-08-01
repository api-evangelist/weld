---
name: Activate a Weld Reverse ETL sync
description: Create a Reverse ETL sync to push modeled data into an operational destination, activate it, run it, and retry failed records using the Weld Connect API.
api: openapi/weld-connect-openapi.json
operations:
- ReverseEtlSyncsOpenApiController_getReverseEtlObjectTypes
- ReverseEtlSyncsOpenApiController_getReverseEtlDestinationSchema
- ReverseEtlSyncsOpenApiController_createReverseEtlSync
- ReverseEtlSyncsOpenApiController_activateReverseEtlSync
- ReverseEtlSyncsOpenApiController_requestRun
- ReverseEtlFailedRecordsOpenApiController_getReverseEtlSyncFailedRecordErrors
- ReverseEtlFailedRecordsOpenApiController_retryReverseEtlSyncFailedRecords
---

# Activate a Weld Reverse ETL sync

Reverse ETL activates warehouse/modeled data into operational tools. Use the Weld Connect API (`https://connect.weld.app`).

## Auth & conventions
- `x-api-key` header with a workspace-scoped API key.
- Reverse-ETL failed-record and run listings are cursor-paginated (`starting_after` + `limit`) and filtered by `reverse_etl_sync_id`.
- Errors are `{ error_code, message }` JSON; a `409` on run means one is already in progress.

## Steps
1. **List destination object types.** `ReverseEtlSyncsOpenApiController_getReverseEtlObjectTypes` (GET `/reverse_etl_syncs/object_types`) for the destination connection.
2. **Inspect the destination schema.** `ReverseEtlSyncsOpenApiController_getReverseEtlDestinationSchema` (GET `/reverse_etl_syncs/destination_schema`).
3. **Create the sync.** `ReverseEtlSyncsOpenApiController_createReverseEtlSync` (POST `/reverse_etl_syncs`) with the source model, destination connection, object type, and field mapping.
4. **Activate it.** `ReverseEtlSyncsOpenApiController_activateReverseEtlSync` (POST `/reverse_etl_syncs/{id}/activate`).
5. **Run it.** `ReverseEtlSyncsOpenApiController_requestRun` (POST `/reverse_etl_syncs/{id}/request_run`).
6. **Handle failures.** List errors with `ReverseEtlFailedRecordsOpenApiController_getReverseEtlSyncFailedRecordErrors` (GET `/reverse_etl_failed_records`), fix mappings, then `ReverseEtlFailedRecordsOpenApiController_retryReverseEtlSyncFailedRecords` (POST `/reverse_etl_failed_records/retry`).
---
