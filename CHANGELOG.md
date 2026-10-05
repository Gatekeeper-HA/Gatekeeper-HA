# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- **Contributing guide and CLA:** `CONTRIBUTING.md`, and a GitHub workflow that asks
  first-time contributors to agree to the Contributor License Agreement (kept in the
  Gatekeeper repository, with Lobo Dorado LLC) and records the agreement on the
  `cla-signatures` branch.

### Changed
- **Docs, from installing on a clean Home Assistant OS:**
  - A complete minimal Frigate config: the MQTT login from the Mosquitto add-on,
    `detect: enabled: true` (without it Frigate detects nothing and nobody is greeted),
    `#backchannel=0` on the camera stream, and placeholders that are clearly placeholders.
  - Set up the MQTT integration Home Assistant discovers, or no Gatekeeper device appears.
  - Install builds on your machine and can take over an hour on slow hardware; don't click
    *Install* again when the button comes back.
  - Notifications and the doorbell button are off until their options are set.
  - How to tell when it's ready, and a troubleshooting section.

## [0.2.0-rc.1] - 2026-10-04

Phase 0 release candidate. The add-on now builds, installs the same tested `gatekeeper`
package as the compose stack (pinned to Gatekeeper `v0.2.0-rc.1`), and works with the Frigate
and Mosquitto add-ons out of the box. See the
[Gatekeeper changelog](https://github.com/Gatekeeper-HA/Gatekeeper/blob/v0.2.0-rc.1/CHANGELOG.md)
for what Gatekeeper itself gained: hearing the visitor's answer, notifications, the doorbell
button, zones, a second listening turn, health checks and retention.

### Fixed
- **The add-on could not build.** `build.yaml` used a `base-python:3.11` tag that doesn't
  exist, and the Python base images are Alpine, which has no `apt-get` and no wheels for
  torch or ctranslate2. It now builds on the Debian (bookworm) base, pinned to `2026.08.0`.
- **Defaults reach the Frigate add-on** (`ccab4aaf-frigate`) instead of `localhost`, which
  another add-on can't reach.
- **MQTT logs in:** it uses Home Assistant's MQTT service (the Mosquitto add-on) and its
  login by default. Before, it connected anonymously, which the Mosquitto add-on rejects.
- **Config cleanup:**
  - `url` points to this repository.
  - Config the add-on linter rejects was removed: the default `startup`/`boot` values and an
    invalid `map`.
  - Unset optional options are no longer passed on as the text "null".

### Added
- **Options:**
  - Notifications via ntfy: `ntfy_url`, `ntfy_topic`, `ntfy_token`, `frigate_api`.
  - The Reolink doorbell button: `reolink_host`, `reolink_username`, `reolink_password`.
  - `trigger_zones`.
  - Every reply text, including `reply_pressed`.
  - `cooldown_seconds`, `visit_timeout_seconds` and the session timings.
  - `audio_retention_days` and `event_log_retention_days`.
  - `log_level` and `log_format`.
  - For an external broker: `mqtt_host`, `mqtt_port`, `mqtt_username`, `mqtt_password`.
  - The English-only Whisper models (`tiny.en`, `base.en`, `small.en`).
- **Health:** a Docker `HEALTHCHECK` on Gatekeeper's `/healthz`, so the Supervisor restarts
  a hung add-on.
- **Store polish:** option translations, plus a placeholder icon and logo.
- **CI:** the HA app linter and ShellCheck.

### Changed
- **No more copied code:** the add-on installs the shared `gatekeeper` package from the
  Gatekeeper repository at the release set by `GATEKEEPER_REF` in `build.yaml`, with
  pinned dependencies.
- **Docs:** `DOCS.md` is the full reference (reaching Frigate, MQTT, notifications, the
  button, zones, every option); the README is a short install guide.

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
