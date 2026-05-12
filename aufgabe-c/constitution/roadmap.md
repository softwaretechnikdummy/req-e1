# roadmap.md

# Assassin Party Game – Technical MVP Roadmap

## 1. Roadmap Scope

This roadmap describes the technical implementation phases for the MVP of Assassin Party Game.

The MVP is a public web application with:

- Node.js backend
- Express HTTP server
- WebSocket-based real-time communication
- Bootstrap/JavaScript frontend
- JSON file-based game state persistence
- Nginx reverse proxy with SSL termination
- exactly one active party session

The roadmap is intended for development planning, not marketing or investor communication.

---

## Phase 1 – Backend Foundation

### Goal

Establish the basic Node.js application structure and runtime foundation.

### Technologies

- Node.js
- Express
- dotenv
- nodemon for development
- basic filesystem access via Node.js `fs/promises`

### Tasks

- Initialize Node.js project.
- Set up Express server.
- Serve static frontend files.
- Define project folder structure.
- Add environment configuration.
- Add basic health endpoint.
- Add initial logging.
- Prepare `/data` directory for JSON persistence.

### Deliverables

- Running Express application.
- Static frontend can be served.
- Server can read/write local JSON files.
- Basic development workflow is functional.

### Related Requirements

- SYS-004
- UX-001
- UX-002

---

## Phase 2 – WebSocket Infrastructure

### Goal

Create the real-time communication layer used by players and GameMaster clients.

### Technologies

- Node.js
- WebSocket server library, e.g. `ws`
- Express HTTP upgrade handling
- JSON message protocol

### Tasks

- Add WebSocket server.
- Define WebSocket message format.
- Track connected clients.
- Distinguish client roles:
  - player
  - gamemaster
- Implement server-to-client broadcast.
- Implement targeted player messages.
- Implement reconnect handling based on stored lobby code.
- Add basic heartbeat/ping handling.

### Deliverables

- Clients can connect via WebSocket.
- Server can broadcast global state changes.
- Server can send private updates to individual players.
- Disconnected clients can reconnect.

### Related Requirements

- SYS-003
- PL-001
- GM-006

---

## Phase 3 – Game State Model and JSON Persistence

### Goal

Define the authoritative server-side game state and persist it safely to JSON files.

### Technologies

- Node.js
- JSON files
- `fs/promises`
- atomic write pattern using temporary files and rename

### Tasks

- Define canonical game state schema.
- Define player entity structure.
- Define word list structure.
- Define assignment graph structure.
- Define event log structure.
- Implement load-state-on-startup.
- Implement save-state-after-mutation.
- Add validation before writing JSON.
- Add backup or snapshot strategy for corrupt-state recovery.

### Deliverables

- Game state survives server restart.
- State mutations are persisted consistently.
- Invalid state cannot silently overwrite valid state.

### Related Requirements

- SYS-004
- GM-001
- GM-002
- GM-003

---

## Phase 4 – GameMaster Setup Interface

### Goal

Implement the desktop-oriented GameMaster workflow for preparing a party session.

### Technologies

- HTML
- JavaScript
- Bootstrap
- WebSocket messages
- Express static routing

### Tasks

- Create secret GameMaster route.
- Build setup screen.
- Add multiline textarea for player names.
- Add multiline textarea for target words.
- Parse one entry per line.
- Validate minimum player count.
- Validate minimum word count.
- Save player and word lists to game state.
- Display current setup state.

### Deliverables

- GameMaster can create and edit the initial party setup.
- Player and word lists are persisted.
- Setup can be restored after browser reload.

### Related Requirements

- GM-001
- GM-002
- GM-003
- UX-002

---

## Phase 5 – Lobby and Temporary Identity System

### Goal

Allow players to join the party without registration and allow the GameMaster to associate lobby codes with prepared identities.

### Technologies

- Browser localStorage
- WebSocket
- Node.js random code generation
- Bootstrap mobile UI

### Tasks

- Create player landing page.
- Generate unique lobby code on first visit.
- Store lobby code in `localStorage`.
- Reuse lobby code on reconnect.
- Show lobby waiting state to player.
- Show waiting lobby codes to GameMaster.
- Allow GameMaster to assign lobby codes to player identities.
- Push assignment updates in real time.

### Deliverables

- Players can join without accounts.
- Lobby codes uniquely identify temporary devices.
- GameMaster can manually connect devices to player identities.

### Related Requirements

- PL-001
- PL-002
- GM-006
- GM-007
- GM-010
- GM-011

---

## Phase 6 – Assignment Graph Engine

### Goal

Generate and maintain the circular player assignment graph.

### Technologies

- Node.js
- Server-side graph generation logic
- JSON-persisted graph state

### Tasks

- Implement random circular player ordering.
- Assign each active player exactly one target.
- Ensure each active player has exactly one pursuer.
- Prevent self-targeting.
- Assign target words.
- Store graph in persisted game state.
- Add validation function for graph consistency.
- Add GameMaster action to start game and generate assignments.

### Deliverables

- Valid circular assignment graph.
- No player targets themselves.
- Each player has exactly one target and one pursuer.
- Assignments are persisted and recoverable.

### Related Requirements

- GM-004
- GM-005
- PL-003
- PL-004
- SEC-001

---

## Phase 7 – Player Mission Interface

### Goal

Display each player's private mission after assignment.

### Technologies

- HTML
- JavaScript
- Bootstrap
- WebSocket targeted messages

### Tasks

- Build mobile player mission screen.
- Display assigned target player.
- Display assigned target word.
- Hide all other player assignments.
- Update mission screen in real time after graph changes.
- Handle inactive, unassigned, and eliminated states.

### Deliverables

- Player sees only their own mission.
- Mission updates are delivered without page reload.
- Sensitive information is not exposed client-side.

### Related Requirements

- PL-003
- PL-004
- SEC-001
- UX-001

---

## Phase 8 – Kill Transaction System

### Goal

Implement the core elimination mechanic and graph update logic.

### Technologies

- Node.js
- WebSocket
- JSON persistence
- atomic state mutation function

### Tasks

- Generate or store victim badge codes.
- Add kill submission UI for active players.
- Validate that submitted badge belongs to the current target.
- Reject invalid kill attempts.
- Mark victim as eliminated.
- Transfer victim's target and target word to killer.
- Reconnect circular graph.
- Persist mutation atomically.
- Broadcast updated state.
- Send death screen data to eliminated player.

### Deliverables

- Valid kills eliminate players.
- Invalid kills are rejected.
- Killer inherits victim's mission.
- Eliminated player sees death screen.
- Graph remains valid after every kill.

### Related Requirements

- PL-005
- PL-006
- PL-007
- PL-008
- SEC-002

---

## Phase 9 – Endgame and Leaderboard

### Goal

Automatically detect game completion and display final results.

### Technologies

- Node.js
- WebSocket broadcast
- Bootstrap frontend

### Tasks

- Detect when only one active player remains.
- Mark session as ended.
- Compute leaderboard by kill count.
- Broadcast game-end event.
- Display winner.
- Display leaderboard to all participants.
- Keep final state persisted.

### Deliverables

- Game ends automatically.
- Winner is determined server-side.
- All participants see final leaderboard.

### Related Requirements

- SYS-001
- SYS-002

---

## Phase 10 – GameMaster Administration

### Goal

Allow the GameMaster to actively manage the session before and during gameplay.

### Technologies

- Bootstrap desktop UI
- WebSocket admin actions
- server-side validation
- event log

### Tasks

- Remove cancelled players.
- Add late players during lobby phase.
- Reassociate player identity to another lobby code.
- Change assigned target word.
- Display current graph state to GameMaster.
- Log administrative actions.
- Validate that admin actions do not corrupt the graph.

### Deliverables

- GameMaster can correct real-world party chaos.
- Administrative actions are persisted.
- Graph remains valid after manual interventions.

### Related Requirements

- GM-008
- GM-009
- GM-010
- GM-011
- GM-012
- GM-013

---

## Phase 11 – Production Deployment

### Goal

Deploy the MVP publicly behind Nginx with SSL and WebSocket support.

### Technologies

- Nginx
- SSL certificates, e.g. Let's Encrypt
- Node.js process manager, e.g. PM2 or systemd
- Linux server
- HTTPS / WSS

### Tasks

- Configure Nginx reverse proxy.
- Enable HTTPS.
- Enable WebSocket upgrade headers.
- Configure Node.js app as background service.
- Set production environment variables.
- Restrict GameMaster URL.
- Add basic log rotation.
- Test reconnect behavior over public internet.
- Test mobile browser compatibility.

### Deliverables

- Publicly reachable HTTPS application.
- WebSocket communication works through Nginx.
- Server can recover from restart.
- MVP is ready for real party testing.

### Related Requirements

- SYS-003
- SYS-004
- SEC-001
- UX-001
- UX-002

---

## MVP Completion Criteria

The MVP is complete when:

- the GameMaster can prepare a session
- players can join without accounts
- lobby codes persist through localStorage
- the GameMaster can associate lobby codes with identities
- assignments are generated correctly
- each player sees only their own mission
- kills are validated server-side
- eliminated players receive a death screen
- target inheritance works reliably
- the game ends automatically
- all participants see the final leaderboard
- game state survives server restart
- WebSockets work behind Nginx over SSL

At this point, the system is technically ready for the first real-world party test.
