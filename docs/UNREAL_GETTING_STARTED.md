# Getting Started with Unreal Engine 5

## Installation

### Prerequisites
- **Windows 10/11** (64-bit)
- **16GB RAM minimum** (32GB recommended)
- **GPU:** NVIDIA GTX 1070 or AMD RX Vega 56 (minimum)
- **Storage:** 100GB+ free space (Unreal + assets are large)
- **Visual Studio 2022** (Community Edition is free)

### Steps
1. Download **Epic Games Launcher** from epicgames.com
2. Install **Unreal Engine 5.5** (latest stable version)
3. Install **Visual Studio 2022** with:
   - Desktop development with C++
   - Game development with C++ workload
   - Windows 10/11 SDK

## Creating Your First Project

### Method 1: Template (Recommended for Learning)
1. Open Epic Games Launcher
2. Launch Unreal Engine 5
3. Select "Games" category
4. Choose **Third Person** template
5. Project Settings:
   - **Blueprint or C++:** Choose C++ (you can use both)
   - **Target Platform:** Desktop
   - **Quality Preset:** Maximum
   - **Raytracing:** Optional (disable for better performance)
   - **Starter Content:** Yes (helpful assets)
6. Name: `ApocalypseMMOPrototype`
7. Create Project

### Method 2: Blank Project (For Production)
- Use when you know what you're doing
- Less bloat, more control
- Start here after completing tutorials

## Understanding the Unreal Editor

### Main Windows
1. **Viewport:** 3D view of your world
2. **Content Browser:** Your assets (models, textures, blueprints)
3. **Details Panel:** Properties of selected object
4. **Outliner:** List of all actors in scene
5. **Toolbar:** Quick actions (Play, Compile, etc.)

### Key Concepts
- **Level:** A game map/scene
- **Actor:** Any object placed in the world
- **Component:** Building blocks of actors (mesh, collision, etc.)
- **Blueprint:** Visual scripting system
- **C++:** For performance-critical code

## Learning Path

### Week 1: Basics
**Goal:** Get comfortable with the editor

1. **Official Tutorial:**
   - Open Unreal → Learning Tab → "Your First Hour in Unreal Engine 5"
   
2. **Practice Tasks:**
   - Move camera (WASD + Right-click drag)
   - Place objects in scene (drag from Content Browser)
   - Transform objects (W=move, E=rotate, R=scale)
   - Play level (Alt+P or green Play button)

3. **YouTube Resources:**
   - "Unreal Engine 5 Beginner Tutorial" by Unreal Sensei
   - "UE5 Complete Beginner Course" by Smart Poly

### Week 2: Character Movement
**Goal:** Understand character controllers

1. **Study Third Person Template:**
   - Open `Content/ThirdPerson/Blueprints/BP_ThirdPersonCharacter`
   - Look at input bindings (Edit → Project Settings → Input)
   - Examine movement component

2. **Customize:**
   - Change movement speed
   - Modify jump height
   - Adjust camera settings

3. **Resources:**
   - "Character Movement in UE5" tutorials

### Week 3: Blueprints
**Goal:** Learn visual scripting

1. **Create Simple Blueprint:**
   - Make a door that opens when approached
   - Create a pickup item
   - Build a simple interaction system

2. **Key Nodes to Learn:**
   - Event Tick (runs every frame)
   - Print String (debugging)
   - Branches (if/else)
   - Timeline (animations)

3. **Resources:**
   - "Blueprint Basics" series by Epic Games

### Week 4: Networking Basics
**Goal:** Understand multiplayer

1. **Concepts:**
   - Server vs Client
   - Replication
   - Remote Procedure Calls (RPCs)
   - Authority

2. **Test Multiplayer:**
   - Play → Number of Players: 2
   - Play → Net Mode: Listen Server
   - See multiple player instances

3. **Simple Networked Blueprint:**
   - Create replicated variable
   - Use Server RPC
   - Understand "Run on Server" vs "Multicast"

4. **Resources:**
   - "Multiplayer in Unreal Engine" by Epic Games
   - "UE5 Networking" tutorials

## For Apocalypse MMO

### Phase 1 Prototype Goals
Create a simple test environment:

1. **Create a Basic Room:**
   - Use BSP geometry (Box tool) or import a plane
   - Add walls and floor
   - Place lights
   - Add Player Start

2. **Character Setup:**
   - Use Third Person template character
   - Customize appearance later
   - Focus on movement first

3. **Test Multiplayer:**
   - Run with 2 players locally
   - Verify you can see other player
   - Test movement synchronization

4. **Simple Interaction:**
   - Place a cube in world
   - Add blueprint to print "Picked up item" when player overlaps
   - Make it replicate (all players see it disappear)

### Networking Architecture for MMO

**Unreal's Built-in Networking:**
- Good for small-scale multiplayer (2-64 players)
- Uses "Listen Server" or "Dedicated Server" model
- Automatic replication of actors and variables

**For True MMO (100+ players):**
You'll likely need:
- **Dedicated server** (no client rendering)
- **Custom networking layer** (may use external server like Node.js)
- **Spatial partitioning** (players only see nearby area)

**Our Approach:**
1. **Phase 1:** Use Unreal's native networking (prove concept)
2. **Phase 2:** Integrate with Node.js server (custom protocol)
3. **Phase 3:** Optimize for scale (if needed)

## C++ vs Blueprints

### Blueprints
**Pros:**
- Visual, easier to learn
- Rapid prototyping
- No compilation time
- Good for designers

**Cons:**
- Slower performance
- Can get messy with complex logic
- Harder to version control

### C++
**Pros:**
- Better performance
- Cleaner for complex systems
- Better IDE support
- Easier to maintain at scale

**Cons:**
- Steeper learning curve
- Compilation time
- More verbose

### Our Strategy
- **Blueprints:** UI, level scripting, quick prototypes
- **C++:** Core systems (networking, character, inventory)
- **Hybrid:** Expose C++ classes to Blueprints for flexibility

## Essential Plugins

### For MMO Development
- **Online Subsystem** (built-in) - networking foundation
- **Enhanced Input** (built-in UE5) - modern input system
- **CommonUI** (built-in) - for multiplayer-friendly UI

### Helpful Plugins
- **Electronic Nodes** - makes Blueprint graphs prettier
- **Asset Validator** - catch errors before shipping
- **Gameplay Abilities** - for skill/ability system (advanced)

## Common Pitfalls

### 1. Asset Size
- Unreal projects get HUGE (10-50GB+)
- Use Git LFS for large files
- Don't commit `Saved/` or `Intermediate/` folders

### 2. Performance
- Don't use Tick for everything (use timers)
- Limit dynamic lights
- Use LODs for models
- Profile early and often

### 3. Networking
- Test with simulated lag (Edit → Editor Preferences → Play → Network Emulation)
- Always think "who has authority?"
- Don't replicate everything (bandwidth!)

### 4. Scope Creep
- Start SIMPLE
- Polish one room before making ten
- Prototype before making it pretty

## Resources

### Official
- [Unreal Engine Documentation](https://docs.unrealengine.com)
- [Unreal Online Learning](https://dev.epicgames.com/community/learning)
- [Official YouTube Channel](https://www.youtube.com/@UnrealEngine)

### Community
- [Unreal Slackers Discord](https://unrealslackers.org/)
- r/unrealengine subreddit
- Unreal Engine Forums

### Courses (Paid)
- Udemy: "Unreal Engine 5 C++ Developer" by GameDev.tv
- Udemy: "Unreal Engine 5 Multiplayer" courses

### YouTube Channels
- **Unreal Sensei** - Great beginner tutorials
- **Smart Poly** - Comprehensive courses
- **Ryan Laley** - Advanced techniques
- **Matt Aspland** - Multiplayer focus

## Next Steps for Apocalypse MMO

1. **Complete "First Hour" Tutorial** (built into Unreal)
2. **Explore Third Person Template** (understand movement)
3. **Test Multiplayer Locally** (2 players, same machine)
4. **Create First Room Prototype** (simple 3D space)
5. **Add Basic Interaction** (pickup an object)
6. **Research Unreal + Node.js Integration** (for our server architecture)

## Questions to Explore

As you learn, think about:
- How will we handle 100+ players in one area?
- What's the best way to sync game state with external server?
- How do we dynamically load/unload zones?
- What art style should we target?
- How complex should combat feel?

## Getting Help

When stuck:
1. Google: "unreal engine 5 [your problem]"
2. Check official docs
3. Ask in Unreal Slackers Discord
4. Post on forums with screenshots/code

Remember: Everyone struggles at first. Unreal is powerful but complex. Take it one step at a time!
