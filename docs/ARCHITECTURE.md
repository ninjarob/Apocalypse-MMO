# Apocalypse MMO Architecture

## Overview

The Apocalypse MMO is built as a distributed system with clear separation between client and server responsibilities.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Apocalypse MMO System                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────┐              ┌──────────────────┐    │
│  │  Unreal Client   │◄────────────►│  Game Server     │    │
│  │  (UE5)           │   WebSocket  │  (Node.js/C++)   │    │
│  │                  │   or Native  │                  │    │
│  │  - Rendering     │              │  - Game Logic    │    │
│  │  - Input         │              │  - Authority     │    │
│  │  - Prediction    │              │  - Validation    │    │
│  │  - UI/UX         │              │  - Persistence   │    │
│  └──────────────────┘              └─────────┬────────┘    │
│                                              │              │
│                                    ┌─────────▼────────┐    │
│                                    │   Database       │    │
│                                    │   (SQLite/PG)    │    │
│                                    │                  │    │
│                                    │  - Characters    │    │
│                                    │  - World State   │    │
│                                    │  - Items         │    │
│                                    └──────────────────┘    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

## Server Architecture

### Authoritative Server Model
- Server has final say on all game state
- Client sends inputs, server validates and broadcasts results
- Prevents cheating and ensures consistency

### Core Systems
1. **Connection Management**
   - Handle client connections/disconnections
   - Session management
   - Authentication

2. **Game Loop**
   - Tick-based updates (e.g., 20Hz)
   - Process player actions
   - Update world state
   - AI/NPC updates

3. **Data Persistence**
   - Character saves
   - World state
   - Item storage
   - Quest progress

4. **Network Protocol**
   - Binary or JSON-based messages
   - Event-driven architecture
   - State synchronization

## Client Architecture

### Unreal Engine Structure
1. **Game Mode & Game State**
   - Network-replicated game rules
   - Global game state

2. **Player Controller**
   - Input handling
   - Camera control
   - UI management

3. **Character System**
   - Movement (client prediction + server reconciliation)
   - Animation
   - Equipment/appearance

4. **World Rendering**
   - 3D environment from MUD room data
   - Dynamic loading/streaming
   - LOD and optimization

### Client-Side Prediction
- Predict movement locally for responsiveness
- Server authoritative - corrects client when needed
- Interpolation for smooth remote player movement

## Data Flow

### Player Action Example
```
1. Player presses "attack" button
2. Client sends attack command to server
3. Server validates:
   - Is target in range?
   - Does player have required resources?
   - Are cooldowns ready?
4. Server processes attack
5. Server broadcasts results to all affected clients
6. Clients play animations and update UI
```

## Shared Code

### Protocol Definitions
- Message types and structures
- Enums for game constants
- Data validation schemas

### Game Data Structures
- Character stats
- Item definitions
- Skill/ability data
- World/room layouts

## Scalability Considerations

### Current Phase: Single Server
- Good for 50-500 concurrent players
- Simpler to develop and debug
- Monolithic server

### Future: Horizontal Scaling
- Multiple game servers (zones/shards)
- Dedicated auth server
- Database clustering
- Load balancer

## MUD Data Conversion

### Pipeline
```
MUD Text Data → Parser → Structured JSON → Conversion Tools → Game Assets
```

1. **Extraction**: Use crawler to explore MUD
2. **Parsing**: Convert text descriptions to structured data
3. **Mapping**: Map MUD concepts to 3D equivalents
4. **Asset Generation**: Create/assign 3D models
5. **Import**: Load into Unreal and server database

### Key Conversions
- **Rooms** → 3D environments with connections
- **Items** → 3D models with stats
- **NPCs** → Character models with AI behaviors
- **Combat** → Real-time 3D combat system
- **Skills** → Visual effects and mechanics

## Technology Decisions

### Why Unreal Engine?
- Industry-standard 3D engine
- Excellent networking built-in
- Blueprint visual scripting + C++
- Strong multiplayer support
- Asset marketplace

### Why Node.js for Server?
- Rapid development
- JavaScript/TypeScript familiarity
- Good for I/O heavy operations
- Easy integration with existing tools
- Can migrate to C++ if performance needed

### Why SQLite Initially?
- Simple setup, no separate DB server
- Good performance for single server
- Easy to migrate to PostgreSQL later

## Security Considerations

- Never trust client input
- Server-side validation for all actions
- Encrypted connections (WSS/TLS)
- Rate limiting to prevent abuse
- Anti-cheat measures (speed hacks, teleport detection)

## Development Phases

### Phase 1: Proof of Concept
- Single room in 3D
- Basic character movement
- Client-server connection
- Simple interaction (e.g., pickup item)

### Phase 2: Core Systems
- Multiple rooms/zone
- Combat system
- Inventory management
- Character progression

### Phase 3: Content
- More zones from MUD data
- NPCs and quests
- Skills and abilities
- Social features (chat, groups)

### Phase 4: Polish & Scale
- Performance optimization
- UI/UX refinement
- Additional content
- Scalability improvements
