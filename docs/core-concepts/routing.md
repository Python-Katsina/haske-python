# Routing

Haske’s router is decorator-driven and compatible with async callables. Routes are stored on the application instance and exposed to Starlette during the build step. A Rust-powered matcher accelerates path compilation when the native extension is installed, while Python fallbacks preserve functionality everywhere else.

## Defining routes

```python
@app.route("/hello")
async def say_hello(request: Request):
    return {"message": "Hello World"}
```

The `route` decorator accepts a path, optional HTTP methods, and an optional name. Handlers should return dictionaries, Starlette responses, or Haske response classes—plain dictionaries are automatically converted to JSON responses.

To support multiple methods, pass the `methods` argument:

```python
@app.route("/submit", methods=["POST"])
async def submit(request: Request):
    data = await request.json()
    return {"received": data}
```

## Path parameters & converters

Parameters declared inside braces (`/user/{username}`) are parsed and injected into your handler. Haske ships with reusable converters for integers, floats, and UUIDs; you can implement custom converters by subclassing `PathConverter` and registering them with the converter registry.

## URL generation

Use `haske.routing.get_url()` (also exposed in templates as `url_for`) to generate paths by endpoint name, mirroring Flask’s `url_for` helper. This function inspects the registered routes on the current application and fills in path parameters for you.

```python
from haske.routing import get_url

profile_url = get_url("profile", username="alice")
```

## Mounting sub-apps and static routes

Because Haske sits on top of Starlette, you can mount additional ASGI applications or static file handlers. The `setup_frontend` helper demonstrates how Haske mounts multiple static directories for production builds, and you can use the same approach for microservices or third-party dashboards.

## WebSocket routes

WebSocket support mirrors HTTP routing but is documented in the real-time chapter. Behind the scenes Haske registers `WebSocketRoute` objects and exposes high-level helpers for broadcasting and connection management.
