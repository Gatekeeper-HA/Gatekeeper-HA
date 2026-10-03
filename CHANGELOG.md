# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added (Phase 0 M3)
- MQTT: uses Home Assistant's MQTT service (the Mosquitto add-on) with its login by
  default (`services: mqtt:want`); `mqtt_host`/`mqtt_port`/`mqtt_username`/`mqtt_password`
  override it for an external broker.
- Options for notifications (`ntfy_url`, `ntfy_topic`, `ntfy_token`, `frigate_api`), the
  Reolink doorbell button (`reolink_host`, `reolink_username`, `reolink_password`) and
  `trigger_zones`.
- `reply_pressed` (said on a doorbell press during a visit's cooldown), and the English-only
  Whisper models (`tiny.en`, `base.en`, `small.en`).

### Changed (Phase 0 M3)
- Defaults now reach the Frigate add-on (`ccab4aaf-frigate`) instead of `localhost`, which
  another add-on can't reach. `DOCS.md` covers other Frigate installs.
- Unset optional options are no longer passed to Gatekeeper as the text "null".
- `DOCS.md` is the full reference; the README is a short install guide.

### Added (Phase 0 M2)
- Options: `cooldown_seconds`, `visit_timeout_seconds`, `audio_retention_days` and
  `event_log_retention_days`.
- A Docker `HEALTHCHECK` on Gatekeeper's `/healthz` (port 8099), which the Supervisor
  uses to restart a hung add-on.

### Fixed
- The add-on could not build: the `base-python:3.11` image tag does not exist, and the
  Python base images are Alpine, which has no `apt-get` and no wheels for torch or
  ctranslate2. It now builds on the Debian (bookworm) base, pinned to `2026.08.0`.
- `url` now points to this repository.
- Removed config the add-on linter rejects: default `startup`/`boot`, and the invalid
  `map: data:rw` (an add-on's `/data` is always mounted).

### Changed
- The app code is no longer copied here. The add-on installs the shared `gatekeeper`
  package from the Gatekeeper repository, with pinned dependencies (`GATEKEEPER_REF` in
  `build.yaml`).
- Logging goes through the `logging` module, with the visit's Frigate event id on every line.

### Added
- Options: `session_ttl_seconds`, `sweep_interval_seconds`, the five reply texts,
  `log_level` and `log_format`.
- Option translations (`translations/en.yaml`), plus placeholder `icon.png` and `logo.png`.
- CI: add-on linter and ShellCheck.

## [0.1.0] - 2026-05-14

### Added
- Initial release as a Home Assistant add-on
- Kokoro TTS for natural-sounding voice responses (pre-synthesized at startup for instant playback)
- faster-whisper STT with built-in VAD filter for visitor transcription
- WebRTC talkback via go2rtc for greeting and reply playback
- Keyword-based visitor classification (delivery, sales, maintenance, generic, no answer)
- TALKBACK_LOCK to prevent concurrent go2rtc session conflicts
- Session management with configurable dwell time before triggering
- Persistent storage of audio clips, transcripts, and event log under `/data`
- Configurable greeting, voice, Whisper model, listen duration, and camera settings via HA UI
- Multi-arch support (amd64, aarch64)
