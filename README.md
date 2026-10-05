# Gatekeeper AI Doorbell

AI-powered doorbell assistant that greets visitors via speaker, listens to their response, classifies their intent, and replies — all processed locally on your hardware. It notifies you with what they said and a snapshot, and shows up in Home Assistant as a device.

## Installation

You need Frigate (the Frigate add-on works out of the box), an MQTT broker (the Mosquitto broker add-on is used automatically), and a camera with RTSP audio and talkback, e.g. a Reolink doorbell. Set up Frigate first, with person detection and the talkback stream: see [Frigate setup](ha-addon/DOCS.md#frigate-setup).

1. In Home Assistant, go to **Settings → Add-ons → Add-on Store** (add-ons are called **Apps** in newer Home Assistant versions)
2. Click the **⋮** menu (top right) → **Repositories**
3. Paste `https://github.com/Gatekeeper-HA/Gatekeeper-HA` and click **Add**
4. Find **Gatekeeper AI Doorbell** in the store and click **Install**. It's built on your machine, which takes from about 10 minutes to over an hour on a slow machine. If the **Install** button comes back after a page refresh, it's still building: don't click it again.
5. In the **Configuration** tab, set `ntfy_url` (and `ntfy_token`) for notifications and the `reolink_*` options for the doorbell button. Both are off until you set them. The defaults already reach the Frigate and Mosquitto add-ons.
6. Click **Start**. The first start takes a few minutes; it's ready when the **Log** tab shows `connected to MQTT`.
7. If you haven't yet: **Settings → Devices & services**, add the discovered **MQTT** integration. A **Gatekeeper** device appears.

See the add-on's **Documentation** tab ([DOCS.md](ha-addon/DOCS.md)) for every option and for troubleshooting: Frigate and go2rtc setup, reaching Frigate, notifications, the doorbell button and zones.

## About the code

The add-on installs the `gatekeeper` Python package from the
[Gatekeeper](https://github.com/Gatekeeper-HA/Gatekeeper) repository, pinned to the release
set by `GATEKEEPER_REF` in `ha-addon/build.yaml`. The Docker Compose rig and this add-on run
the same code; only the defaults differ.

## Contributing

Contributions are welcome: see [CONTRIBUTING.md](CONTRIBUTING.md). Your first pull request
asks you to agree to the
[Contributor License Agreement](https://github.com/Gatekeeper-HA/Gatekeeper/blob/main/CLA.md).

## License

AGPL-3.0 — see [LICENSE](LICENSE)
