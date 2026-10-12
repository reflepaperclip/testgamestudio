# Game Architecture

## Overview

This document outlines the technical architecture of the game built with Godot 4.x. The architecture follows a modular, entity-component-system (ECS) inspired approach while leveraging Godot's node system and scene architecture.

## Core Principles

1. **Modularity**: Systems are decoupled and communicate through well-defined interfaces (signals, events, or resource-based communication).
2. **Data-Oriented Design**: Game state is stored in resources or lightweight data structures to facilitate saving/loading and networking.
3. **Extensibility**: New systems and features can be added without modifying existing code.
4. **Performance**: Critical systems are optimized for frame rate and memory usage.

## High-Level Structure

### 1. Engine Layer (Godot)
- Utilizes Godot 4.x engine for rendering, physics, input, and core functionality.
- Custom modules and plugins are avoided to maintain portability; instead, we use GDScript and built-in nodes.

### 2. Game Systems Layer
Each system is responsible for a specific domain and operates as an autoload (singleton) or is instantiated per scene as needed.

#### Existing Systems
- **Stealth System**: Handles visibility, noise, and detection mechanics.
- **AI System**: Base AI behaviors and decision-making.
- **Enemy AI System**: Specialized AI for enemy entities.
- **Escape System**: Manages escape objectives and conditions.
- **Info System**: Handles UI information display, tooltips, and HUD elements.

#### System Communication
- Systems communicate via:
  - **Signals**: For loose coupling and event-driven interactions.
  - **Event Bus**: Global EventBus (autoload) for cross-system events.
  - **Shared Resources**: Configuration and state stored in Resources (e.g., `StealthConfig.tres`).

### 3. Entity Layer
- Entities are composed of nodes (CharacterBody2D/3D, Sprite2D, CollisionShape2D, etc.).
- Each entity type (Player, Enemy, NPC, Item) is a scene with inherited base classes.
- Components are implemented as scripts attached to nodes, providing specific functionalities (e.g., `StealthComponent.gd`, `HealthComponent.gd`).

### 4. Scene Layer
- **Scenes** represent levels, menus, UI, and reusable entities.
- Level scenes are designed to be modular, allowing for streaming or chunk-based loading.
- UI scenes are separated from gameplay scenes and loaded as overlays or underlays via Viewport or CanvasLayer.

### 5. Data Layer
- **Configuration**: Stored in `.tres` resources (e.g., `GameConfig.tres`, `DifficultySettings.tres`).
- **Save Data**: Serialized to JSON or custom binary format for save/load functionality.
- **Procedural Content**: Generated via scripts and stored in temporary resources.

## Key Architectural Decisions

### 1. Use of Autoloads (Singletons)
- Systems like `EventBus`, `SaveLoadManager`, and `ConfigurationManager` are autoloads for global access.
- Caution is taken to avoid overuse; systems that are scene-specific are instantiated per scene.

### 2. Node-Based Composition
- Avoids deep inheritance; instead, uses composition by adding node children or scripts as components.
- Example: A player node may have a `CharacterBody2D`, a `StealthComponent` script, and a `HealthComponent` script.

### 3. Signal-Based Communication
- Nodes emit signals for state changes (e.g., `health_changed`, `detected`).
- Systems connect to relevant signals to update their state.

### 4. Resource-Driven Design
- Game data (enemy stats, level layouts, item properties) is stored in editable Resources.
- Enables non-programmers to adjust game balance and content.

## Dependencies and External Tools
- **Godot 4.x**: Stable release (4.2+ recommended).
- **Blender**: For 3D asset creation (if applicable) and glb export.
- **Aseprite**: For 2D sprite and animation creation.
- **Git**: Version control with LFS for large assets.

## Diagrams
*(To be added: Component diagram, System interaction diagram, Data flow diagram)*

## Conclusion
This architecture provides a solid foundation for a stealth-based game, emphasizing modularity, extensibility, and maintainability. It leverages Godot's strengths while introducing patterns to manage complexity.
