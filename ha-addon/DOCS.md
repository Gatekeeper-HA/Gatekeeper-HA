# Gatekeeper AI Doorbell

AI-powered doorbell assistant that greets visitors via speaker, listens to their response, classifies their intent, and replies — all processed locally on your hardware.

## Requirements

- **Frigate** — for person detection (publishes events to MQTT)
- **go2rtc** — for WebRTC talkback to your camera speaker
- **Mosquitto** (or any MQTT broker) — for Frigate events
- A camera with RTSP audio support and a speaker/talkback capability

## go2rtc setup

You need a talkback stream defined in your `go2rtc.yaml`. Example:

```yaml
streams:
  front_door:
    - rtsp://user:pass@192.168.1.100/stream1
  front_door_talk:
    - rtsp://user:pass@192.168.1.100/backchannel
```

The `audio_rtsp_url` option should point to the stream go2rtc exposes for listening (e.g. `rtsp://localhost:8554/front_door`).

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

All audio clips, transcripts, and logs are stored in the add-on's `/data` directory, which persists across restarts and updates.

- `/data/audio/in/` — captured visitor audio clips
- `/data/audio/out/` — synthesized TTS files
- `/data/logs/events.jsonl` — interaction log (JSON Lines)

## First run

On first start, Gatekeeper will:
1. Download the Kokoro TTS model weights (~350 MB) and Whisper model — this only happens once, they are cached in `/data/cache/`
2. Pre-synthesize all fixed responses so playback is instant

Startup may take a few minutes the first time.
