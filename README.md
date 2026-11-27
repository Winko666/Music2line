# Music2line

为小爱音箱设计的独立歌词屏，基于 RT-Thread Titan Board Mini，计划通过麦克风识曲并持续校准歌词进度。[中文项目说明与硬件清单](README.CN.md)

Music2line is a standalone lyrics display for a Xiaomi XiaoAI speaker. Ask XiaoAI to play a song as usual. The speaker plays the music while a nearby Titan Board Mini listens, looks up timed lyrics, and highlights the line being sung. The device is designed to run on USB power with a network connection.

**Project status** — Hardware design. The BetterLyrics desktop source in this repository is a reference for lyric search, parsing, and display. Titan Mini firmware, online service integration, and on-device synchronization tests are planned.

## How the display will follow a song

1. **Look up lyrics when the listener requests a song.** Transcribe the spoken request using an online service, extract the title and artist, and look for line-timed lyrics such as LRC.
2. **Identify the recording when playback starts.** Capture a short sample from the speaker. The recognition service needs to return both the track and the sample’s position within it. This helps select lyrics for the version XiaoAI actually played, including live recordings.
3. **Correct the timing during playback.** Advance the lyrics using the board’s clock between samples. Use each reliable match and its capture time to estimate the current position, accounting for network delay. Ease in small corrections, locate the track again after a pause, and fetch new lyrics when the track changes. Incorrect timestamps in the lyric file require a corrected or replacement file.

## Prototype hardware

| Part | Quantity | Purpose |
| --- | --- | --- |
| Xiaomi XiaoAI speaker | 1 | Handles the wake word and music playback. |
| RT-Thread Titan Board Mini (Renesas RA8P1) | 1 | Runs RT-Thread, captures speech and music with its onboard PDM microphone, and drives the lyrics screen with LVGL. |
| Mini-compatible RGB565 LCD and matching ribbon cable | 1 set | A 4.3-inch panel is an option for desktop viewing. Check the Mini pinout, panel timing, and any required adapter before purchase. |
| ESP32-C3 UART Wi-Fi module | 1 | The proposed wireless design uses a module with an antenna and ESP-AT firmware to connect the Titan Mini to online services. |
| 5 V USB supply and USB-C cable | 1 set | Size the supply for the combined load of the board and display. |

A wired build can use a Mini-compatible FPC-to-RJ45 adapter and Ethernet cable in place of the Wi-Fi module. For development, a J-Link with RA8P1 support can flash and debug the board over SWD. [Titan Mini board support package](https://github.com/RT-Thread-Studio/sdk-bsp-ra8p1-titan-mini) · [Espressif ESP-AT guide](https://docs.espressif.com/projects/esp-at/en/latest/esp32c3/Get_Started/What_is_ESP-AT.html)

## Prototype validation

The first test will use studio and live recordings of the same song to check which lyrics the display selects and when it highlights the first line. Pause, resume, and track changes will then be used to measure how long the display takes to recover synchronization. Speech transcription, timestamped music recognition, and timed lyric providers are still to be selected.

## Credits and license

Thanks to the BetterLyrics contributors. This repository includes their .NET desktop source as a reference for the embedded design. See the repository’s GNU GPL v3 license text. [Reference source](src/BetterLyrics.DotNet/) · [Upstream BetterLyrics project](https://github.com/jayfunc/BetterLyrics) · [License](LICENSE)
