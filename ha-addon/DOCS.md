# Gatekeeper AI Doorbell

AI-powered doorbell assistant that greets visitors via speaker, listens to their response, classifies their intent, and replies — all processed locally on your hardware. It notifies you with what they said and a snapshot, and shows up in Home Assistant as a device.

## Requirements

- **Frigate** (the Frigate add-on, or Frigate elsewhere) for person detection. Its bundled **go2rtc** provides the camera streams and the talkback to the camera speaker.
- **An MQTT broker.** The **Mosquitto broker** add-on is picked up automatically, login included.
- A camera with RTSP audio and a speaker with talkback (RTSP backchannel), e.g. a Reolink doorbell.

## go2rtc setup

In Frigate's config, define a talkback stream with `backchannel=1` next to the camera stream:

```yaml
go2rtc:
  streams:
    front_door: rtsp://user:pass@192.168.1.100:554/h264Preview_01_main#backchannel=0
    front_door_talk: rtsp://user:pass@192.168.1.100:554/h264Preview_01_main#backchannel=1
```

The stream names must match `camera_name` and `go2rtc_talk_stream`.

## Reaching Frigate and go2rtc

The defaults point at the **Frigate add-on**, whose hostname on Home Assistant's internal network is `ccab4aaf-frigate`:

| Option | Default |
|--------|---------|
| `audio_rtsp_url` | `rtsp://ccab4aaf-frigate:8554/front_door` |
| `go2rtc_api` | `http://ccab4aaf-frigate:1984` |
| `frigate_api` | `http://ccab4aaf-frigate:5000` |

For other Frigate installs, replace `ccab4aaf-frigate` in all three:

- **Frigate (Full Access) add-on:** `ccab4aaf-frigate-fa`
- **Frigate Beta add-on:** `ccab4aaf-frigate-beta`
- **Frigate on another machine:** its IP address, with whatever ports it publishes for go2rtc's API (1984), go2rtc's RTSP (8554) and Frigate's API (5000).

The add-on's hostname is shown on the Frigate add-on's page under *Hostname*.

## MQTT

With the Mosquitto broker add-on installed, Gatekeeper uses it with its own login; leave `mqtt_host` empty. For an external broker, set `mqtt_host`, `mqtt_port`, `mqtt_username` and `mqtt_password`.

Gatekeeper reads Frigate's events and publishes (retained) `gatekeeper/status`, `gatekeeper/<camera>/state` and `gatekeeper/<camera>/visit`. With Home Assistant's MQTT integration, a **Gatekeeper** device appears automatically, with a *Last visitor* sensor (classification, with the transcript and reply as attributes) and a *Conversation* binary sensor.

## Notifications

Set `ntfy_url` (and `ntfy_topic`, `ntfy_token` if your server needs them) to get a notification as soon as the visitor's answer is classified: what they said, Gatekeeper's reply, and Frigate's snapshot of them. Visits Gatekeeper couldn't talk to, and doorbell presses during a visit, are notified too. Any [ntfy](https://ntfy.sh) server works: a self-hosted one, or ntfy.sh (which then sees the snapshots and transcripts).

## Doorbell button

Set `reolink_host`, `reolink_username` and `reolink_password` for a Reolink doorbell, and Gatekeeper reacts to its button. It logs in over HTTPS and listens for the camera's push events on port 9000. A press greets the visitor right away, even before Frigate has detected them. A press during a visit or just after it sends a *Doorbell pressed* notification and says `reply_pressed`.

## Zones

To ignore people on the sidewalk, draw a zone in Frigate (e.g. `porch`) and set `trigger_zones: porch`. Only people who enter one of the listed zones are greeted, and the dwell time counts from entering it. Doorbell presses always count.

## Configuration

| Option | Default | Description |
|--------|---------|-------------|
| `camera_name` | `front_door` | Must match the camera name in your Frigate config |
| `mqtt_host`, `mqtt_port`, `mqtt_username`, `mqtt_password` | *(from the Mosquitto add-on)* | Only for an external broker |
| `mqtt_topic` | `frigate/events` | Frigate event topic. Leave as is unless you changed Frigate's topic prefix |
| `audio_rtsp_url` | `rtsp://ccab4aaf-frigate:8554/front_door` | go2rtc stream for recording the visitor's answer |
| `go2rtc_api` | `http://ccab4aaf-frigate:1984` | go2rtc API base URL |
| `go2rtc_talk_stream` | `front_door_talk` | go2rtc stream name for talkback |
| `frigate_api` | `http://ccab4aaf-frigate:5000` | Frigate API, for snapshots in notifications |
| `trigger_zones` | *(none)* | Comma-separated Frigate zones a person must enter to be greeted |
| `dwell_seconds` | `1` | Seconds a person must be visible before triggering |
| `listen_seconds` | `4` | How long to listen after the greeting |
| `session_ttl_seconds` | `120` | Forget a Frigate event this long after its last update |
| `sweep_interval_seconds` | `1` | How often sessions are checked for dwell and expiry |
| `cooldown_seconds` | `90` | After a visit, new person detections this soon are merged into it instead of greeting again |
| `visit_timeout_seconds` | `90` | Give up on a visit (greet, listen, reply) after this long |
| `whisper_model` | `tiny` | Whisper STT model. `tiny` is fastest; `base.en` hears noticeably better (about twice the CPU time) |
| `whisper_compute_type` | `int8` | Quantization: `int8` for CPU, `float32` if you have issues |
| `kokoro_voice` | `af_heart` | TTS voice. See voice options below |
| `greeting` | *Hello. This property is monitored. Please state the purpose of your visit.* | What Gatekeeper says when a visitor is detected |
| `reply_delivery` | *Thank you. Please leave the package at the door.* | Reply to a delivery |
| `reply_sales` | *No solicitation. Please leave the property.* | Reply to a solicitor |
| `reply_maintenance` | *Please wait while I notify the resident.* | Reply to a service visit |
| `reply_generic` | *Thank you. Please wait while I notify the resident.* | Reply to any other answer |
| `reply_no_answer` | *You are being recorded. Please state your purpose or leave the property.* | Reply to silence or a one-word answer |
| `reply_pressed` | *The resident has already been notified.* | Said when the doorbell is pressed during a visit or right after one |
| `ntfy_url`, `ntfy_topic`, `ntfy_token` | *(off)*, `doorbell`, *(none)* | ntfy notifications |
| `reolink_host`, `reolink_username`, `reolink_password` | *(off)* | Reolink doorbell button presses |
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

Audio clips, transcripts and logs are stored in the add-on's `/data` directory, which persists across restarts and updates:

- `/data/audio/in/`: recordings of visitors' answers (kept `audio_retention_days`)
- `/data/audio/out/`: synthesized speech
- `/data/logs/events.jsonl`: today's visit log (JSON Lines). Earlier days are rotated to `events-YYYY-MM-DD.jsonl` and kept `event_log_retention_days`.

## First run

On first start, Gatekeeper downloads the Kokoro TTS model weights (~350 MB) and the selected Whisper model, cached in `/data/cache/`, and pre-synthesizes its phrases. Startup can take several minutes on small CPUs. The add-on reports healthy once it's connected to MQTT and ready.
