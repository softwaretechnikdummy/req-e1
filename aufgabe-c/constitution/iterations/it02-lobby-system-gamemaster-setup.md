# Iteration 02 – Lobby System & GameMaster Setup

## 1. Purpose

This iteration transforms the technical backend foundation into the first playable pre-game experience.

The goal is to allow:

- GameMaster setup of a real party session
- temporary player onboarding without accounts
- persistent lobby-code identities
- real-time lobby synchronization
- manual association between devices and prepared player identities
- reconnect-safe player sessions

This iteration still does not implement the actual assassination gameplay.

---

## 2. Scope

### Included

- GameMaster setup interface
- player name management
- target-word management
- lobby-code generation
- localStorage-based reconnect identity
- player lobby screen
- GameMaster lobby overview
- manual player assignment workflow
- real-time synchronization
- persisted lobby state
- reconnect handling

### Excluded

- assignment graph generation
- gameplay missions
- kill validation
- death handling
- leaderboard
- endgame logic

---

## 3. Related Roadmap Phases

This iteration covers parts of:

- Phase 4 – GameMaster Setup Interface
- Phase 5 – Lobby and Temporary Identity System

---

## 4. Related Requirements

| ID | Requirement |
|---|---|
| GM-001 | Create Party Setup |
| GM-002 | Configure Player List |
| GM-003 | Configure Word List |
| GM-006 | View Waiting Lobby Clients |
| GM-007 | Assign Lobby Codes |
| PL-001 | Temporary Lobby Identity |
| PL-002 | Reconnect Support |
| UX-001 | Mobile Optimized Player Interface |
| UX-002 | Desktop Optimized GM Interface |

# 5. Functional Specification

## 5.1 GameMaster Setup Interface

The system shall provide a desktop-oriented GameMaster interface.

### Requirements

- The GameMaster interface shall be reachable through the secret GameMaster URL.
- The interface shall allow editing:
  - player names
  - target words
- Entries shall be parsed line-by-line.
- The current setup state shall persist automatically.
- Setup data shall survive browser reload and server restart.

### Example Input

```txt
Players:
Anna
Ben
Chris
Daria
```

```txt
Words:
Banana
Airport
Shrek
Dentist
```

---

## 5.2 Validation Rules

### Player Validation

- Minimum player count: 3
- Empty lines shall be ignored.
- Duplicate names should trigger warnings.
- Names shall be trimmed.

### Word Validation

- Minimum word count must match player count.
- Empty lines shall be ignored.
- Words shall be trimmed.

### Invalid State Handling

Invalid setup data shall not overwrite previously valid setup state.

---

## 5.3 Player Lobby Identity

The system shall support temporary anonymous player identities.

### Requirements

- A player opening the frontend shall automatically receive a generated lobby code.
- The lobby code shall be stored in browser localStorage.
- The same device shall reuse the same lobby code after reconnect.
- Lobby codes shall be globally unique within the current session.

### Example Lobby Codes

```txt
A7K2
M4PX
Q91L
```

The codes are not security credentials.
They are temporary session identifiers.

---

## 5.4 Player Lobby Screen

The player frontend shall provide a mobile-friendly waiting screen.

### Requirements

- Display generated lobby code prominently.
- Display connection state:
  - connecting
  - connected
  - disconnected
- Display waiting status until assigned by GameMaster.
- Automatically reconnect after temporary connection loss.
- Automatically restore lobby identity after reload.

### Example States

```txt
Waiting for GameMaster assignment...
```

```txt
Reconnecting...
```

```txt
Connected
```

---

## 5.5 GameMaster Lobby Overview

The GameMaster interface shall display all currently connected lobby clients.

### Requirements

- Display all waiting lobby codes in real time.
- Display connection status.
- Display assigned/unassigned state.
- Display mapped player identity if assigned.
- Update automatically through WebSocket synchronization.

### Example Table

| Lobby Code | Status | Assigned Player |
|---|---|---|
| A7K2 | connected | Anna |
| M4PX | connected | — |
| Q91L | disconnected | Ben |

---

## 5.6 Manual Lobby Assignment

The GameMaster shall manually associate devices with prepared player identities.

### Requirements

- A lobby code can only be assigned once.
- A player identity can only be assigned once.
- Reassignment shall be possible before game start.
- Assignment updates shall synchronize in real time.

### Example Workflow

```txt
Lobby Code "A7K2"
→ assign to
Player "Anna"
```

---

## 5.7 Reconnect Handling

The system shall support reconnect-safe sessions.

### Requirements

- Reconnecting clients shall reclaim their previous lobby identity.
- Previously assigned player identity shall be restored automatically.
- Temporary disconnects shall not remove assignments.
- The GameMaster shall see reconnect state changes in real time.

---

## 5.8 Persistence

The following state shall persist to JSON:

```json
{
  "players": [],
  "words": [],
  "lobbyClients": [],
  "assignments": [],
  "status": "setup"
}
```

### Requirements

- State shall save after every mutation.
- Persisted state shall load on server startup.
- Corrupted JSON shall not silently destroy valid state.

# 6. WebSocket Protocol

## 6.1 Player Messages

### CLIENT_HELLO

```json
{
  "type": "CLIENT_HELLO",
  "payload": {
    "role": "player",
    "lobbyCode": "A7K2"
  }
}
```

---

## 6.2 GameMaster Messages

### GM_ASSIGN_PLAYER

```json
{
  "type": "GM_ASSIGN_PLAYER",
  "payload": {
    "lobbyCode": "A7K2",
    "playerName": "Anna"
  }
}
```

---

## 6.3 Server Broadcasts

### LOBBY_STATE_UPDATE

```json
{
  "type": "LOBBY_STATE_UPDATE",
  "payload": {}
}
```

---

# 7. Non-Functional Requirements

## 7.1 Stability

- Reconnects shall not crash the server.
- Duplicate reconnect attempts shall not corrupt state.
- Invalid assignment requests shall be rejected safely.

---

## 7.2 Security

- Players shall never see:
  - other lobby codes
  - player mappings
  - word lists
  - GameMaster setup state
- The server remains authoritative for all assignments.

---

## 7.3 UX

### Player UX

- optimized for mobile portrait usage
- large readable lobby code
- minimal interaction complexity

### GameMaster UX

- optimized for laptop/desktop
- efficient assignment workflow
- live-updating overview tables

---

# 8. Acceptance Criteria

## AC-01 – GameMaster Setup

Given the GameMaster interface  
When player names and words are entered  
Then the setup state is persisted successfully.

---

## AC-02 – Lobby Code Generation

Given a new player visits the frontend  
When localStorage contains no code  
Then the server generates a new unique lobby code.

---

## AC-03 – Reconnect Persistence

Given a player reconnects  
When the browser reloads  
Then the previous lobby identity is restored.

---

## AC-04 – Lobby Synchronization

Given multiple connected clients  
When lobby state changes  
Then all affected clients receive real-time updates.

---

## AC-05 – Manual Assignment

Given connected lobby clients  
When the GameMaster assigns a player  
Then the assignment persists and synchronizes.

---

## AC-06 – Restart Recovery

Given persisted lobby state exists  
When the server restarts  
Then the session restores successfully.

---

# 9. Suggested File Structure

```txt
/src
├── lobbyManager.js
├── lobbyCodeGenerator.js
├── reconnectHandler.js
├── playerAssignments.js
├── setupValidation.js
├── gmHandlers.js
├── playerHandlers.js
└── statePersistence.js
```

---

# 10. Test Checklist

- [ ] Player receives lobby code.
- [ ] Lobby code persists through reload.
- [ ] Multiple players can connect simultaneously.
- [ ] GameMaster sees waiting clients.
- [ ] Player identities can be assigned.
- [ ] Duplicate assignments are rejected.
- [ ] Reconnect restores assignment.
- [ ] Lobby state survives restart.
- [ ] Invalid messages do not crash server.
- [ ] Mobile player screen remains usable.

---

# 11. Definition of Done

Iteration 02 is complete when a real group of players can:

- join the lobby from their phones
- receive persistent temporary identities
- appear in the GameMaster interface
- be manually mapped to prepared player identities
- reconnect safely without losing state

At this point, the system supports a stable real-world pre-game flow and is ready for assignment graph generation in the next iteration.
