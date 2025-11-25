# GitHub Copilot Instructions for Apocalypse MMO

## 🎮 Project Context

Converting Apocalypse VI text-based MUD to a 3D MMO.

**Primary Reference:** `docs/AI_DEV_GUIDE.md` - Read this first for full context!

## 🔑 Key Architectural Decisions

1. **Server:** Node.js + TypeScript (NOT C++)
2. **Client:** Unreal Engine 5 (C++ + Blueprints)
3. **Design Philosophy:** Data-driven, moddability-first
4. **Mod System:** JavaScript mods with sandboxing

## 📋 Core Principles

- **Data-Driven:** All content in JSON (items, NPCs, quests, zones)
- **Moddable:** Clean API, event hooks, hot-reload
- **Server Authority:** Server validates everything
- **Type-Safe:** Explicit TypeScript types, no 'any'

## 🚫 Do NOT Suggest

- ❌ C++ for server (we chose Node.js)
- ❌ Hardcoded game content (use JSON)
- ❌ REST API (we use WebSocket)
- ❌ Breaking Mod API without versioning
- ❌ Ignoring server authority

## 📚 Documentation

Before working on any task, check relevant docs:
- `docs/AI_DEV_GUIDE.md` - Full development context
- `docs/CODING_STANDARDS.md` - TypeScript style guide
- `docs/ARCHITECTURE.md` - System design
- `docs/MODDABILITY.md` - Mod system design
- `docs/GLOSSARY.md` - Project terminology

## 🎯 Current Phase

**Phase:** 0 - Planning & Setup ✅  
**Next:** Phase 1 - Proof of Concept (basic networking + single room)

## 💻 Repository Structure

- **Apocalypse-MMO** - Documentation hub (this repo)
- **Apocalypse-MMO-Server** - Node.js game server
- **Apocalypse-MMO-Client** - Unreal Engine project (future)
- **Apocalypse-MMO-Shared** - Shared TypeScript types
- **Apocalypse-VI-Web-and-Crawler** - MUD data extraction

## ⚡ Quick Commands

```bash
# Server
cd Apocalypse-MMO-Server
npm install
npm run dev

# Shared
cd Apocalypse-MMO-Shared
npm run build
```

## 🎨 Code Style

Follow `docs/CODING_STANDARDS.md`:
- 2 spaces, camelCase functions, PascalCase classes
- Explicit types, no 'any'
- Pure functions where possible
- Event-driven architecture

## ✅ When Implementing Features

1. Check if data-driven (should it be JSON?)
2. Consider moddability (should mods access this?)
3. Maintain server authority (validate server-side)
4. Follow existing patterns
5. Update relevant documentation

---

**Remember:** This is a moddable MMO being built with Node.js. Always prioritize data-driven design and clean mod APIs!
