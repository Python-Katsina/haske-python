# Testing & Deployment

The Haske toolchain supports a full lifecycle: write tests against the ASGI app, harden error handling for production, and deploy behind any ASGI server that speaks Uvicorn’s interface.

## Testing strategies

Because Haske applications are Starlette apps under the hood, you can reuse Starlette’s `TestClient` (powered by `requests`) or `httpx.AsyncClient` to exercise routes. The README’s pytest snippet demonstrates creating a client, issuing a request, and asserting on the JSON payload.

```python
from starlette.testclient import TestClient
from myapp import app

def test_homepage():
    client = TestClient(app)
    response = client.get("/")
    assert response.status_code == 200
    assert response.json()["message"] == "Hello, Haske!"
```

For async tests, use `pytest-asyncio` and `httpx.AsyncClient` to `await` the responses instead.

## Error handling in production

Install the provided exception handlers to return consistent JSON envelopes for framework, validation, and HTTP errors. The helpers produce error codes, HTTP status metadata, and optional detail payloads, making them ideal for APIs consumed by SPAs or mobile apps.

```python
from haske.exceptions import install_error_handlers

install_error_handlers(app)
```

Combine this with middleware such as compression, rate limiting, and CORS to harden your public endpoints (see the Middleware chapter for details).

## Deployment options

Use `app.run()` during development for quick iterations—it wraps Uvicorn, finds an available port, and enables reload when you set `debug=True` or pass `reload=True` in your entrypoint.

For production, run the application with a dedicated ASGI server:

```bash
uvicorn myapp:app --host 0.0.0.0 --port 8000 --workers 4
gunicorn -k uvicorn.workers.UvicornWorker myapp:app
```

These commands mirror the recommendations from the README and allow you to scale horizontally across CPU cores.

Before deploying, execute `haske build` to ensure Rust extensions are compiled and your frontend assets are packaged (see the CLI chapter). Pair the ASGI server with a process manager such as systemd, Docker, or Kubernetes for long-running services.
