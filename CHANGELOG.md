# Changelog

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
