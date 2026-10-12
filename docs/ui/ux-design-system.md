# UI Design System

## Color Palette

| Usage | Primary | Secondary | Accent | Neutral |
|---|---|---|---|---|
| Background | `#0A0A0A` (almost black) | `#1A1A1A` | — | `#2A2A2A` |
| Text (primary) | `#F5F5F5` (off-white) | — | — | `#CCCCCC` |
| Text (secondary) | — | `#888888` (muted) | — | — |
| Success/Normal | — | `#4CAF50` (green) | — | — |
| Warning | — | `#FFC107` (amber) | — | — |
| Danger/Risk | — | `#F44336` (red) | — | — |
| Highlight/Accent | — | — | `#FF5722` (orange) | — |
| Border/Divider | — | — | — | `#444444` |

> **Note**: All colors are dark-mode optimized for stealth gameplay. Primary text is off-white for readability against dark backgrounds.

## Typography

| Property | Value |
|---|---|
| Font Family | "Roboto", "Segoe UI", system UI fallback |
| Base Size | 14pt at 1920×1080 (scales with resolution) |
| Heading Size | 20pt bold |
| Label Size | 12pt regular |
| Mini Text Size | 10pt (caption-sized) |

## Core UI Components

### 1. Stealth Gauge
- **Type**: Circular/radial progress bar
- **Size**: 80×80px minimum (scales with resolution)
- **Background**: Transparent or subtle circle outline
- **Fill Color**: 
  - 0-30%: `#4CAF50` (green)
  - 31-70%: `#FFC107` (amber/yellow)
  - 71-100%: `#F44336` (red)
- **Border**: 2px, `#666666`
- **Display**: Percentage text in center (e.g., "73%")
- **Animation**: Smooth value transition over 0.2s

### 2. Minimap
- **Type**: Square texture rect with optional overlay nodes
- **Size**: 200×200px (configurable per resolution preset)
- **Position**: Top-right corner
- **Content**: 
  - Level layout (simplified outline)
  - Player position marker (blue circle)
  - Enemy vision cones (red/orange triangles)
  - Objective markers (green star)
  - Interaction prompt markers (exclamation!)
- **Toggle**: Player can enable/disable in settings

### 3. Objective Label
- **Type**: Centered top text label
- **Size**: Dynamic, max 30 characters
- **Color**: `#F5F5F5`
- **Background**: Semi-transparent black bar (`rgba(0,0,0,0.6)`) for contrast
- **Content**: "목표: 정보 획득" / "목표: 탈출지점 도달" / "탈출 성공!" / "탐지됨! 실패..."
- **Animation**: Fade in/out on state change over 0.3s

### 4. Interaction Prompt
- **Type**: Centered bottom text label
- **Size**: Dynamic, max 25 characters
- **Color**: `#F5F5F5`
- **Background**: Semi-transparent bar for contrast
- **Content**: "{키}로 상호작용" (e.g., "E키로 상호작용" or "패드 A버튼으로 상호작용")
- **Visibility**: Only when player is near interactable object
- **Animation**: Pulse animation on interactive objects (1.0s cycle)

### 5. Alert Flash
- **Type**: Fullscreen ColorRect overlay
- **Default**: Fully transparent (`Color(1, 0, 0, 0)`)
- **On Detection**: `Color(1, 0, 0, 0.3)` with pulsating animation
- **Duration**: 2-3 seconds, then fade out
- **Purpose**: Visual cue when player detection risk increases

### 6. Notification Log
- **Type**: Left-bottom scrollable text area
- **Max Lines**: 3 visible lines
- **Auto-remove**: Messages fade after 5 seconds (configurable 3-10s)
- **Color**: `#F5F5F5` text on `rgba(0,0,0,0.6)` background
- **Content Types**:
  - "목표 완료!"
  - "적 발견됨!"
  - "정보 획득!"
  - Custom event messages

### 7. Help Overlay/Tooltip
- **Type**: Right-bottom panel or fullscreen overlay
- **Activation**: F1 key or settings toggle, or tutorial context
- **Content**: 
  - Control scheme display
  - System hints (first time only)
  - Accessibility options preview
- **Dismiss**: Esc, timeout after 10s, or manual close button

## Layout & Spacing

### Anchor Rules (Godot CanvasLayer)
- All UI elements use **AnchorContainer** as root
- Anchors defined as `(left, top, right, bottom)` in normalized coordinates (0-1)
- Margins in pixels from anchor edges

### Safe Zones
- Minimum 20px margin from all screen edges on 1920×1080
- Margins scale proportionally with resolution changes
- No critical information within 40px of top/bottom edges (taskbar/ dock area)

### Vertical Spacing
- Between grouped elements: 12px (default), 24px (section divider)
- Within compact groups: 8px

### Horizontal Spacing
- Between grouped elements: 16px (default), 32px (section divider)

## Responsive Breakpoints

| Preset | Resolution | Behavior |
|---|---|---|
| Wide 16:9 | 1920×1080 | Default layout, all elements visible |
| Tall 16:10 | 1920×1200 | Vertical spacing adjusted, minimap height increases |
| UltraWide 21:9 | 2560×1080 | Extended horizontal space, HUD elements spread wider |
| Vertical 9:16 | 1080×1920 (mobile) | Compact layout, collapsible sections, priority reordering |

> **Responsive Principle**: Never break core functionality. Collapse or reorder secondary elements. Critical information (stealth gauge, objective, minimap) must remain visible in all presets.

## Accessibility

| Feature | Implementation |
|---|---|
| Color Blind Support | Stealth gauge uses **thickness** and **pattern** distinctions in addition to color. Alert flash uses **pattern** (dashed vs solid). |
| Text Scaling | Minimum 14pt at 1920×1080, scales with resolution. Player can override via settings. |
| Input Remapping | All UI actions fully remappable via InputMap. No hardcoded key assumptions. |
| Screen Reader | UI elements have accessible names via `AccessibleName` property. |
| Motion Reduction | Option to disable pulsating animations and flashing effects. |
| High Contrast Mode | Switch to `#[FFFFFF]` text on `#[000000]` background for high contrast. |

## Godot Implementation References

- **Theme**: `res://ui/theme/default_hud.tres`
- **Scene**: `res://ui/main_hud/main_hud.tscn`
- **Signals**:
  - `stealth_updated(exposure: float)`: Emitted when stealth level changes
  - `audibility_updated(exposure: float)`: Emitted when audibility level changes
  - `objective_updated(new_objective: String)`: Emitted when objective changes
  - `interaction_prompt_shown(show: Boolean, action_name: String)`: Emitted when prompt appears
  - `notification_added(message: String, duration: float)`: Emitted when notification added
  - `help_toggled(show: Boolean)`: Emitted when help overlay toggled

- **UI Node Hierarchy** (from main_hud.tscn):
  ```
  CanvasLayer (MainHUD)
  └── AnchorContainer (fullscreen)
      ├── StealthGauge (TextureProgressBar) - left-bottom
      ├── Minimap (TextureRect) - top-right
      ├── ObjectiveLabel (Label) - top-center
      ├── InteractionPrompt (Label) - bottom-center
      └── AlertFlash (ColorRect) - fullscreen behind other elements
  ```

## TODO: Future UI Screens

| ID | Screen | Priority | Dependencies |
|---|---|---|---|
| T-1 | Pause Menu | 높음 | Main HUD enabled → Pause → Main HUD disabled |
| T-2 | Settings Menu | 중간 | Pause Menu → Settings → Resume |
| T-3 | Inventory/Equipment | 낮음 | Core loop completed |
| T-4 | Map/Navigation | 중간 | Minimap feature enabled |
| T-5 | Onboarding/Tutorial | 높음 | New player only |
| T-6 | Game Over/Success | 중간 | Mission completion/failure |

> **Design Principle**: All UI elements must be implementable without modifying core game logic after the design freeze. Any additions post-freeze require impact assessment on the HUD scene and signal wiring.