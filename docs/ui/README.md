# UI/UX Design Documents

This directory contains all UI/UX design documents for the project.

## Documents

- [`ux-design-system.md`](ux-design-system.md) — UI design system (colors, typography, components, accessibility)
- [`user-flows.md`](user-flows.md) — User flow diagrams for core loops, screen transitions, interactions, settings, and onboarding
- [`wireframes.md`](wireframes.md) — Wireframe diagrams for main HUD, pause menu, settings, tutorial, game over, and success screens
- [`main-hud.md`](main-hud.md) — Main HUD design and implementation guide (existing)

## Design Intent

The UI/UX is designed around the core stealth gameplay loop:
**은신 → 정보 획득 → 탈출**

All UI elements support this loop without introducing cognitive friction. Critical information (stealth level, objectives, detection risk) is always visible or immediately accessible. The design is dark-mode optimized for the military stealth aesthetic, with full accessibility support including colorblind modes, input remapping, and resolution independence.

## Adding New Documents

When adding new UI-related documents, follow the naming convention and frontmatter pattern established in this folder. All documents should reference the core loop and maintain consistency with the design system.