# Standing Instructions

## Decision Notes

### Philips Hue 100B-0112 bulbs cannot OTA via Zigbee2MQTT

These bulbs (Hue white ambiance E27 with Bluetooth, model 100B-0112) refuse OTA
through Zigbee2MQTT. Every attempt stalls at ~87% and Z2M aborts with an
`image_block_request_timeout` error, regardless of the timeout value. Do not
re-diagnose this; the cause is known.

To update such a bulb:

1. Unpair it from Zigbee2MQTT
2. Pair it temporarily to a Philips Hue bridge
3. Update the firmware there
4. Re-pair it to Zigbee2MQTT (the new firmware is retained)

After re-pairing, Z2M names the device by its IEEE address, so restore the
original `friendly_name` in `/homeassistant/zigbee2mqtt/configuration.yaml`
and rename the HA entities back to the original IDs (e.g. `light.stairs_light`)
so existing references keep resolving.

### /homeassistant/opencode/bin/opencode-v2 is intentional — do not remove

`opencode-v2` is a second, deliberately installed OpenCode CLI (v2.0.18) living
alongside the add-on's own `opencode` (v1.18.32). Two binaries are expected. Do
not treat `opencode-v2` as a stray duplicate, and do not remove it during cleanup
or an add-on version refresh.

OpenChamber (the `fedaykindev.openchamber` VS Code extension, running in the HA
SSH add-on) requires OpenCode 2.x and is pointed at this binary via the
`openchamber.opencodeBinary` setting. That setting takes an absolute path, so v2
does not need to replace or be renamed to `opencode`.

If this binary must be restored:

- The v2.0.18 GitHub *release* was unpublished, so `releases/latest` and the npm
  registry only offer 1.18.32. Prebuilt v2 binaries still exist at
  `https://opencode.ai/files/bin/<version>/`.
- This host is musl libc without AVX2, so the correct asset is
  `opencode-linux-x64-baseline-musl.tar.gz`. A plain `linux-x64` build will not run.
- `install-opencode.sh` targets the `opencode` name only and never touches
  `opencode-v2`.

Deliberate consequences to leave alone:

- Both binaries share the v1 XDG dirs from `/etc/profile.d/opencode.sh`. The old
  `*_v2` XDG dirs were removed on purpose; do not recreate them.
- The first v2 start applies database migration `20260804233008_loose_psylocke`
  to the shared `data/opencode/opencode.db`. That is expected, not corruption.

### Node-RED Lounge scene engine (adopted Oct 2026) — decisions to leave alone

The original Lounge flow was deleted and replaced by a data-driven scene
engine: one 6-scene trigger → "Scene Engine" function → per-scene dispatch
switches, plus a `TV Bring-up` subflow. The surviving tab is named "Lounge";
`node_red_flows/node_red_lounge_scene_draft.json` is the historical design
record of the rewrite, not a pending import.

Deliberate choices that the flow itself does not explain:

- **Xmas is standalone.** Mutual exclusion excludes it in both directions, it
  is not part of the none-on room-light reset, and the 00:06 midnight reset
  does not switch it off. Its two eztimers (goldenHour → xmas on, 23:30 → xmas
  off) exist in the flow with `suspended: true`; unsuspend them in December
  instead of rebuilding them. `switch.xmas` sits unavailable out of season.
- **`input_boolean.tv_auto_on` is kept on purpose.** Only the TV Bring-up
  subflow and the music/Spotify chain set and clear it, and the TV-polling
  guards ("TV Being turned on?") halt on it. Removing it means reworking the
  polling section first.
- **The Shield poll-state node ("Poll Shield Box") was re-added after the
  rewrite.** ADB state updates from the Shield lag badly; the poll is wanted
  even though, as in the original design, it has no output wires.
- **Scene → Lights link-outs are wired deliberately, not leftovers.** Several
  still carry a "REVIEW (dead)" name prefix from the draft, but all are
  linked: movie / music / chill → `link in 3` (light 60%), wii → `link in 2`
  (light 70%), chat → `link in 4` (light 100%), "Reset Room Lights" →
  `link in 5` (scene cascade), and the polling section's "Lights Low" pair →
  `link in 1` (Wii? cascade). Do not re-tag them as dead or remove them.

### Node-RED backup: subflows are included automatically

`node_red_flows/splitter.js` maps **tabs** to export files via `TAB_FILES`;
subflows need no entry there. Every `type === "subflow"` definition plus its
internal nodes is written to `node_red_lights_sub_flow.json`, so `TV Bring-up`
(and any future subflow) is already covered — that file name is historical.
Subflow instances export with their tab's file (the two TV Bring-up instances
live in `node_red_lounge_flow.json`).