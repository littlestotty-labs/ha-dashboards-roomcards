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