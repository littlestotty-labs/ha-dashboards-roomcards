# Copilot Instructions — ha-dashboards-roomcards

## Project Overview
This is a **Home Assistant YAML-mode dashboard configuration** built around a single-page, room-centric card layout. Automations live in Node-RED (JSON flows), not HA YAML automations. The dashboard uses Bubble Card pop-ups (hash navigation like `#room_lounge`) instead of multi-page navigation.

## Architecture & File Relationships

### Dashboard Assembly (`dashboards/ui-lovelace.yaml`)
The main dashboard file wires everything together via HA `!include` directives:
- `button_card_templates: !include_dir_merge_named button_templates/` — merges ALL YAML files in `button_templates/` as a single template dictionary
- Room cards are included from `home_cards/` (e.g., `!include home_cards/lounge.yaml`)
- Bubble Card pop-ups are defined **inline** in `ui-lovelace.yaml` — Bubble Card does not currently support `!include` for pop-ups, so all pop-up definitions must remain in this single file
- Decluttering templates and kiosk config live in `dashboards/includes/`

### Button-Card Template Hierarchy
Templates use **composition via `template:` arrays** (button-card inheritance):
- **`room_card`** — base layout for room tiles on the main grid (140px cards with name, label, icon circle, and `btn` custom field)
- **`room_button`** — small 44×35px toggle buttons inside a room card's `btn` slot
- **`bubble_base`** — base for popup toggle buttons (on/off states with theme-aware colors)
- **Device mixins** — `fan`, `heat`, `aircon` add CSS animations (`@keyframes rotating`, `heat-shimmer`) and are composed: e.g., `bubble_fan` = `[bubble_base, fan]`, `fan_room_button` = `[room_button, fan]`
- **`inkwell`** — specialized ink-level display template (printer ink cartridges)

### Theme System (`themes/`)
Two themes (`grahams-dark`, `grahams-light`) define **per-room accent color CSS variables**:
```yaml
room-lounge: "#d19c51ff"
room-bedroom: "#3a9ff8ff"
room-office: "#78b78fff"
```
Room cards reference these as `var(--room-lounge)`, passed via `variables.accent_color`. Both themes must define the same variable names with tuned values.

### Decluttering Templates (`dashboards/includes/decluttering.yaml`)
Uses `decluttering-card` for reusable parameterized cards. Key templates:
- `light_row` — Bubble Card slider with lock sub-button (uses `[[entity]]`, `[[lock_entity]]` placeholder syntax — **double-bracket**, distinct from button-card's `[[[JS]]]` triple-bracket)
- `scene_button` — standardized scene toggle

## Key Conventions

### Variable/Placeholder Syntax
- **Button-card JS templates**: `[[[return variables.name]]]` (triple brackets, JavaScript)
- **Decluttering-card variables**: `'[[entity]]'` (double brackets, string substitution)
- **Jinja2 templates** (in `template.yaml`, `configuration.yaml`): `{{ states.sensor.x.state }}`

### Adding a New Room
1. Create `dashboards/home_cards/<room>.yaml` using `room_card` template with `variables.accent_color: var(--room-<name>)`
2. Add `room-<name>` CSS variable to **both** `themes/dark_theme.yaml` and `themes/light_theme.yaml`
3. Include the card in `ui-lovelace.yaml`'s grid section
4. Add the Bubble Card pop-up section in `ui-lovelace.yaml` with `hash: '#room_<name>'` and matching `.bubble-close-icon` / `.bubble-header` color styles using `var(--room-<name>)`

### Adding a New Button Template
Place in `dashboards/button_templates/` — it is auto-merged via `!include_dir_merge_named`. The top-level key in the YAML file becomes the template name. Compose with existing mixins where possible (e.g., `template: [room_button, fan]`).

## HACS Custom Cards Required
`button-card`, `bubble-card`, `card-mod`, `decluttering-card`, `kiosk-mode`, `layout-card`, `mini-media-player`, `mini-graph-card`, `auto-entities`, `apexcharts-card`, `stack-in-card`, `vertical-stack-in-card`, `clock-weather-card`, `swipe-card`, `gap-card`, `horizon-card`, `template-entity-row`, `custom-brand-icons`

## Sensitive Data
Never commit `secrets.yaml` or `google_calendars.yaml`. Use `secrets_example.yaml` as reference. Entity IDs and device names are fine to commit; credentials and URLs are not.

## Entity Naming — Smart Plugs
Smart plugs are identified by **physical color stickers** (e.g., `switch.plug_pink_2`, `switch.plug_blue`). The color is the stable identifier; the `name` shown in the dashboard reflects what is currently plugged in and may change over time. When referencing plugs in templates, always use the color-based `entity_id`, not the display name.
