# Technology Choices

## Game Engine: Godot 4.x

### Why Godot?
- **Open Source**: No licensing fees, source available for debugging and customization.
- **Node-Based Architecture**: Aligns with our modular design principles.
- **GDScript**: Python-like language that is easy to learn and integrate with the engine.
- **Cross-Platform**: Supports Windows, macOS, Linux, Android, iOS, and Web (HTML5).
- **Built-in Tools**: Includes editor, debugger, profiler, and animation tools.
- **Active Community**: Growing ecosystem with tutorials and plugins.

### Why Version 4.x?
- Significant performance improvements over 3.x.
- Enhanced rendering engine (Vulkan renderer).
- Improved GDScript with optional static typing.
- Better multiplayer and networking features (if needed in future).

## Programming Language: GDScript

### Why GDScript?
- **Tight Integration**: Native to Godot, no compilation steps, instant feedback.
- **Ease of Use**: Syntax similar to Python, lowering the barrier for contributors.
- **Performance**: Sufficient for most game logic; critical sections can be optimized with C++ if needed.
- **Built-in Types**: Rich set of engine-specific types (Vector2, Transform2D, etc.).
- **Signal System**: First-class support for Godot's signal-based communication.

### Considerations for Alternatives
- **C#**: Offered via .NET integration; provides static typing and familiarity for some developers, but increases build complexity and binary size. Not chosen to keep the toolchain simple.
- **C++ Modules**: For engine extensions; not needed as Godot 4.x features cover our requirements.

## Art and Asset Creation

### 2D Art
- **Aseprite**: For pixel art and sprite animations. Chosen for its robust animation tools and ease of exporting sprite sheets.
- **Krita**: For concept art, textures, and UI elements. Free and open-source alternative to Photoshop.

### 3D Art (if applicable)
- **Blender**: For modeling, rigging, and animation. Industry-standard open-source 3D suite.
  - Exports to `.glb` format for seamless import into Godot.

### Audio
- **Audacity**: For editing and processing sound effects and music.
- **LMMS or Bosca Ceoil**: For creating chiptune or electronic music (if needed).

## Version Control
- **Git**: Distributed version control system.
- **Git LFS**: For handling large binary assets (textures, models, audio files).
- **Hosting**: GitHub (or similar) for collaboration, issue tracking, and CI/CD.

## Build and Deployment
- **Godot Export Templates**: Official templates for exporting to target platforms.
- **Custom Export Scripts**: To automate builds for different configurations (debug/release).
- **CI/CD**: GitHub Actions for building and testing on push/pull request (if applicable).

## Dependencies and Middleware
- **No External Middleware**: We rely on Godot's built-in features for physics, navigation, and rendering to avoid licensing and integration complexity.
- **Potential Future Additions**:
  - **Steamworks**: For PC distribution via Steam (if decided later).
  - **Firebase**: For analytics, authentication, and cloud saves (if expanding to online features).

## Development Tools
- **IDE**: 
  - Godot's built-in editor for scene and script editing.
  - VS Code with GDScript extension for advanced coding features (optional).
- **Debugging**: 
  - Godot's debugger and profiler.
  - External profilers (e.g., Perfetto) for deep performance analysis.
- **Testing**: 
  - Godot's built-in unit testing framework (via GUT or similar) for logic testing.
  - Manual playtesting for gameplay and QA.

## Rationale Summary
We chose Godot 4.x and GDScript for their combined strengths in rapid development, flexibility, and zero cost. The technology stack is designed to be accessible to a wide range of contributors while providing the power needed to create a polished stealth game. By avoiding unnecessary dependencies, we reduce potential points of failure and keep the build process simple.
