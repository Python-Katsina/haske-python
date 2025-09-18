# Templates

Haske uses Jinja2 for HTML rendering and layers conveniences on top so you can wire templates into your routes with minimal boilerplate. When the Rust extension is installed, template rendering and precompilation gain an additional performance boost, but the Python implementation continues to work seamlessly.

## Configuring directories

Templates live in the directory you pass to `Haske(...)` or configure via `configure_templates()`. The helper ensures both template and static directories exist, initialises the Jinja2 environment, and injects handy globals such as `url_for` and `static_url` for linking to routes and static assets.

```python
from haske import Haske

app = Haske(__name__, template_dir="templates", static_dir="static")
```

## Rendering helpers

- `render_template()` – synchronous helper returning an HTML string.
- `render_template_async()` – async equivalent useful inside async route handlers.
- `template_response()` – returns an `HTMLResponse` directly.
- `TemplateEngine.TemplateResponse()` – renders into an `HTMLResponse` using an instance-specific engine.

All helpers automatically inject the current request into the context when possible, making it easy to access `request` from within templates without passing it manually.

```python
from haske.templates import render_template_async

@app.route("/index")
async def index(request: Request):
    return await render_template_async("index.html", title="Welcome", items=["Fast", "Flexible", "Rust-powered"])
```

## Precompilation

`TemplateEngine.precompile()` caches template sources (and leverages Rust when available) so you can render frequently-used templates without re-reading them from disk. Use `render_precompiled()` to render from the cached representation while falling back to Jinja2 as needed.

## Accessing URLs and static files

Because the environment injects `url_for` and `static_url`, templates can link to routes and assets declaratively:

```html
<link rel="stylesheet" href="{{ static_url('app.css') }}">
<a href="{{ url_for('home') }}">Home</a>
```

Pair this with `app.static()` to expose your static directory at `/static`. The helper reconfigures the template environment and ensures the mount appears after API routes.
