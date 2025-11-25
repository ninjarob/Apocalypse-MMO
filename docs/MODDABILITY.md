# Moddability Architecture

## Overview

Apocalypse MMO is designed from the ground up to be highly moddable. The modding system allows players to extend and customize the game through JavaScript mods on the server and Blueprints/data files on the client.

## Design Philosophy

### Core Principles
1. **Easy to Start, Powerful When Needed** - Simple JSON edits for beginners, full API for advanced modders
2. **Safe by Default** - Mods run in sandboxed environments with resource limits
3. **Hot-Reload** - Changes take effect without restarting (in development mode)
4. **Data-Driven** - Game content defined in JSON/CSV, not hardcoded
5. **Well-Documented** - Comprehensive API docs and example mods

## Server Moddability (Node.js)

### Mod Structure

```
Server/
├── core/                   # Core game engine (not moddable)
├── api/                    # Mod API
│   ├── ModLoader.ts
│   ├── ModAPI.ts
│   └── Sandbox.ts
├── data/                   # Game data (moddable)
│   ├── items.json
│   ├── npcs.json
│   ├── quests.json
│   └── zones.json
└── mods/                   # User mods
    ├── example-mod/
    │   ├── mod.json        # Mod metadata
    │   ├── index.js        # Main mod file
    │   └── data/           # Mod-specific data
    ├── custom-items/
    └── new-quests/
```

### Mod Metadata (mod.json)

```json
{
  "id": "custom-items",
  "name": "Custom Items Pack",
  "version": "1.0.0",
  "author": "PlayerName",
  "description": "Adds 50 new unique items",
  "dependencies": [],
  "entry": "index.js",
  "permissions": ["items", "crafting"]
}
```

### Mod API

Mods receive an API object with safe methods:

```javascript
// mods/custom-items/index.js
module.exports = function(api) {
  // Register items
  api.items.register({
    id: 'fire_sword',
    name: 'Flaming Sword',
    type: 'weapon',
    damage: 50,
    effects: ['burn']
  });

  // Register custom command
  api.commands.register('summon', (player, args) => {
    const itemId = args[0];
    player.inventory.add(itemId);
    player.sendMessage(`Summoned ${itemId}`);
  });

  // Hook into events
  api.events.on('playerKill', (killer, victim) => {
    if (killer.equipment.weapon?.id === 'fire_sword') {
      killer.stats.addExperience(100); // Bonus XP
    }
  });

  // Custom NPC behavior
  api.npcs.registerBehavior('vendor', {
    onInteract: (npc, player) => {
      player.ui.openShop(npc.inventory);
    }
  });
};
```

### API Surface

**Items API:**
- `items.register(itemDef)` - Register new item
- `items.get(id)` - Get item definition
- `items.modify(id, changes)` - Modify existing item

**NPCs API:**
- `npcs.spawn(type, position)` - Spawn NPC
- `npcs.registerBehavior(name, behavior)` - Custom AI
- `npcs.registerDialogue(npcId, dialogue)` - Add dialogue

**Commands API:**
- `commands.register(name, handler)` - Add command
- `commands.unregister(name)` - Remove command

**Events API:**
- `events.on(eventName, handler)` - Listen to event
- `events.emit(eventName, data)` - Trigger event

**Player API:**
- `player.sendMessage(text)` - Send message
- `player.teleport(position)` - Teleport player
- `player.inventory.add/remove(item)` - Modify inventory
- `player.stats.*` - Access/modify stats

**World API:**
- `world.getZone(id)` - Get zone data
- `world.spawnEntity(type, position)` - Spawn entity
- `world.setWeather(weather)` - Change weather

**Database API (limited):**
- `db.get(key)` - Get mod data
- `db.set(key, value)` - Store mod data
- Isolated storage per mod

### Sandboxing

Mods run in a restricted context:

```javascript
// ModLoader.ts
class ModLoader {
  loadMod(modPath: string) {
    const sandbox = {
      // Safe globals
      console: createSafeConsole(),
      setTimeout, setInterval, clearTimeout, clearInterval,
      
      // No access to:
      // - require() (except whitelisted modules)
      // - process
      // - fs
      // - network APIs
      
      // Mod API
      api: this.createModAPI()
    };

    const code = fs.readFileSync(modPath, 'utf-8');
    const vm = require('vm');
    vm.runInNewContext(code, sandbox, {
      timeout: 5000,
      displayErrors: true
    });
  }
}
```

### Resource Limits

- **Memory:** 50MB per mod
- **CPU Time:** Max 100ms per tick
- **File Size:** Max 10MB total per mod
- **Event Handlers:** Max 50 registered listeners per event

### Hot-Reload

Development mode supports hot-reload:

```javascript
// Watch for changes
fs.watch('mods/', (event, filename) => {
  if (event === 'change') {
    modLoader.reloadMod(filename);
    console.log(`Reloaded mod: ${filename}`);
  }
});
```

## Client Moddability (Unreal Engine)

### Mod Types

1. **Data Mods** - JSON/CSV overrides (easiest)
2. **Blueprint Mods** - Custom Blueprints (intermediate)
3. **Content Packs** - Models, textures, maps (advanced)
4. **C++ Plugins** - Engine extensions (expert)

### Data Mods

Players edit JSON files to modify items, NPCs, etc:

```json
// Client/Mods/BalanceTweaks/items.json
{
  "fire_sword": {
    "damage": 60,  // Buffed from 50
    "effects": ["burn", "light"]  // Added light effect
  }
}
```

Client loads mods on startup and overrides default data.

### Blueprint Mods

**Moddable Systems via Blueprints:**
- Custom items (Blueprint actor classes)
- Custom abilities (Blueprint function libraries)
- UI modifications (Widget blueprints)
- Quest logic (Blueprint interfaces)

**Example: Custom Item Blueprint**
```
BP_CustomSword (Blueprint Actor)
├─ Parent Class: BP_BaseItem
├─ Variables:
│  ├─ Damage: 75
│  ├─ EffectOnHit: BP_FireEffect
│  └─ Model: StaticMesh_CustomSword
└─ Functions:
   └─ OnEquip() - Override to add glow effect
```

### Content Packs

Mods can package as `.pak` files:

```
ContentPack_MedievalWeapons/
├── Models/
│   ├── Sword_01.uasset
│   ├── Sword_02.uasset
│   └── Shield_01.uasset
├── Textures/
│   └── T_Sword_Diffuse.uasset
└── Blueprints/
    └── BP_MedievalSword.uasset
```

**Packaging:**
```bash
# Unreal's UnrealPak tool
UnrealPak.exe ContentPack.pak -create=FileList.txt
```

**Loading:**
```cpp
// C++: Load .pak at runtime
FPakPlatformFile::Mount("Mods/ContentPack.pak");
```

### Mod Manager UI

In-game menu for managing mods:

```
Mod Manager
├─ Installed Mods
│  ├─ [✓] Custom Items Pack (Server)
│  ├─ [✓] Balance Tweaks (Client)
│  └─ [ ] Medieval Weapons (Client)
├─ Available Mods (Workshop)
│  └─ Browse, Download, Install
└─ Settings
   ├─ Load Order
   └─ Auto-Update
```

## Data-Driven Design

### Item Definition Example

**Shared Format (used by both server and client):**

```json
{
  "id": "fire_sword",
  "name": "Flaming Sword",
  "type": "weapon",
  "rarity": "legendary",
  "stats": {
    "damage": 50,
    "attackSpeed": 1.2,
    "critChance": 0.15
  },
  "requirements": {
    "level": 10,
    "strength": 15
  },
  "effects": [
    {
      "type": "burn",
      "damagePerSecond": 5,
      "duration": 3
    }
  ],
  "client": {
    "model": "Weapons/Sword_Fire",
    "icon": "Icons/Items/fire_sword",
    "glowColor": [255, 100, 0],
    "sounds": {
      "equip": "Audio/Items/sword_equip",
      "attack": "Audio/Items/sword_swing_fire"
    }
  }
}
```

**Server uses:** `stats`, `requirements`, `effects`  
**Client uses:** `client.*` for visuals

### Loading Data

```typescript
// Server
const items = JSON.parse(fs.readFileSync('data/items.json', 'utf-8'));
const modItems = loadModData('mods/*/data/items.json');
const allItems = mergeDeep(items, modItems); // Mods override

// Client (Unreal)
// Data Table imported from JSON
UDataTable* ItemsTable = LoadObject<UDataTable>("DataTables/DT_Items");
```

## Mod Distribution

### Workshop Integration

**Steam Workshop (future):**
- Upload mods via Steamworks API
- Subscribe to mods in-game
- Automatic updates
- Rating and comments

**Manual Installation:**
```
Server: Copy to Server/mods/
Client: Copy to Client/Mods/ or install via Mod Manager
```

### Mod Format

**.zip structure:**
```
custom-items-v1.0.zip
├── mod.json              # Metadata
├── README.md             # Mod description
├── index.js              # Server mod
├── data/
│   ├── items.json        # Server data
│   └── Items.pak         # Client assets (optional)
└── screenshots/          # For workshop
```

## Security Considerations

### Server Mods

**Threats:**
- Malicious code execution
- Resource exhaustion (DoS)
- Data theft
- Cheating

**Mitigations:**
1. **VM Sandbox** - No access to file system, network, or Node.js internals
2. **Resource Limits** - Memory, CPU time, event listener caps
3. **Permission System** - Mods declare required permissions
4. **Code Review** - Workshop mods reviewed before approval
5. **Signatures** - Verify mod hasn't been tampered with

### Client Mods

**Threats:**
- Malicious code in Blueprints (limited)
- Cheating (aimbots, wallhacks)
- Inappropriate content

**Mitigations:**
1. **Server Authority** - Server validates all game actions
2. **Anti-Cheat** - Detect client modifications
3. **Content Filtering** - Report inappropriate mods
4. **Sandboxed Assets** - Limit what mods can access

## Development Tools

### Mod Creator Tool

GUI tool for creating mods:

```
Mod Creator
├─ New Mod Wizard
│  ├─ Mod Name/ID
│  ├─ Select template (Items, Quests, NPCs)
│  └─ Generate boilerplate
├─ Item Editor
│  ├─ Visual editor for item properties
│  └─ Preview in 3D
├─ Quest Editor
│  └─ Visual quest flow editor
└─ Package & Export
   └─ Creates .zip for distribution
```

### Testing Environment

**Mod Debugging:**
```javascript
// Enable debug mode
api.debug.enable();

// Logs to console with mod name prefix
api.log('Fire sword equipped'); 
// Output: [CustomItems] Fire sword equipped

// Breakpoints (in development)
api.debug.breakpoint();
```

## Community & Ecosystem

### Documentation Site

- Getting Started guides
- API reference (auto-generated from code)
- Tutorials (beginner to advanced)
- Best practices
- FAQ

### Example Mods

Ship with example mods:
- `example-item` - Adds a single item
- `example-command` - Adds a custom command
- `example-npc` - Custom NPC behavior
- `example-quest` - Simple quest chain

### Mod Template Repository

GitHub repo with templates:
```
mod-templates/
├── item-mod/
├── quest-mod/
├── npc-mod/
├── zone-mod/
└── system-mod/
```

Clone and customize.

## Roadmap

**Phase 1: Foundation**
- [x] Design mod architecture
- [ ] Implement ModLoader
- [ ] Create basic API (items, commands)
- [ ] Simple example mods

**Phase 2: Core Features**
- [ ] Event system
- [ ] Hot-reload in development
- [ ] Sandboxing and security
- [ ] Client data override system

**Phase 3: Advanced**
- [ ] Blueprint mod support
- [ ] Content pack loading
- [ ] Mod Manager UI
- [ ] Workshop integration

**Phase 4: Polish**
- [ ] Comprehensive documentation
- [ ] Mod Creator tool
- [ ] Community hub
- [ ] Modding contests/showcase

## Best Practices for Modders

1. **Start Simple** - One feature at a time
2. **Test Thoroughly** - Test with and without other mods
3. **Version Control** - Use git for your mod
4. **Document** - README with features, installation, config
5. **Be Compatible** - Avoid overriding core systems unnecessarily
6. **Ask for Help** - Active community on Discord

## Conclusion

By prioritizing moddability from day one, Apocalypse MMO will have:
- **Longevity** - Community keeps creating content
- **Engagement** - Players invested in the ecosystem
- **Innovation** - Community ideas we never imagined
- **Testing Ground** - Popular mods become official features

This makes the game more than just a game—it becomes a platform for creativity.
