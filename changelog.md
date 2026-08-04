# Changelog (ZappInfinit)

All notable changes to ZappInfinit are documented in this file. Updates are recorded here on every release.

# V0.21.4.0beta

## Build & Packaging
* **UPX Compression Re-enabled:** Re-enabled UPX binary compression in `build.py` (was disabled by `--noupx`). The build now locates the WinGet-installed UPX toolchain and compresses the onedir collection while excluding the Python runtime DLLs (`python314.dll`, `python3.dll`), `.pyd` extension modules, and the `accessible_output2` screen-reader DLLs loaded via `ctypes` — UPX packing corrupts `ZDSRAPI_x64.dll` and crashes the app on startup with an access violation. Smaller installer/portable payload.

## Audio
* **BASS plugin resolution in dev mode:** `_load_bass_plugin()` now also searches `sound_lib`'s own bundled plugin directory (`sound_lib/external/paths.py` x64/x86), so `bass_aac.dll` resolves correctly in dev mode instead of only through the app-specific `client/lib` pair.
* **BASS_ERROR_ALREADY treated as success:** A plugin that is already registered (because `pybassopus`/`pybass_aac` call `BASS_PluginLoad` at import time) no longer logs a misleading "not loaded" warning — the codec is available either way.
* **Dead `bass_opus.dll` fallback removed:** `start()` no longer tries the non-existent `bass_opus.dll` as a second Opus plugin name; only the real `bassopus.dll` is attempted.

---

# V2026.06.21.1555

## Bug Fixes
* **Auto-Updater Path Warning:** Fixed the incorrect warning log statement indentation in `updater.py` so it only runs on unsupported platforms.
* **Process Tree Cleanup:** Modified `real_exit()` and `_stop_evolution()` to explicitly kill the entire WPPConnect Server Node.js/Chromium process tree on Windows using `taskkill /F /T`, preventing orphaned processes and releasing all file locks for auto-updater overwrites.
* **PTT Audio Playback (Opus):** Loaded the `bassopus` and `bass_aac` plugins during BASS startup in `sound_system.py` to support playing WhatsApp Opus-encoded voice notes (`.ogg`) and AAC attachments.
* **Message Status Filtering:** Fixed `_map_status()` to display status ticks (sent, delivered, read) only on messages sent by you (`fromMe`). Incoming received messages will no longer display these ticks (unless the audio was played).

---

# V2026.06.21.1450

## Upstream Synchronization & Merge
* **PyQt/wxPython UI Enhancements:** Merged client UI enhancements, including the new playing voice note audio controls visualization in the conversations list.
* **WPPConnect Mentions Integration:** Integrated the new `@mention` suggestion panel and `mentioned_jids` arguments in `send_text_message` with WPPConnect Server's `/api/:session/send-mentioned` routing (with robust fallback to standard sending on failure).
* **Silent Disconnection Handling:** Replaced blocking error popups during transient network disconnections with silent status bar notifications and automatic Socket.IO reconnection loops to prevent UI freezes.
* **Debounced Data Saves:** Coalesced rapid message writes using a thread-safe `_save_lock` and a `150ms` debounced timer (`_schedule_save`) to prevent `messages.dat` file corruption during bulk syncs.
* **Accessibility Overrides (NVDA):** Preserved local list focus and selection guards (e.g. clearing focus state before DeleteAllItems) to prevent stuttering and COM errors on screen readers.

---