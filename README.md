# Gatekeeper AI Doorbell

AI-powered doorbell assistant that greets visitors via speaker, listens to their response, classifies their intent, and replies — all processed locally on your hardware.

## Installation

1. In Home Assistant, go to **Settings → Add-ons → Add-on Store**
2. Click the **⋮** menu (top right) → **Repositories**
3. Paste `https://github.com/Gatekeeper-HA/Gatekeeper-HA` and click **Add**
4. Find **Gatekeeper AI Doorbell** in the store and click **Install**
5. Configure the options (see below) then click **Start**

## Prerequisites

- **Frigate** — person detection, publishes events to MQTT
- **go2rtc** — WebRTC talkback to your camera speaker
- **Mosquitto** (or any MQTT broker) — receives Frigate events
- A camera with RTSP audio and speaker/talkback support

## go2rtc setup

Define a talkback stream in your `go2rtc.yaml`:

```yaml
streams:
  front_door:
    - rtsp://user:pass@192.168.1.100/stream1
  front_door_talk:
    - rtsp://user:pass@192.168.1.100/backchannel
```

Set `audio_rtsp_url` to the listening stream go2rtc exposes (e.g. `rtsp://localhost:8554/front_door`) and `go2rtc_talk_stream` to the backchannel stream name (e.g. `front_door_talk`).

## Configuration

| Option | Default | Description |
|--------|---------|-------------|
| `camera_name` | `front_door` | Must match the camera name in your Frigate config |
| `mqtt_host` | `core-mosquitto` | HA's built-in Mosquitto broker. Change if using an external broker |
| `mqtt_port` | `1883` | MQTT port |
| `mqtt_topic` | `frigate/events` | Frigate event topic — leave as default unless you changed it |
| `audio_rtsp_url` | `rtsp://localhost:8554/front_door` | RTSP stream for capturing visitor audio (from go2rtc) |
| `go2rtc_api` | `http://localhost:1984` | go2rtc API base URL |
| `go2rtc_talk_stream` | `front_door_talk` | go2rtc stream name for talkback |
| `dwell_seconds` | `1` | Seconds a person must be visible before triggering |
| `listen_seconds` | `4` | How long to record the visitor's response |
| `session_ttl_seconds` | `120` | Forget a Frigate event this long after its last update |
| `sweep_interval_seconds` | `1` | How often sessions are checked for dwell and expiry |
| `cooldown_seconds` | `90` | After a visit, new person detections this soon are merged into it instead of greeting again |
| `visit_timeout_seconds` | `60` | Give up on a visit (greet, listen, reply) after this long |
| `whisper_model` | `tiny` | Whisper STT model size. `tiny` is fastest; `small` is more accurate |
| `whisper_compute_type` | `int8` | Quantization — `int8` for CPU, `float32` if you have issues |
| `kokoro_voice` | `af_heart` | TTS voice. See voice options below |
| `greeting` | *Hello. This property is monitored. Please state the purpose of your visit.* | What Gatekeeper says when a visitor is detected |
| `reply_delivery` | *Thank you. Please leave the package at the door.* | Reply to a delivery |
| `reply_sales` | *No solicitation. Please leave the property.* | Reply to a solicitor |
| `reply_maintenance` | *Please wait while I notify the resident.* | Reply to a service visit |
| `reply_generic` | *Thank you. Please wait while I notify the resident.* | Reply to any other answer |
| `reply_no_answer` | *You are being recorded. Please state your purpose or leave the property.* | Reply to silence or a one-word answer |
| `audio_retention_days` | `7` | Delete visitor recordings older than this. `0` keeps them forever |
| `event_log_retention_days` | `30` | Delete daily visit logs older than this. `0` keeps them forever |
| `log_level` | `info` | `debug`, `info`, `warning` or `error` |
| `log_format` | `text` | `text`, or `json` for one JSON object per line. Every line carries the visit's Frigate event id |

## Available voices

| Voice | Description |
|-------|-------------|
| `af_heart` | Female, warm (default) |
| `af_bella` | Female, bright |
| `af_sarah` | Female, natural |
| `am_michael` | Male, neutral |
| `am_adam` | Male, deep |
| `bm_george` | British male |
| `bf_emma` | British female |

## Data storage

All audio clips, transcripts, and logs are stored under the add-on's `/data` directory and persist across restarts and updates.

- `/data/audio/in/` — captured visitor audio clips
- `/data/audio/out/` — synthesized TTS files
- `/data/logs/events.jsonl` — interaction log (JSON Lines)

## First run

On first start, Gatekeeper downloads the Kokoro TTS model weights (~350 MB) and the selected Whisper model. Both are cached in `/data/cache/` so this only happens once. Startup may take a few minutes the first time.

## About the code

The add-on installs the `gatekeeper` Python package from the
[Gatekeeper](https://github.com/Gatekeeper-HA/Gatekeeper) repository, pinned to the release
set by `GATEKEEPER_REF` in `ha-addon/build.yaml`. The Docker Compose rig and this add-on run
the same code; only the defaults differ.
