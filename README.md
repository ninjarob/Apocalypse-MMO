# Apocalypse MMO

A modern 3D MMO adaptation of the classic Apocalypse VI text-based MUD, built with Unreal Engine.

## Project Overview

This repository serves as the central planning and documentation hub for the Apocalypse MMO project. The goal is to transform the text-based Apocalypse VI MUD into a fully-realized 3D MMO experience while preserving the spirit and gameplay of the original.

## Repository Structure

This is a **documentation and planning repository**. The actual code is split across multiple repositories:

- **[Apocalypse-MMO-Server](https://github.com/ninjarob/Apocalypse-MMO-Server)** - Game server (authoritative logic, networking, database)
- **[Apocalypse-MMO-Client](https://github.com/ninjarob/Apocalypse-MMO-Client)** - Unreal Engine 5 client
- **[Apocalypse-MMO-Shared](https://github.com/ninjarob/Apocalypse-MMO-Shared)** - Shared protocols, types, and data structures
- **[Apocalypse-VI-Web-and-Crawler](https://github.com/ninjarob/Apocalypse-VI-Web-and-Crawler)** - MUD exploration and data extraction tools

## Technology Stack

### Client
- **Engine:** Unreal Engine 5
- **Language:** C++ / Blueprints
- **Graphics:** Full 3D with modern rendering

### Server
- **Language:** Node.js with TypeScript
- **Database:** SQLite (development) / PostgreSQL (production)
- **Networking:** WebSockets for real-time communication
- **Moddability:** JavaScript-based mod system with hot-reload

### Data Pipeline
- MUD data extraction and parsing
- Automated conversion to 3D-compatible formats
- Asset generation tools
- Data-driven design (JSON/CSV for all content)

## Design Principles

- **Moddability First:** Built to be easily extensible by the community
- **Data-Driven:** Game content defined in JSON/CSV, not hardcoded
- **Rapid Iteration:** Fast development and testing cycles
- **Performance Second:** Optimize after gameplay is fun

## Project Status

🚧 **Phase:** Initial Planning & Architecture

### Current Focus
- [ ] Define technical architecture
- [ ] Design mod API and system
- [ ] Plan data conversion pipeline
- [ ] Prototype basic 3D environment
- [ ] Design networking infrastructure

## Documentation

See the `/docs` folder for comprehensive documentation:

### Core Documentation
- **[Architecture](./docs/ARCHITECTURE.md)** - System design and technical architecture
- **[Roadmap](./docs/ROADMAP.md)** - Development phases and timeline
- **[Moddability](./docs/MODDABILITY.md)** - Mod system design and API
- **[MUD Conversion](./docs/MUD_CONVERSION.md)** - Strategy for converting text to 3D

### Development Guides
- **[AI Dev Guide](./docs/AI_DEV_GUIDE.md)** - Context for AI assistants
- **[Coding Standards](./docs/CODING_STANDARDS.md)** - TypeScript style guide
- **[Glossary](./docs/GLOSSARY.md)** - Project terminology reference
- **[Unreal Getting Started](./docs/UNREAL_GETTING_STARTED.md)** - Learn Unreal Engine 5
- **[Setup Complete](./docs/SETUP_COMPLETE.md)** - Repository overview and quick start

## Contributing

This is currently a personal project, but contributions and suggestions are welcome!

## License

TBD
