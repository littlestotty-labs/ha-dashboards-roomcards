# Changelog

## [2.7.0] - 2026-10-19

### Added
- `TV Bring-up` subflow in the Lounge flow: one parameterised bring-up (`tv_source` / `sound_mode` / `sound_volume` env vars) shared by the Movie and Wii scenes, with `tv_auto_on` set on entry and cleared after source select
- Xmas scene rows in the engine (plugs on/off) plus its goldenHour and 23:30 eztimers, added with `suspended: true` pending December
- Decision notes in `AGENTS.local.md`: Xmas standalone (excluded from mutual exclusion, none-on reset and midnight), `tv_auto_on` kept because the TV-polling guards halt on it, Shield poll re-added for ADB lag, scene→lights links wired deliberately, and the backup's generic subflow collection
- `node_red_backup_nodes.json` export for the Backup Nodes tab in the node-red-flows backup repo

### Changed
- Lounge tab rebuilt around a data-driven scene engine: one 6-scene trigger → `Scene Engine` function → per-scene dispatch switches, replacing 18 triggers and cascading state checks; green plug auto-scene, midnight 00:06 reset and per-user Spotify carried over unchanged
- Status text is now cleared only by the none-on check (live had 4 racing clear calls), always cleared on last-scene-off and at midnight; `unavailable`/`unknown` switch states are ignored instead of triggering teardown
- Fixed the none-on `ha-get-entities` list: `input_boolean.whatsapp_switch` (was `input_boolean.whatsapp`) and the stray space in ` input_boolean.reading_switch`
- Scene → Lights links wired: movie/music/chill → light 60%, wii → light 70%, chat → light 100%, Reset Room Lights → scene cascade, polling "Lights Low" pair → Wii? cascade; `Poll Shield Box` re-added for Shield ADB state lag
- `splitter.js` subflow collection made explicit and now logged per run (`subflows [Light On, Light Off (1), TV Bring-up]`); Backup Nodes tab mapped to stop the unmapped-tab warning; node-red-flows README table updated

### Removed
- Original Lounge scene section (superseded by the engine); the TV-polling section was carried over into the rebuilt tab

## [2.6.0] - 2026-10-15

### Added
- Vacuum alert button in `bottom_tools.yaml`: `mdi:air-filter` icon, shown only while `input_boolean.purifier_vacuum` is on, with a red badge dot; navigates to `#room_bedroom`

### Changed
- Printer and vacuum toolbar buttons unwrapped from `conditional` cards into direct button-card children, with show/hide moved to a templated `styles.card.display`. The conditional wrappers were being stretched to an equal share by the `horizontal-stack` (they had no `width` to trigger button-card's `flex: 0 0 auto` self-limit), leaving a gap between the two icons
- Both buttons gained `triggers_update` for the entity their `display` template reads, so they hide/show live rather than only on a dashboard reload
- Electric blanket popup icon background now uses `var(--bubble-icon-background-color)`

### Removed
- VACUUM ALERT overlay from the bedroom room card (`bedroom.yaml`) — the `vac` custom field and its styling, superseded by the toolbar button

## [2.5.0] - 2026-10-15

### Added
- Three `problem` binary sensors in `template.yaml`: `tech_storage_low` (any storage bar >= 90% used), `printer_ink_low` (any cartridge <= 10%), `battery_very_low` (any battery <= 10%)
- Red warning badge dots on the tech, battery and printer buttons in `bottom_tools.yaml`, driven by those sensors; battery button icon switched to `mdi:battery-low`

### Changed
- Plug entity updates: lounge pedestal fan now `switch.tuya_pink_2`; office fan now `switch.plug_purple_2`; office heated-throw and lounge heater cards commented out pending their new entities
- Tech popup renamed "Device Storage" and restructured into NVIDIA SHIELD / SYNOLOGY (DS216+II) / QNAP (TS-264) cards, each with its own icon and header
- Storage bars restyled: square 15px bars, 12-15px labels at full opacity, tighter inset shadow
- Battery popup restyled: square 15px bars, 14px name/state at full opacity; filter now hides only batteries above 99% (was 30%)
- Battery friendly names in `customize.yaml` no longer repeat the "Battery" suffix; added Office Temp/Humidity
- Printers popup heading -> "Ink Levels", icon hidden, header uses primary text colour
- Bedroom/office heat emoji now scale to 🔥🔥 / 🔥🔥🔥 at 30/33 °C
- Sizing tweaks: room card 152px, room button 41px, scene button 66px, separator label 15px; fan button icon logic corrected; blanket state labels bold

### Fixed
- Template sensors hardened against unavailable entities (Shield/TV source, soundbar/TV volume, plug power, Ryde temperature now `°C`); removed duplicated `shield_source`
- `tv_volume` now reads `media_player.lg_webos_smart_tv`
- Printer button condition uses `state_not: Unavailable` consistently; dropped invalid `device_class: volume` from `number_of_lights_on`

## [2.4.0] - 2026-10-06

### Added
- New `batteries` popup card (`#room_batteries`) holding the low-battery list moved out of the tech popup, opened by a new always-on battery button in `bottom_tools.yaml`
- New `printers` popup card (`#room_printers`) holding the HP Color LaserJet ink levels moved out of the tech popup, opened by a new printer button in `bottom_tools.yaml` conditional on `sensor.hp_color_laserjet_mfp_m277dw` being available
- Both new popups wired into `ui-lovelace.yaml`

### Changed
- Tech popup (`popup_cards/tech.yaml`) now contains only the Server Storage card

## [2.3.0] - 2026-09-22

### Added
- Electric blanket controls in bedroom popup: Dawn + Graham on/off switches with body/feet power level (0–6) and timer selects, conditional off/on cards
- Bedroom room card: four-zone animated SVG heat visualization (intensity-scaled glow, rings, expanding waves per zone), Dawn left / Graham right, accent background circle replacing bed icon
- Theme vars: electric-blanket-card-dawn/graham (dark + light), bubble-timer-color-on

### Changed
- room_card template: default `i` custom-field sizing for SVG room art
- bubble_base: softened on-state img_cell inset shadow
- Light theme card background and bubble on-icon tweak; dark theme timer colour

### Removed
- dashboards/home_cards/banner.yaml

## [2.2.1] - 2026-09-16

### Fixed
- Rewrote `dishwasher_start` script to pass options directly to `start_program` — options were not available when set separately via `set_program_option`

## [2.2.0] - 2026-09-05

### Added
- Dishwasher program sensors (name, description, icon) from Home Connect entity mappings
- Dishwasher state-sync template sensors for program, extra dry, hygiene plus, speed perfect
- `dishwasher_start` script for selecting program and options, then starting the dishwasher
- Kitchen popup card expanded with dishwasher controls, program selection, and status display
- New `bottom_tools.yaml` hamburger menu card with sidebar toggle and tech room navigation
- Decluttering templates for dishwasher control rows and status displays

### Changed
- Replaced `menu.yaml` with `bottom_tools.yaml` for bottom toolbar navigation
- Reformatted automations for consistent indentation and style
- Updated kitchen dashboard card with dishwasher integration
- Minor theme tweaks (dark and light)

### Removed
- `dashboards/home_cards/menu.yaml` (replaced by `bottom_tools.yaml`)

## [2.1.1] - 2026-08-17

### Removed
- Node-RED flow JSON files moved out of this repo into their own repository ([node-red-flows](https://github.com/littlestotty-labs/node-red-flows)) — this instance is primarily driven by Node-RED automation, and that repo now holds the flow files.

### Added 
- New dev environment in fedora/vscode to access ha and opencode via openChamber and ssh-remote extensions.
- Migration of opencode, hab, ha-mcp-server and zigporter from opencode addon to ssh addon
- inclusion of init-command symlinks via root so all migration survives updates and resets.

### Changed
- Minor tweak to Kitchen room to accomodate dishwasher status.

## [2.1.0] — 2026-08-06

### Added
- Waste collection card and `waste_day_button` template (`waste_collection_schedule`, Ryde NSW)
- Menu card for dashboard navigation
- Light and scene button templates (`light`, `scene`, `bubble_light`, `bubble_scene`)
- `custom-brand-icons` and Mushroom Better Sliders Lovelace resources

### Changed
- Screenshots replaced with PNG versions
- Light entity references renamed (lobby, stairs, office, lounge)

### Removed
- `home_cards_flat/` flat dashboard variant (11 files)
- `grahams_dash.yaml`, `room_card_flat.yaml`
- `popup_cards/weather.yaml`
- Stale `.bak` files (`automations.yaml.bak`, `ui-lovelace.yaml.bak`)

## [2.0.0] — 2026-06-12

### Added
- Decluttering storage bars for battery/usage tracking
- Light row grid template (`light_row_v2`) with motion sensor badge
- Landing motion combined binary sensor
- Slider gradient theme variables for light/dark modes

### Changed
- Room popups modularised into `dashboards/popup_cards/`
- Decluttering templates split into `dashboards/includes/`
- Theme input field colours for better light/dark contrast

### Removed
- Deprecated `light_row` bubble-card slider template

## [1.3.0] — 2026-06-06

### Added
- Tech popup with clearer storage readouts
- Node-RED dashboard changes
- OpenCode integration for AI-driven config management

### Fixed
- Theme input field styling (idle/hover/focus states)

## [1.2.0] — 2026-04-30

### Added
- OpenCode refactor tests and automation scripts
- Header documentation for all YAML files

### Changed
- Refactored dashboard configuration for AI tool compatibility

## [1.1.0] — 2026-03-22

### Added
- Room card refactor with standalone pop-up cards
- Split room cards and templates into sub-files
- Decluttering-card templates for reusable button rows

### Changed
- Migrated from inline bubble-card configs to decluttering templates
- Restructured `dashboards/` directory for modularity

## [1.0.0] — 2026-03-20

### Added
- Initial Home Assistant configuration
- Dashboard YAML mode with Lovelace resources
- Theme files (grahams-dark, grahams-light)
- Bubble Card pop-ups for room controls
- Room accent colour variables
- Light row slider with lock override
- Scene toggle buttons
- Weather chip
- Media player controls (Shield, LG TV, soundbar)
- NSW fuel price sensors
- Emby media player integration
- Input boolean helpers for automation flags
- License (MIT)
