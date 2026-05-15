# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
