# Configuration Reference

This page summarises the core configuration knobs available on the `Haske` application class and supporting utilities. Use it as a checklist when wiring a new project or deploying to different environments.

## Application constructor

```python
from haske import Haske

app = Haske(
    name="myapp",
    template_dir="templates",
    static_dir="static"
)
```

- `name` – logical name used by Starlette when constructing the ASGI app.
- `template_dir` / `static_dir` – locations passed to the templating subsystem and used when automatically mounting static files.

Creating the app sets up template paths, initialises the optional Rust router, prepares frontend state, and attaches a WebSocket broadcaster. Haske also installs default CORS and gzip middleware and attempts to mount `/static` immediately so assets are served without extra steps.

## Middleware & mounts

- `app.middleware(cls, **options)` – append Starlette-compatible middleware to the stack. Handy for adding authentication, sessions, or compression beyond the defaults.
- `app.mount(path, app, name=None)` – mount other ASGI apps (e.g., admin dashboards or documentation sites).
- `app.static(path="/static", directory=None, name=None)` – mount a static directory and synchronise the template environment with the new path.

## Frontend integration

`app.setup_frontend(config, mode)` detects build directories, mounts static assets, and—when running in development—launches a frontend dev server and proxies requests to it. Key configuration keys include `frontend_dir`, `build_dir`, `dev_command`, `dev_port`, and `force_dev`. The helper also honours the `HASKE_SKIP_FRONTEND` environment variable to avoid launching duplicate dev servers.

Retrieve a public URL for a given path with `app.get_frontend_url(path)`, which returns a relative path in production and the proxied dev server URL in development.

## Environment variables

- `HASKE_DEBUG` – toggles Starlette’s debug mode and is set automatically when you pass `debug=True` to `app.run()`. You can also export it manually before building the app.
- `HASKE_SKIP_FRONTEND` – prevents `setup_frontend()` from launching a dev server if one is already running (set to `1` by the helper after it spawns the process).

## Runtime helpers

- `app.route()` / `app.websocket()` – register HTTP and WebSocket routes while keeping middleware-aware ordering through `_reorder_routes`.
- `app.run(host="0.0.0.0", choosen_port=8000, debug=False)` – wrap Uvicorn, ensure a free port, and apply reload/debug settings. Use this for local development; production deployments should invoke Uvicorn or Gunicorn directly.

Keep these options in mind as you grow your Haske project—they provide the hooks needed to customise behaviour without forking the framework.
