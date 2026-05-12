# iteration-01-backend-websocket-foundation.md

# Iteration 01 – Backend Foundation & WebSocket Infrastructure

## 1. Purpose

This iteration establishes the technical foundation of Assassin Party Game.

The goal is to create a running Node.js application that can:

- serve the web frontend
- accept WebSocket connections
- distinguish player and GameMaster clients
- maintain a basic in-memory connection registry
- prepare JSON-based game-state persistence
- provide the foundation for all later real-time gameplay features

This iteration does not yet implement the full game logic.

---

## 2. Scope

### Included

- Node.js application setup
- Express HTTP server
- static frontend delivery
- WebSocket server setup
- basic message protocol
- client role identification
- connection tracking
- basic broadcast mechanism
- initial JSON persistence utilities
- health endpoint
- development environment setup

### Excluded

- player assignment graph
- lobby-code generation
- kill validation
- leaderboard
- GameMaster administration
- production Nginx configuration
- final UI polish

---

## 3. Related Roadmap Phases

This iteration covers parts of:

- Phase 1 – Backend Foundation
- Phase 2 – WebSocket Infrastructure
- Phase 3 – Game State Model and JSON Persistence

---

## 4. Related Requirements

| ID | Requirement |
|---|---|
| SYS-003 | Real-Time Synchronization |
| SYS-004 | Persist Game State |
| UX-001 | Mobile Optimized Player Interface |
| UX-002 | Desktop Optimized GM Interface |

---

## 5. Functional Specification

## 5.1 Express Application

The system shall provide an Express-based HTTP server.

### Requirements

- The server shall start on a configurable port.
- The server shall serve static files from a public frontend directory.
- The server shall expose a health endpoint.
- The server shall be configurable through environment variables.

### Suggested Routes

```txt
GET /              -> player frontend
GET /health        -> health check
GET /gamemaster-[secret] -> GameMaster frontend
```

The GameMaster path may be hardcoded during MVP development and later moved into environment configuration.

---

## 5.2 WebSocket Server

The system shall provide a WebSocket server attached to the Node.js application.

### Requirements

- Clients shall be able to establish a WebSocket connection.
- The server shall register each connected client.
- The server shall remove disconnected clients.
- The server shall support basic ping/pong or heartbeat logic.
- The server shall support JSON-based messages.

### Example Message Format

```json
{
  "type": "CLIENT_HELLO",
  "role": "player",
  "payload": {}
}
```

---

## 5.3 Client Role Identification

The server shall distinguish at least two client roles:

```txt
player
gamemaster
```

### Requirements

- A connected client shall be considered unidentified until it sends a valid hello message.
- Player clients shall be stored separately from GameMaster clients.
- Invalid roles shall be rejected or ignored.
- Role information shall be stored only server-side.

---

## 5.4 Broadcast System

The server shall support basic real-time message delivery.

### Required Delivery Modes

```txt
broadcastToAll(message)
broadcastToPlayers(message)
broadcastToGameMasters(message)
sendToClient(clientId, message)
```

### Requirements

- Messages shall be serialized as JSON.
- Broken connections shall not crash the server.
- Failed sends shall be logged.
- The broadcast layer shall not expose private player data by default.

---

## 5.5 JSON Persistence Utilities

The system shall prepare basic JSON persistence utilities.

### Requirements

- The application shall create a `/data` directory if it does not exist.
- The application shall be able to read JSON files.
- The application shall be able to write JSON files.
- Writes should use an atomic write pattern:
  1. write temporary file
  2. validate file
  3. rename temporary file to final file
- Invalid JSON shall not overwrite valid existing state.

### Initial Files

```txt
/data/game-state.json
/data/events.json
```

---

## 6. Non-Functional Requirements

## 6.1 Stability

- Server shall not crash when a malformed WebSocket message is received.
- Server shall not crash when a client disconnects unexpectedly.
- Server shall log unexpected errors.

## 6.2 Maintainability

- WebSocket message handlers shall be separated from Express route setup.
- Persistence utilities shall be reusable by later iterations.
- Server startup logic shall be clearly isolated.

## 6.3 Security

- GameMaster route shall not be linked from the public player interface.
- The WebSocket layer shall not trust client-provided role information for privileged actions.
- Privileged GameMaster actions are not yet implemented in this iteration.

---

## 7. Validation Rules

## 7.1 WebSocket Message Validation

Every received WebSocket message must satisfy:

- message is valid JSON
- message has a `type` field
- `type` is a non-empty string
- `payload` is either an object or omitted

Invalid messages shall result in an error response, not a server crash.

### Example Error Response

```json
{
  "type": "ERROR",
  "payload": {
    "code": "INVALID_MESSAGE",
    "message": "Message must be valid JSON with a type field."
  }
}
```

---

## 7.2 Role Validation

Accepted roles:

```txt
player
gamemaster
```

Rejected roles:

```txt
admin
root
assassin-god
whatever-the-client-invented
```

---

## 7.3 Persistence Validation

Before writing game state:

- data must be serializable as JSON
- top-level value must be an object
- required base fields must exist

Suggested base state:

```json
{
  "session": null,
  "players": [],
  "words": [],
  "assignments": [],
  "events": [],
  "status": "idle"
}
```

---

## 8. Acceptance Criteria

## AC-01 – Server Startup

Given the application is started  
When the configured port is available  
Then the Express server starts successfully.

## AC-02 – Static Frontend Delivery

Given the server is running  
When a browser opens `/`  
Then the player frontend is served.

## AC-03 – Health Endpoint

Given the server is running  
When `/health` is requested  
Then the server responds with status `200`.

## AC-04 – WebSocket Connection

Given the server is running  
When a client opens a WebSocket connection  
Then the connection is accepted and tracked.

## AC-05 – Client Hello

Given a connected WebSocket client  
When the client sends a valid `CLIENT_HELLO` message  
Then the server stores the client role.

## AC-06 – Invalid Message Handling

Given a connected WebSocket client  
When the client sends invalid JSON  
Then the server responds with an error message and remains running.

## AC-07 – Broadcast

Given multiple connected clients  
When the server broadcasts a message  
Then all intended recipients receive the message.

## AC-08 – JSON Persistence

Given a valid game-state object  
When the persistence utility writes the state  
Then the corresponding JSON file is created or updated.

## AC-09 – Restart Recovery Preparation

Given an existing valid `game-state.json` file  
When the server starts  
Then the file can be read without error.

---

## 9. Suggested File Structure

```txt
assassin-party-game/
├── package.json
├── server.js
├── .env
├── /public
│   ├── index.html
│   ├── gamemaster.html
│   ├── app.js
│   └── styles.css
├── /src
│   ├── config.js
│   ├── httpServer.js
│   ├── websocketServer.js
│   ├── messageProtocol.js
│   ├── clientRegistry.js
│   ├── persistence.js
│   └── logger.js
└── /data
    ├── game-state.json
    └── events.json
```

---

## 10. Test Checklist

- [ ] Server starts locally.
- [ ] `/health` returns HTTP 200.
- [ ] `/` serves player frontend.
- [ ] GameMaster page is reachable through secret path.
- [ ] WebSocket client can connect.
- [ ] Player client can identify itself.
- [ ] GameMaster client can identify itself.
- [ ] Invalid JSON does not crash server.
- [ ] Broadcast message reaches connected clients.
- [ ] JSON state file can be created.
- [ ] JSON state file can be loaded after restart.

---

## 11. Definition of Done

Iteration 01 is complete when the application has a stable HTTP and WebSocket foundation that can be used by the lobby, GameMaster, and gameplay systems in later iterations.

No gameplay logic is required yet.

The result should feel boring, stable, and technically trustworthy.
