# Requests & Responses

Haske wraps Starlette’s request/response primitives to provide faster JSON handling, convenient helpers, and sensible defaults such as automatic CORS headers. Understanding these utilities lets you write concise handlers without juggling low-level ASGI objects.

## Request object

Route handlers receive a `haske.request.Request` instance. It exposes familiar properties such as `method`, `path`, `headers`, and `cookies`, plus async helpers for reading the body in various formats.

Key capabilities:

- **Cached body access** – `await request.body()` streams the body once and reuses the bytes for subsequent calls.
- **Accelerated JSON parsing** – `await request.json()` uses the Rust parser when available and falls back to Python if necessary.
- **Form helpers** – `await request.form()` parses URL-encoded payloads, while `.is_form()` and `.is_json()` quickly inspect the content type.
- **Query utilities** – `get_query_param()` handles default values and repeated parameters without you reaching into `scope` directly.
- **Validation hooks** – `await request.validate_json(schema)` integrates with Marshmallow- or Pydantic-style schemas, raising Haske’s `ValidationError` when data is invalid.

## Response conversion

Handlers can return dictionaries, lists, strings, Haske response instances, or any Starlette response class. The framework normalises the value via `_convert_to_response`, turning plain dicts/lists into JSON responses and strings into HTML responses. Custom response objects pass through untouched.

Automatic CORS headers are appended to every response, ensuring browsers can call your APIs during development without extra middleware configuration.

## Response classes

Haske ships thin wrappers around Starlette responses plus a base `Response` class with optional compression:

- `Response` – adds `.compress()` which uses Rust-based gzip/brotli when available.
- `JSONResponse`, `HTMLResponse`, `RedirectResponse`, `StreamingResponse`, `FileResponse`, and `APIResponse` – convenience subclasses for common media types.

### Example

```python
from haske import JSONResponse

@app.route("/ping")
async def ping(request: Request):
    data = await request.json()
    return JSONResponse({"echo": data.get("message", "pong")}, status_code=201)
```

Combine these building blocks with middleware or exception handlers to craft consistent API surfaces.
