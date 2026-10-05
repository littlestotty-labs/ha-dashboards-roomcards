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