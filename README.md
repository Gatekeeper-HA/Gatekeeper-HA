# Gatekeeper AI Doorbell

AI-powered doorbell assistant that greets visitors via speaker, listens to their response, classifies their intent, and replies — all processed locally on your hardware. It notifies you with what they said and a snapshot, and shows up in Home Assistant as a device.

## Installation

1. In Home Assistant, go to **Settings → Add-ons → Add-on Store**
2. Click the **⋮** menu (top right) → **Repositories**
3. Paste `https://github.com/Gatekeeper-HA/Gatekeeper-HA` and click **Add**
4. Find **Gatekeeper AI Doorbell** in the store and click **Install**
5. Configure the options, then click **Start**

You need Frigate (the Frigate add-on works out of the box), an MQTT broker (the Mosquitto broker add-on is used automatically), and a camera with RTSP audio and talkback, e.g. a Reolink doorbell.

See the add-on's **Documentation** tab ([DOCS.md](ha-addon/DOCS.md)) for setup and every option: go2rtc talkback streams, reaching Frigate, notifications, the doorbell button and zones.

## About the code

The add-on installs the `gatekeeper` Python package from the
[Gatekeeper](https://github.com/Gatekeeper-HA/Gatekeeper) repository, pinned to the release
set by `GATEKEEPER_REF` in `ha-addon/build.yaml`. The Docker Compose rig and this add-on run
the same code; only the defaults differ.
