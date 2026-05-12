# techstack.md

# Assassin Party Game – Tech Stack

## 1. Product Type

Assassin Party Game is a public web application for one active real-world party session.

It is not a SaaS platform.
It is not a social network.
It is a focused real-time party game that runs entirely in the browser.

## 2. Backend

### Runtime

- Node.js
- Express
- WebSocket server
- JSON file persistence

The backend owns the canonical game state.

## 3. Frontend

- HTML
- JavaScript
- Bootstrap
- Responsive browser UI

### Player UI

Optimized for mobile browsers.

Players can:

- join the lobby
- receive a lobby code
- view their target
- view their target word
- register kills
- see death screen
- see final leaderboard

### GameMaster UI

Optimized for desktop browsers.

Protected through a secret non-guessable path:

```txt
/gamemaster-doJSAD1281384FDJSD
```

No accounts.
No passwords.
No “Forgot Password?” emails at 3am.

## 4. Communication

### Primary Channel

- WebSockets

Used for:

- lobby updates
- assignment distribution
- kill registration
- graph synchronization
- death events
- leaderboard updates
- game-end broadcasts

### Secondary Channel

- HTTP via Express

Used for:

- static frontend delivery
- initial page loading
- health endpoints

HTTP loads the page.
WebSockets keep the paranoia alive.

## 5. Persistence

No database is used.

Persistent state is stored in JSON files.

### Example Files

```txt
/data/game-state.json
/data/players.json
/data/words.json
/data/events.json
```

The application must recover after restart without losing the active session.

## 6. Deployment

Production architecture:

```txt
Browser Client
      ↓ HTTPS / WSS
Nginx Reverse Proxy
      ↓ HTTP / WS
Node.js Application
      ↓
JSON Persistence
```

Nginx handles:

- SSL termination
- reverse proxy routing
- WebSocket upgrades

## 7. Authentication Model

### Players

- receive generated lobby codes
- codes are stored in localStorage
- reconnect automatically using stored code

### GameMaster

Protected through secret URL access only.

This is not bank-grade security.
This is “prevent drunk economics students from opening admin tools” security.

## 8. Session Model

Exactly one active party session exists at any time.

Benefits:

- simpler architecture
- easier synchronization
- simpler persistence
- easier recovery
- reduced operational complexity

## 9. Design Philosophy

The gameplay may be chaotic.

The interface should not be.

Visual direction:

- clean
- minimal
- reliable
- mobile-friendly
- surprisingly polished

## 10. Technical Principles

- server-authoritative architecture
- atomic kill handling
- no self-targeting
- real-time synchronization
- secure mission visibility
- persistent recoverable state
- automatic game ending
