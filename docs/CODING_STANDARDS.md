# Coding Standards

## TypeScript Style Guide

### General Principles
- **Explicit over implicit** - Use type annotations
- **Immutable by default** - Use `const` and `readonly`
- **Functional where sensible** - Pure functions for logic
- **Small, focused functions** - Each does one thing well
- **Testable** - Design for testability from the start

## Formatting

### Indentation & Spacing
```typescript
// 2 spaces for indentation
class Player {
  constructor(
    public readonly id: string,
    public name: string
  ) {}
}

// Space after keywords
if (condition) {
  doSomething();
}

// Space around operators
const result = a + b * c;

// No space before function params
function calculate(x: number, y: number): number {
  return x + y;
}
```

### Line Length
- **Max 100 characters** per line
- Break long lines at logical points

### Imports
```typescript
// Group imports: stdlib, external, internal
import { EventEmitter } from 'events';
import express from 'express';
import { GameWorld } from './game/GameWorld';
import { Player } from './game/entities/Player';

// Sort alphabetically within groups
import { ItemAPI, NpcAPI, EventAPI } from './modding/ModAPI';
```

## Naming Conventions

### Variables & Functions
```typescript
// camelCase for variables and functions
const playerHealth = 100;
function calculateDamage() { }

// Meaningful names (avoid abbreviations)
const playerExperience = 0; // Good
const plyrExp = 0;          // Bad

// Boolean variables: is/has/can prefix
const isAlive = true;
const hasInventory = true;
const canAttack = false;
```

### Classes & Interfaces
```typescript
// PascalCase for classes and interfaces
class GameWorld { }
interface PlayerData { }

// Interfaces: descriptive noun
interface DatabaseConnection { }
interface ModAPI { }

// No 'I' prefix for interfaces (not IPlayerData)
```

### Constants
```typescript
// UPPER_SNAKE_CASE for constants
const MAX_PLAYERS = 100;
const DEFAULT_TICK_RATE = 20;
const API_VERSION = '1.0.0';

// Enums: PascalCase for name, UPPER_SNAKE_CASE for values
enum MessageType {
  PLAYER_JOIN = 'PLAYER_JOIN',
  PLAYER_LEAVE = 'PLAYER_LEAVE',
  CHAT_MESSAGE = 'CHAT_MESSAGE'
}
```

### Files & Folders
```typescript
// Files: Match the main export (PascalCase for classes)
GameWorld.ts        // exports class GameWorld
PlayerController.ts // exports class PlayerController
utils.ts            // exports utility functions (camelCase)

// Folders: camelCase or kebab-case
game/
network/
mod-loader/
```

## Types & Interfaces

### Always Use Types
```typescript
// Good: Explicit types
function greet(name: string): string {
  return `Hello, ${name}`;
}

// Bad: Implicit 'any'
function greet(name) {
  return `Hello, ${name}`;
}
```

### Prefer Interfaces for Objects
```typescript
// Use interface for object shapes
interface Player {
  id: string;
  name: string;
  health: number;
}

// Use type for unions, intersections, utilities
type Result = Success | Failure;
type ReadonlyPlayer = Readonly<Player>;
```

### Avoid 'any'
```typescript
// Bad
function process(data: any) { }

// Good: Use unknown and narrow
function process(data: unknown) {
  if (typeof data === 'string') {
    // data is string here
  }
}

// Or use generics
function process<T>(data: T): T {
  return data;
}
```

## Functions

### Small & Focused
```typescript
// Good: Single responsibility
function calculateDamage(weapon: Weapon, target: Entity): number {
  const baseDamage = weapon.damage;
  const defense = target.defense;
  return Math.max(0, baseDamage - defense);
}

// Bad: Too many responsibilities
function handleCombat(attacker, target) {
  // 50 lines of mixed concerns
}
```

### Pure Functions
```typescript
// Good: Pure (no side effects)
function add(a: number, b: number): number {
  return a + b;
}

// Acceptable: Side effects clearly named
function savePlayerToDatabase(player: Player): Promise<void> {
  // Side effect is obvious from name
}
```

### Parameter Order
```typescript
// Required params first, optional last
function createPlayer(
  id: string,
  name: string,
  level: number = 1,
  class?: CharacterClass
): Player {
  // ...
}
```

## Classes

### Structure
```typescript
class Player {
  // 1. Static properties
  static readonly MAX_LEVEL = 100;

  // 2. Instance properties
  private _health: number;
  public readonly id: string;
  
  // 3. Constructor
  constructor(id: string, health: number) {
    this.id = id;
    this._health = health;
  }

  // 4. Getters/setters
  get health(): number {
    return this._health;
  }

  // 5. Public methods
  public takeDamage(amount: number): void {
    this._health = Math.max(0, this._health - amount);
  }

  // 6. Private methods
  private calculateDefense(): number {
    return 10;
  }
}
```

### Composition Over Inheritance
```typescript
// Prefer composition
class Player {
  constructor(
    private inventory: Inventory,
    private stats: CharacterStats
  ) {}
}

// Avoid deep inheritance hierarchies
// Bad: Character -> Player -> WarriorPlayer -> EliteWarriorPlayer
```

## Error Handling

### Use Typed Errors
```typescript
class GameError extends Error {
  constructor(
    message: string,
    public readonly code: string
  ) {
    super(message);
    this.name = 'GameError';
  }
}

// Specific error types
class InvalidMoveError extends GameError {
  constructor(message: string) {
    super(message, 'INVALID_MOVE');
  }
}
```

### Async Error Handling
```typescript
// Always handle promise rejections
async function loadPlayer(id: string): Promise<Player> {
  try {
    const data = await database.getPlayer(id);
    return new Player(data);
  } catch (error) {
    logger.error('Failed to load player', { id, error });
    throw new GameError('Player not found', 'PLAYER_NOT_FOUND');
  }
}
```

## Comments

### When to Comment
```typescript
// Good: Explain WHY, not WHAT
// Use exponential backoff to avoid overwhelming the database
// during connection storms
const retryDelay = baseDelay * Math.pow(2, attemptCount);

// Bad: Obvious comments
// Increment counter by 1
counter++;
```

### JSDoc for Public APIs
```typescript
/**
 * Calculates damage dealt to a target based on weapon and defense.
 * 
 * @param weapon - The attacking weapon
 * @param target - The entity being attacked
 * @returns The final damage amount (always >= 0)
 */
function calculateDamage(weapon: Weapon, target: Entity): number {
  // Implementation
}
```

## Async/Await

### Prefer async/await over .then()
```typescript
// Good
async function loadGameData() {
  const items = await loadItems();
  const npcs = await loadNPCs();
  return { items, npcs };
}

// Avoid
function loadGameData() {
  return loadItems().then(items => {
    return loadNPCs().then(npcs => {
      return { items, npcs };
    });
  });
}
```

### Parallel Async Operations
```typescript
// Good: Parallel when possible
const [items, npcs, quests] = await Promise.all([
  loadItems(),
  loadNPCs(),
  loadQuests()
]);

// Bad: Sequential when not necessary
const items = await loadItems();
const npcs = await loadNPCs();    // Waits for items unnecessarily
const quests = await loadQuests(); // Waits for npcs unnecessarily
```

## Data-Driven Patterns

### Load from JSON
```typescript
// Good: Load from data files
const items = loadJSON<ItemDefinition[]>('data/items.json');

// Bad: Hardcoded in code
const items = [
  { id: 'sword', damage: 10 },
  { id: 'axe', damage: 15 }
];
```

### Validate Data
```typescript
interface ItemDefinition {
  id: string;
  name: string;
  damage: number;
}

// Validate at load time
function loadItems(): ItemDefinition[] {
  const data = loadJSON('data/items.json');
  validateSchema(data, itemSchema);
  return data;
}
```

## Testing

### Test File Naming
```typescript
// Place tests next to source or in __tests__
GameWorld.ts
GameWorld.test.ts

// Or
src/game/GameWorld.ts
src/game/__tests__/GameWorld.test.ts
```

### Test Structure
```typescript
describe('GameWorld', () => {
  // Setup
  let world: GameWorld;

  beforeEach(() => {
    world = new GameWorld();
  });

  // Test naming: should + behavior
  it('should spawn player at starting position', () => {
    const player = world.spawnPlayer('test');
    expect(player.position).toEqual({ x: 0, y: 0, z: 0 });
  });

  it('should emit playerJoin event when player spawns', () => {
    const handler = jest.fn();
    world.on('playerJoin', handler);
    
    world.spawnPlayer('test');
    
    expect(handler).toHaveBeenCalledWith(
      expect.objectContaining({ name: 'test' })
    );
  });
});
```

## Mod API Patterns

### Stable Interface
```typescript
// Good: Version the API, keep stable
interface ModAPI_v1 {
  readonly version: '1.0.0';
  items: ItemAPI;
  npcs: NpcAPI;
}

// Bad: Changing frequently breaks mods
```

### Defensive Programming
```typescript
// Validate mod input
class ItemAPI {
  register(item: unknown): void {
    // Validate before using
    if (!isValidItemDefinition(item)) {
      throw new Error('Invalid item definition');
    }
    this.items.set(item.id, item);
  }
}
```

## File Organization

```
src/
├── server.ts              # Entry point
├── game/                  # Game logic
│   ├── GameWorld.ts
│   ├── GameLoop.ts
│   ├── entities/
│   │   ├── Entity.ts      # Base class
│   │   ├── Player.ts
│   │   └── NPC.ts
│   └── systems/
│       ├── CombatSystem.ts
│       ├── MovementSystem.ts
│       └── InventorySystem.ts
├── network/               # Networking
│   ├── ConnectionManager.ts
│   ├── MessageHandler.ts
│   └── protocol/
├── modding/               # Mod system
│   ├── ModLoader.ts
│   ├── ModAPI.ts
│   └── Sandbox.ts
├── database/              # Persistence
│   ├── Database.ts
│   └── models/
└── utils/                 # Shared utilities
    ├── logger.ts
    └── validators.ts
```

## Common Mistakes to Avoid

### 1. Mutating Parameters
```typescript
// Bad
function updatePlayer(player: Player) {
  player.health = 100; // Mutates input
}

// Good
function updatePlayer(player: Player): Player {
  return { ...player, health: 100 }; // Returns new object
}
```

### 2. Not Handling Errors
```typescript
// Bad
async function loadData() {
  const data = await fetch('/api/data');
  return data.json(); // What if fetch fails?
}

// Good
async function loadData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    logger.error('Failed to load data', error);
    throw error;
  }
}
```

### 3. Using 'any'
```typescript
// Bad
function process(data: any) { }

// Good
function process(data: unknown) {
  if (isValidData(data)) {
    // Type is narrowed here
  }
}
```

### 4. Ignoring TypeScript Errors
```typescript
// Bad
const player = getPlayer(id) as Player; // What if null?

// Good
const player = getPlayer(id);
if (!player) {
  throw new Error(`Player ${id} not found`);
}
```

## Editor Configuration

### .editorconfig
```ini
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false
```

### tsconfig.json
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

## Resources

- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Clean Code JavaScript](https://github.com/ryanmcdermott/clean-code-javascript)

---

**Remember:** Consistency matters more than personal preference. Follow these standards for maintainable code.
