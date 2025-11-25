# Project Glossary

Quick reference for terminology used throughout the project.

## General Terms

**MUD (Multi-User Dungeon)**
- Text-based multiplayer game
- The original Apocalypse VI that we're converting

**MMO (Massively Multiplayer Online)**
- 3D game supporting many concurrent players
- What we're building

**Mod**
- User-created extension or modification to the game
- Can be JavaScript (server) or Blueprints/assets (client)

**Data-Driven**
- Game content defined in JSON/CSV files, not hardcoded
- Allows modders to change content without coding

## Architecture Terms

**Server Authority**
- Server has final say on all game state
- Prevents cheating, ensures consistency

**Client Prediction**
- Client guesses what will happen before server responds
- Makes movement feel responsive despite network latency

**Server Reconciliation**
- Server corrects client predictions when they're wrong
- Maintains authoritative state

**Tick**
- One iteration of the game loop
- Default: 20 ticks per second (50ms per tick)

**Hot-Reload**
- Reload code/data without restarting server
- Useful for development iteration

**WebSocket**
- Protocol for real-time bidirectional communication
- Used for client-server connection

## Game Systems

**Entity**
- Any object in the game world (player, NPC, item)
- Base class for all interactive objects

**Component**
- Modular piece of functionality attached to entities
- Examples: HealthComponent, InventoryComponent

**System**
- Processes entities with specific components
- Examples: CombatSystem, MovementSystem

**World/Zone**
- A game area (map, dungeon, city)
- Contains entities and environmental data

**Inventory**
- Player's collection of items
- Includes equipped items and backpack

**Quest**
- Task or mission for players to complete
- Has objectives, rewards, and story

## Modding Terms

**Mod API**
- Interface exposed to mods
- Provides safe methods to extend game

**Sandbox**
- Restricted execution environment for mods
- Prevents malicious or buggy code from breaking game

**Mod Loader**
- System that loads and initializes mods
- Manages mod lifecycle and dependencies

**Hook**
- Point in code where mods can inject behavior
- Example: `onPlayerJoin` event

**Data Override**
- Mod replaces default game data
- Example: Changing an item's stats

**Permission**
- What a mod is allowed to access
- Examples: 'items', 'npcs', 'commands'

## Network Protocol

**Message Type**
- Category of network message
- Examples: CONNECT, MOVE, CHAT, ERROR

**Packet**
- Unit of data sent over network
- Contains message type and payload

**Latency**
- Time delay between client and server
- Measured in milliseconds (ms)

**Bandwidth**
- Amount of data that can be sent per second
- Important for scalability

**State Sync**
- Keeping client and server game state in sync
- Challenge of networked games

## Data Terms

**JSON (JavaScript Object Notation)**
- Human-readable data format
- Used for all game data files

**Schema**
- Definition of data structure
- Used to validate JSON files

**Serialization**
- Converting objects to transmittable format
- Example: Player object → JSON string

**Deserialization**
- Converting received data back to objects
- Example: JSON string → Player object

## Unreal Engine Terms

**Blueprint**
- Visual scripting system in Unreal
- Alternative to C++ for game logic

**Actor**
- Base class for objects in Unreal
- Anything that can be placed in a level

**Pawn**
- Actor that can be controlled
- Player characters are pawns

**Controller**
- Handles input and controls pawns
- PlayerController for players, AIController for NPCs

**Widget**
- UI element in Unreal
- Built with UMG (Unreal Motion Graphics)

**Asset**
- Game content file (model, texture, sound)
- Stored in Content Browser

**.pak File**
- Packaged collection of assets
- Used for mods and distribution

## Development Terms

**Repository (Repo)**
- Git project containing code
- We have 5 repos for this project

**Commit**
- Saved snapshot of code changes
- Has message describing what changed

**Branch**
- Parallel version of code
- Example: `main`, `feature/combat-system`

**Pull Request (PR)**
- Request to merge code changes
- Includes code review

**CI/CD (Continuous Integration/Deployment)**
- Automated testing and deployment
- Future: Auto-deploy on commit

**TypeScript**
- JavaScript with type checking
- Used for server code

**Node.js**
- JavaScript runtime for server
- Executes our TypeScript (compiled to JS)

## Testing Terms

**Unit Test**
- Test of a single function/class
- Fast, isolated, many of them

**Integration Test**
- Test of multiple systems working together
- Example: Combat system + inventory

**Mock**
- Fake version of dependency for testing
- Example: Mock database for testing without real DB

**Stub**
- Simplified implementation for testing
- Returns predetermined responses

## Performance Terms

**FPS (Frames Per Second)**
- How often screen updates
- Target: 60 FPS on client

**TPS (Ticks Per Second)**
- How often server updates game state
- Default: 20 TPS (50ms tick)

**Optimization**
- Making code run faster or use less resources
- "Premature optimization is the root of all evil"

**Profiling**
- Measuring where code spends time
- Identifies bottlenecks

**Memory Leak**
- Code that doesn't free unused memory
- Eventually causes crash

## Database Terms

**SQLite**
- Lightweight file-based database
- Used for development

**PostgreSQL**
- Full-featured database server
- Used for production

**Query**
- Request for data from database
- Example: "Get player with ID 123"

**Schema**
- Database structure definition
- Tables, columns, relationships

**Migration**
- Script to update database structure
- Allows versioned database changes

## Version Control Terms

**Git**
- Version control system
- Tracks all code changes

**GitHub**
- Hosting service for Git repos
- Where our code lives

**Clone**
- Download a copy of repo
- `git clone <url>`

**Push**
- Upload local changes to GitHub
- `git push`

**Pull**
- Download changes from GitHub
- `git pull`

**Merge**
- Combine changes from different branches
- Can have conflicts

## Apocalypse VI Specific

**Zone**
- Area in the MUD (village, dungeon, etc.)
- Being converted to 3D environments

**Room**
- Single location in MUD
- Has description, exits, NPCs, items

**Exits**
- Connections between rooms
- Example: north, south, east, west

**Crawler**
- Autonomous bot that explores MUD
- Collects data for conversion

## Project-Specific Abbreviations

**APOC** - Apocalypse MMO (our project)  
**UE5** - Unreal Engine 5  
**TS** - TypeScript  
**WS** - WebSocket  
**API** - Application Programming Interface  
**DB** - Database  
**UI** - User Interface  
**UX** - User Experience  
**NPC** - Non-Player Character  
**XP** - Experience Points  
**HP** - Health Points  
**DPS** - Damage Per Second  

## Common Acronyms

**MVP** - Minimum Viable Product (simplest working version)  
**POC** - Proof of Concept (experimental prototype)  
**CRUD** - Create, Read, Update, Delete (database operations)  
**REST** - REpresentational State Transfer (API style, not using this)  
**JSON** - JavaScript Object Notation  
**CSV** - Comma-Separated Values  
**LOD** - Level of Detail (optimization technique)  
**AI** - Artificial Intelligence  

---

**Note:** This glossary will expand as the project grows. When introducing new terminology, add it here!
