# WebSockets

Haske offers first-class WebSocket support with convenience decorators and a broadcasting subsystem accelerated by the Rust extension. The same application instance can host HTTP routes and WebSockets, letting you build live dashboards, chats, and collaborative tools without introducing a second server.

## Defining a WebSocket route

Decorate an async function with `haske.ws.websocket_route`. The decorator registers a `WebSocketRoute`, wraps the underlying Starlette socket, and automatically tracks connections in the global broadcaster so you can broadcast later if needed.

```python
from haske.ws import websocket_route

@websocket_route("/ws")
async def chat_socket(ws):
    await ws.accept()
    await ws.send_text("Welcome to Haske Chat!")
    while True:
        message = await ws.receive_text()
        await ws.send_text(f"You said: {message}")
```

The wrapper exposes helpers such as `receive_json`, `send_json`, `ping`, `pong`, and, when the Rust extension is available, `receive_frame`/`send_frame` for fine-grained control over WebSocket frames.

## Broadcasting messages

Use the global `WebSocketBroadcaster` to fan messages out to all connections or specific channels. Messages can be strings, bytes, or dictionaries (which are automatically JSON-encoded). Channels allow you to group related sockets—for example, individual chat rooms.

```python
from haske.ws import get_broadcaster

broadcaster = get_broadcaster()
await broadcaster.create_channel("notifications")
await broadcaster.broadcast({"event": "new_post"}, channel="notifications")
```

When Rust is enabled, broadcasting uses the native manager for improved throughput; otherwise it falls back to Python loops.

## Advanced session management

`LiveSessionManager` builds on the broadcaster to maintain a dictionary of active sessions keyed by ID. It provides helpers to add/remove sessions, push targeted messages, and broadcast to everyone. This is a great starting point for presence systems or collaborative editing flows.

## Utilities

- `websocket_handshake()` computes the Sec-WebSocket-Accept header, delegating to Rust when possible.
- `is_websocket_upgrade()` and `validate_websocket_request()` inspect HTTP headers to determine whether a request should be upgraded to a WebSocket connection.

Combine these pieces with Haske’s middleware and routing APIs to build rich real-time user experiences.
