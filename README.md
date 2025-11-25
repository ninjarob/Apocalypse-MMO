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
- **Language:** Node.js with TypeScript (or C++ if performance demands)
- **Database:** SQLite/PostgreSQL
- **Networking:** WebSockets / Unreal's native replication

### Data Pipeline
- MUD data extraction and parsing
- Automated conversion to 3D-compatible formats
- Asset generation tools

## Project Status

🚧 **Phase:** Initial Planning & Architecture

### Current Focus
- [ ] Define technical architecture
- [ ] Plan data conversion pipeline
- [ ] Prototype basic 3D environment
- [ ] Design networking infrastructure

## Documentation

See the `/docs` folder for detailed documentation:
- Architecture decisions
- Design documents
- Development roadmap
- API specifications

## Contributing

This is currently a personal project, but contributions and suggestions are welcome!

## License

TBD
