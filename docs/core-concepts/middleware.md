# Middleware

Middleware wraps your application to inspect or modify traffic before it reaches route handlers. Haske exposes Starlette’s middleware interface while providing factories and accelerated implementations for common concerns such as sessions, CORS, compression, and rate limiting.

## Registering middleware

Call `app.middleware()` with a Starlette-compatible middleware class and keyword arguments. When the application builds, the middleware stack is passed to the underlying Starlette instance in the order you registered them.

```python
from haske.middleware import Middleware, SessionMiddlewareFactory

app.middleware(*SessionMiddlewareFactory(secret_key="super-secret"))
```

The helper factories return a `(cls, options)` tuple, so you can use argument unpacking to keep your configuration concise.

## Built-in helpers

- **SessionMiddlewareFactory** – Configures signed cookie sessions using Starlette’s `SessionMiddleware`. Supply a `secret_key` and optional cookie settings.
- **CORSMiddlewareFactory** – Applies Cross-Origin Resource Sharing headers with configurable origins, methods, headers, and credentials.
- **CompressionMiddlewareFactory** – Wraps Starlette’s gzip middleware and lets you tweak minimum size and compression level.
- **CompressionMiddleware** – Custom ASGI middleware that negotiates gzip/brotli compression using the Rust helpers for maximum throughput.
- **RateLimitMiddlewareFactory** – Adds IP-based request throttling with configurable limits per time window.

You can also pass any third-party Starlette middleware class directly to `app.middleware()`.

## Writing custom middleware

Implement the ASGI callable interface—accept `(scope, receive, send)` and forward to the downstream app when appropriate. The custom compression and rate limiting implementations in the source serve as references for intercepting response bodies and maintaining request state.

Remember to keep middleware order in mind: earlier entries see requests before later ones and receive responses on the way back up the chain.
