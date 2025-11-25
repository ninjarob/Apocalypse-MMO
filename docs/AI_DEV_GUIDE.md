# AI Development Guide

## Overview

This document helps AI assistants (like GitHub Copilot) understand the project context and make better code suggestions. It serves as a quick reference for development sessions.

## Project Summary

**Apocalypse MMO** - Converting a text-based MUD to a 3D MMO
- **Server:** Node.js/TypeScript, WebSocket, event-driven
- **Client:** Unreal Engine 5, C++/Blueprints
- **Key Feature:** Highly moddable with JavaScript mod system
- **Design:** Data-driven (JSON for all content)

## Current Status

**Phase:** 0 - Planning & Setup ✅
**Next Phase:** 1 - Proof of Concept (single room + basic networking)

### What's Done
- ✅ Repository structure
- ✅ Documentation (architecture, roadmap, moddability)
- ✅ Basic server scaffolding
- ✅ Shared protocol definitions

### What's Next
- Implement server game loop
- Create first Unreal project
- Build networking layer
- Test multiplayer locally

## Critical Architectural Decisions

### 1. Node.js for Server (NOT C++)
**Why:** Fast development, easy moddability, adequate performance for <500 players
**Modding:** JavaScript mods loaded at runtime with sandboxing

### 2. Data-Driven Everything
**All game content in JSON:**
- `data/items.json` - Item definitions
- `data/npcs.json` - NPC data
- `data/quests.json` - Quest chains
- `data/zones.json` - World layout

**Never hardcode:**
- Item stats
- NPC behavior parameters
- Quest text
- Zone layouts

### 3. Server Authority
**Server has final say on:**
- All game state
- Combat results
- Inventory changes
- Position validation

**Client does:**
- Rendering
- Input handling
- Prediction (for smoothness)
- UI/UX

### 4. Moddability First
**Design decisions favor:**
- Clean, documented APIs
- Event-driven architecture (easy to hook)
- Separation of concerns
- Hot-reload in development

## Code Style & Patterns

### TypeScript
```typescript
// Prefer explicit types
interface Player {
  id: string;
  name: string;
  position: Vector3;
}

// Use readonly where appropriate
interface GameConfig {
  readonly tickRate: number;
  readonly maxPlayers: number;
}

// Event-driven pattern
class GameWorld extends EventEmitter {
  emit(event: 'playerJoin', player: Player): boolean;
  on(event: 'playerJoin', listener: (player: Player) => void): this;
}

// Dependency injection for testability
class GameLoop {
  constructor(
    private world: GameWorld,
    private network: NetworkManager
  ) {}
}
```

### File Organization
```
One class per file
Name matches class name
Group related files in folders
Index files for clean imports
```

### Naming Conventions
- **Classes:** PascalCase (`GameWorld`, `PlayerController`)
- **Functions:** camelCase (`handlePlayerJoin`, `validateMove`)
- **Constants:** UPPER_SNAKE_CASE (`MAX_PLAYERS`, `TICK_RATE`)
- **Files:** PascalCase for classes, camelCase for modules

## Common Patterns

### Mod API Pattern
```typescript
// Expose safe, stable API to mods
interface ModAPI {
  items: ItemAPI;
  npcs: NpcAPI;
  events: EventAPI;
  commands: CommandAPI;
}

// Implementation keeps internals private
class ModAPIImpl implements ModAPI {
  constructor(private core: GameCore) {
    // Wrap core with safety checks
  }
}
```

### Data Loading Pattern
```typescript
// Load base data
const baseItems = loadJSON<ItemDefinition[]>('data/items.json');

// Load mod data
const modItems = loadModData<ItemDefinition[]>('mods/*/data/items.json');

// Merge (mods override base)
const allItems = mergeDeep(baseItems, modItems);

// Validate
validateSchema(allItems, itemSchema);
```

### Event Pattern
```typescript
// Emit events for moddability
world.on('playerJoin', (player) => {
  logger.info(`Player joined: ${player.name}`);
});

// Always emit after state change
player.inventory.add(item);
world.emit('itemPickup', { player, item });
```

## Technical Constraints

### Performance Targets
- **Server Tick Rate:** 20Hz (50ms per tick)
- **Max Tick Duration:** 40ms (leaves 10ms buffer)
- **Concurrent Players:** 100-500 per instance
- **Client FPS:** 60+ on mid-range PC

### Dependencies
**Server:**
- Keep dependencies minimal
- Prefer native Node.js modules
- Avoid heavy frameworks (we're event-driven, not REST-heavy)

**Client:**
- Use Unreal's built-in systems where possible
- Plugins only when necessary

## Testing Strategy

### Unit Tests
```typescript
// Test pure functions
describe('calculateDamage', () => {
  it('applies weapon damage correctly', () => {
    const damage = calculateDamage(weapon, target);
    expect(damage).toBe(50);
  });
});
```

### Integration Tests
```typescript
// Test system interactions
describe('CombatSystem', () => {
  it('handles player attack', async () => {
    const result = await combat.attack(player, target);
    expect(target.health).toBe(50);
    expect(result.events).toContain('hit');
  });
});
```

### Manual Testing
- Two clients connecting locally
- Test with simulated latency
- Test with multiple mods loaded

## Common Tasks

### Adding a New Item
1. Add to `data/items.json`
2. No code changes needed (data-driven!)
3. Optional: Add 3D model reference for client

### Adding a New System
1. Create system class in `src/systems/`
2. Integrate with game loop
3. Expose API in `ModAPI` if moddable
4. Emit events for hooks
5. Write tests

### Adding a Mod API
1. Define interface in `src/modding/ModAPI.ts`
2. Implement with safety checks
3. Document in `docs/MODDABILITY.md`
4. Create example mod in `mods/example-*`

## Debugging Tips

### Server
```typescript
// Enable debug logging
DEBUG=apocalypse:* npm run dev

// Use VS Code debugger
// Launch config in .vscode/launch.json

// Log mod activity
api.debug.enable();
```

### Network
```bash
# Monitor WebSocket traffic
# Use browser DevTools or Wireshark

# Simulate lag
# Use network emulation in browser DevTools
```

### Mods
```typescript
// Sandbox prevents file access, but logs work
console.log('Mod loaded'); // Works
require('fs'); // Blocked by sandbox
```

## Useful Commands

```bash
# Server
cd Apocalypse-MMO-Server
npm install              # Install dependencies
npm run dev              # Development with hot-reload
npm run build            # Compile TypeScript
npm start                # Production server
npm test                 # Run tests

# Shared library
cd Apocalypse-MMO-Shared
npm run build            # Build types
npm run watch            # Watch mode

# Client (future)
# Open .uproject in Unreal Editor
# Build in Visual Studio
```

## Important Files to Check

When making changes, consider updating:
- `docs/ARCHITECTURE.md` - If architecture changes
- `docs/MODDABILITY.md` - If Mod API changes
- `docs/ROADMAP.md` - If timeline/priorities shift
- Server `README.md` - If server setup changes
- `src/modding/ModAPI.ts` - The mod interface (keep stable!)

## Data Formats

### Item Definition
```json
{
  "id": "fire_sword",
  "name": "Flaming Sword",
  "type": "weapon",
  "stats": { "damage": 50, "attackSpeed": 1.2 },
  "requirements": { "level": 10 },
  "effects": ["burn"],
  "client": { "model": "Weapons/Sword_Fire" }
}
```

### NPC Definition
```json
{
  "id": "guard_captain",
  "name": "Guard Captain",
  "level": 15,
  "health": 500,
  "behavior": "patrol_aggressive",
  "dialogue": "dialogue_guard_01",
  "loot": ["gold_coins", "iron_sword"]
}
```

### Quest Definition
```json
{
  "id": "fetch_tome",
  "name": "Retrieve the Ancient Tome",
  "giver": "librarian_eldric",
  "objectives": [
    { "type": "goto", "location": "old_library" },
    { "type": "collect", "item": "ancient_tome" },
    { "type": "return", "npc": "librarian_eldric" }
  ],
  "rewards": { "xp": 500, "gold": 50 }
}
```

## AI Assistant Guidelines

### When Suggesting Code
1. **Check phase** - Don't implement features from Phase 3 in Phase 1
2. **Follow patterns** - Use existing code patterns in the project
3. **Data-driven** - Suggest JSON configs, not hardcoded values
4. **Moddable** - Consider if modders should be able to change this
5. **Type-safe** - Use TypeScript types properly

### When Answering Questions
1. **Context-aware** - Reference our specific architecture decisions
2. **Practical** - Focus on what helps reach next milestone
3. **Trade-offs** - Explain pros/cons of suggestions
4. **Examples** - Show code examples from this project structure

### Red Flags (What NOT to Suggest)
- ❌ Hardcoding item stats in TypeScript
- ❌ Using C++ for server (we chose Node.js)
- ❌ REST API when we're using WebSocket
- ❌ Complex frameworks (we're keeping it simple)
- ❌ Ignoring server authority (security!)
- ❌ Breaking Mod API without versioning

## Quick Reference Links

- [Architecture](./ARCHITECTURE.md)
- [Roadmap](./ROADMAP.md)
- [Moddability](./MODDABILITY.md)
- [MUD Conversion](./MUD_CONVERSION.md)
- [Unreal Getting Started](./UNREAL_GETTING_STARTED.md)

## Version History

- **v0.1** (Nov 2025) - Initial setup and planning
- More versions as development progresses...

---

**Last Updated:** November 25, 2025  
**Current Phase:** Phase 0 - Planning & Setup
**Next Milestone:** Implement basic server game loop
