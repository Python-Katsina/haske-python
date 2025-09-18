# Quickstart

Follow these steps to spin up a Haske application that responds with JSON and reloads automatically during development.

## 1. Create `app.py`

```python
from haske import Haske, Request, Response

app = Haske(__name__)

@app.route("/")
async def home(request: Request) -> Response:
    return {"message": "Hello, Haske!"}

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, reload=True)
```

This is the minimal asynchronous application shown in the README. It imports the core `Haske` class, declares a single route, and starts the development server with live reload enabled.

## 2. Run the development server

```bash
python app.py
```

The server boots on port 8000. Visit <http://localhost:8000> to confirm you receive the JSON payload.

Alternatively, use the CLI runner to automatically locate your application module:

```bash
haske dev --module app:app --host 0.0.0.0 --port 8000
```

The CLI wraps Uvicorn and exposes flags for host, port, reload, and worker count, mirroring the options available in the Python entrypoint.

## 3. Iterate with auto-reload

When `reload=True`, Haske watches your source files. Edits trigger a restart, so new routes or templates appear immediately.

## 4. Add another endpoint

Extend the app with an additional route to get a feel for the routing decorator:

```python
@app.route("/user/{username}")
async def greet_user(request: Request, username: str):
    return {"message": f"Hello {username}"}
```

Haske automatically parses the `{username}` parameter and passes it into your handler.

That is all you need to start building! Continue to the next section for a look at the default project structure and how to organise larger applications.
