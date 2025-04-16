---
title: Speaches Demo
emoji: 🎙️
colorFrom: purple
colorTo: green
sdk: docker
pinned: false
license: mit
short_description: Showcasing the Speech-to-Text functionality of "Speaches"
preload_from_hub:
  - Systran/faster-whisper-medium.en
  - Systran/faster-whisper-medium
  - Systran/faster-whisper-base.en
  - Systran/faster-whisper-base
  - Systran/faster-whisper-small.en
  - Systran/faster-whisper-small
  - Systran/faster-whisper-tiny.en
  - Systran/faster-whisper-tiny
  - Systran/faster-whisper-large-v3
---


### Note about hosting this demo in HF spaces

By default, the demo throws a 301 HTTP error, because HTTP requests are redirected to HTTPS by HF.
This can be solved by adding in the HF space settings the environment variable `LOOPBACK_HOST_URL=https://<user-name>-<space-name>.hf.space` and speaches will use this var in its config to set the base URL for requests.
