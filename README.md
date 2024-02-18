# OpenAPI spec for Falu

The spec files provided in the `specs` folder (`specs/openapi.{yaml,json}`) conform to the [OpenAPI specification](https://www.openapis.org/).

## Extensions

Falu uses a number of vendor-specific extensions in these spec files to provide extra information or enhance existing features. In most cases, you do not need to worry about them unless you are generating new or alternative SDKs.

Known extensions:

- [`x-patchable`](#x-patchable)
- [`x-pii`](#x-pii)
- [`x-error-codes`](#x-error-codes)
- [`x-supports-pagination`](#x-supports-pagination)

### `x-patchable`

Schemas containing `x-patchable` can be updated using via HTTP `PATCH`. This extension contains the names of properties that can be patched/updated.

In the example below, `metadata` and `expires` properties can be updated while `id`, `file`, and `url` cannot.

```yaml
components:
  ...
  schemas:
    ...
    FileLink:
      ...
      properties:
        metadata:
        expires:
        id:
        file:
        url:
      x-patchable:
        - metadata
        - expires
```

### `x-pii`

Schemas containing `x-pii` indicate there are properties in the schema that carry Personally Identifiable Information (PII) data. These properties are redacted by removal or masking when the `/{id}/redact` endpoint for the object is invoked.

In the example below, `body` property is marked as PII because it may contain sensitive personal information such as a password or health information.

```yaml
components:
  ...
  schemas:
    ...
    Message:
      ...
      properties:
        metadata:
        id:
        stream:
        body:
      x-pii:
        - body
```

### `x-error-codes`

Operations containing `x-error-codes` indicate the error codes that can result from its execution.

In the example below, calls made to `POST /v1/files` can result in 400 (Bad Request) responses with error code `file_type_not_allowed` or `file_invalid`.

```yaml
paths:
  ...
  /v1/files:
    ...
    post:
      ...
      x-error-codes:
        - file_type_not_allowed
        - file_invalid
```

> All possible error codes are documented <https://falu.io/docs/error-codes>

### `x-supports-pagination`

Operations containing `x-supports-pagination: true` indicate that pagination is supported. This is set for any operation/endpoint that returns a list of items. These operations will usually have query parameters for filtering depending on the type of object. In addition,

- `ct`: a continuation token that is used for the next request.
- `sort`: the sorting order (`asc|desc|ascending|descending`).
- `count`: the maximum number of items (must be less than or equal to 100).
- `created.{lt,lte,gt,gte}`: filter options for creation date.
