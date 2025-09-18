# Static Assets & SPA Integration

Haske makes it easy to serve traditional static files and modern single-page applications (SPAs) from the same process. Use whichever approach fits your stack: mount a static directory, ship a compiled frontend, or proxy to a running dev server during development.

## Serving `/static`

Call `app.static()` to expose a directory of assets. The helper resolves the absolute path, configures the template environment with the new static directory, and mounts it after your API routes so dynamic paths continue to win precedence.

```python
app.static("/static", directory="./public")
```

## Production SPA hosting

`Haske.setup_frontend()` inspects your frontend build directory and mounts the appropriate static folders. In production mode it serves prebuilt files (e.g., from `frontend/build`, `dist`, or `.next`) and mounts additional directories for frameworks like Next.js or Vite.

For lower-level control you can use `FrontendServer` directly. It serves files, handles SPA fallbacks by returning `index.html` for unknown routes, and sets caching headers depending on `development_mode`. Extra mounts for framework-specific directories (`_next`, `static`, `public`, etc.) are added automatically.

## Frontend dev servers

During active development, `setup_frontend()` can launch or proxy to a JavaScript dev server. It finds a free port, runs the configured command (defaults to `npm run dev`), waits for the server to start, and installs a reverse proxy so browser requests hit your frontend while `/api` routes stay within Haske.

If you prefer to manage the dev server yourself, instantiate `FrontendDevelopmentServer` with the URL of your running dev server and call `proxy_request()` inside a custom route or middleware. The helper forwards the full request using `httpx` and returns the response to the client.

## Framework presets

Use `create_frontend_config()` to generate sensible defaults for React, Vue, Next.js, Angular, or Svelte projects. The dictionary includes build paths and dev server URLs that you can feed into `setup_frontend()` or your own integration layer.

These tools let you keep your backend and frontend cohesive without sacrificing fast feedback loops or production-ready caching.
