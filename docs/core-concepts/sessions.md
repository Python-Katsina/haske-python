# Sessions & State

Haske applications often need to persist information between requests—user identities, flash messages, rate-limit counters, and more. The framework supplies building blocks for cookie-based sessions, signed tokens, and in-memory caching so you can pick the right strategy for each use case.

## Cookie sessions

Enable server-managed sessions by adding the session middleware factory. Session data is stored in signed cookies by default.

```python
from haske.middleware import SessionMiddlewareFactory

app.middleware(*SessionMiddlewareFactory(secret_key="super-secret", max_age=86400))
```

Within a handler you can read and mutate `request.session` to persist per-user state across requests, as shown in the login/profile example from the README.

## Signed tokens

For stateless APIs or SPAs, issue signed session tokens. The helpers generate HMAC-protected payloads and verify them on subsequent requests, delegating to Rust for speed when available.

```python
from haske.auth import create_session_token, verify_session_token

token = create_session_token(secret, {"user_id": 123}, expires_in=3600)
payload = verify_session_token(secret, token)
```

If verification fails or the token has expired, `verify_session_token` returns `None`. Pair this with middleware or dependency injection to attach authenticated users to incoming requests.

## Passwords & CSRF protection

Haske also exposes helpers for hashing passwords, validating hashes, generating CSRF tokens, and comparing them in constant time. These utilities rely on the Rust crypto primitives when available and include Python fallbacks for portability.

## Caching

Use `haske.cache.Cache` for high-speed in-memory caching. The cache automatically chooses the Rust implementation when compiled, storing entries with configurable size and TTL. A Python fallback maintains compatibility when native modules are unavailable.

```python
from haske.cache import Cache

cache = Cache(max_size=500, ttl=300)
value = cache.get("recent_posts")
if value is None:
    value = load_posts()
    cache.set("recent_posts", value)
```

Each cache instance exposes `get`, `set`, `delete`, `clear`, and `size` operations, making it a lightweight alternative to Redis or Memcached for smaller deployments.

Combine these approaches as needed—cookie sessions for browser clients, signed tokens for APIs, and caches for expensive computations or third-party responses.
