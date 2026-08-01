---
name: Build and publish a Weld SQL transform
description: Create a SQL transform, preview its query, inspect the resulting schema, and publish a version using the Weld Connect API.
api: openapi/weld-connect-openapi.json
operations:
- TransformOpenApiController_getAvailableReferences
- TransformOpenApiController_createTransform
- TransformOpenApiController_createQueryPreview
- TransformOpenApiController_getQueryPreviewStatus
- TransformOpenApiController_getQueryPreviewResult
- TransformOpenApiController_getTransformSchema
- TransformOpenApiController_publishTransform
- TransformOpenApiController_listVersions
---

# Build and publish a Weld SQL transform

Transforms are Weld's SQL data models. Use the Weld Connect API (`https://connect.weld.app`) to author, validate, and publish one.

## Auth & conventions
- `x-api-key` header with a workspace-scoped API key on every request.
- Query previews run asynchronously: create the preview, poll status, then fetch the result.
- Errors are `{ error_code, message }` JSON. Handle `400` (invalid SQL/refs) by fixing the query before retrying.

## Steps
1. **Discover referenceable models/sources.** `TransformOpenApiController_getAvailableReferences` (GET `/transforms/available_references`).
2. **Create the transform.** `TransformOpenApiController_createTransform` (POST `/transforms`) with the SQL and configuration.
3. **Preview the query (async).** `TransformOpenApiController_createQueryPreview` (POST `/transforms/query_previews`), then poll `TransformOpenApiController_getQueryPreviewStatus` (GET `/transforms/query_previews/{queryExecutionId}/status`) and read `TransformOpenApiController_getQueryPreviewResult` (GET `/transforms/query_previews/{queryExecutionId}/result`).
4. **Inspect the output schema.** `TransformOpenApiController_getTransformSchema` (GET `/transforms/{id}/schema`).
5. **Publish.** `TransformOpenApiController_publishTransform` (POST `/transforms/{id}/publish`).
6. **Confirm the version.** `TransformOpenApiController_listVersions` (GET `/transforms/{id}/versions`).
