# ProtoPirate implementation and API

This documents [firmware PR #2979](https://github.com/BruceDevices/firmware/pull/2979), not a released feature. For installation and use, see [ProtoPirate](../features/protopirate.md).

The PR consolidates #2961 and its #2962 main backport, then adds the ProtoPirate
4.0 app and RX1 corrections, followed by native RF menu/shared-protocol integration. The starting commit is current upstream
`dev`, `dc089b920aab214c32fba89ad863a45c821a1627`, not either older PR head.
The latest dev board, platform, pins and shared-bus implementation are retained.

## Shared RF integration

The full build exposes **RF → ProtoPirate** without requiring JS. `rf_protopirate.cpp` uses native `loopOptions`, `ScrollableTextArea` and filesystem helpers. It includes live reception, profile/frequency settings, file decoding, bounded history, timing analysis, save, RAW replay, encoder preview and explicit transmission.

`rf_protocol_engine.*` is the single dispatch/serialization service used by RF Scan/copy, native file send/load, the native app and `protopirate_js.cpp`. The 92-entry catalogue combines 66 beta entries and 26 vendor entries without copying protocol implementations. `rf_codec.*` extracts the existing pure beta codec functions from hardware-bound RMT sources, preserving their implementations. The upstream ProtoPirate C sources remain in `lib/ProtoPirate` for pinned imports and attribution.

Extended vehicle records retain their complete serialized `.sub` and display text in `RfCodes`, avoiding the legacy 64-bit Key / 16-bit Counter limits. Native callbacks receive all existing fields, including Serial for CAME_Twee. The ten existing stub encoder callbacks are registered as receive-only. Native frames return `fieldEditing:false`: changing their decoded Button/Counter alone would not regenerate the Key. The established KeeLoq counter flow is retained. Supported vendor encoders retain their field-editing behavior.

Full PSRAM builds capture up to 8192 RMT symbols in native Scan/copy too; retained capture payloads are bounded to 200 kB. LITE retains beta-only dispatch and its smaller Scan/copy capture buffer. Protocol lookup, decode and encode do not transmit. `rf_protocol_radio.cpp` provides the shared checked RAW transmit boundary and propagates actual success/failure.

The integration passed 55 ASan/UBSan API checks (92 unique entries; ten protocol round trips; Chrysler 80-bit save preservation; malformed input rejection), native frontend host compilation/send/cancel/history flows, and the existing 12 JS UI scenarios. Full firmware and physical verification are reported separately; old binary measurements do not describe this integration.

## Change map

| Area / files | Problem and resulting behavior |
| --- | --- |
| `src/modules/rf/rf_utils.*` | Include the antenna-switch block for `T_EMBED_1101` as well as legacy `T_EMBED`; retain the existing CS guard and 315/433/868/915 MHz routing. Preserve an already initialized radio when setting up RX; return allocation failure instead of aborting. |
| `src/modules/rf/protocols/rf_decoder.*` | RX sessions accept a bounded capture size. JS RAW requests 8192 RMT symbols (up to 16384 durations); normal RX remains 256 symbols. Preserve full-buffer reporting, cleanup and the existing M5 GPIO receiver behavior. |
| `src/modules/rf/rf_scan.*`, `rf_raw_capture.h` | Apply and verify named CC1101 receive presets after initialization. Preserve signed durations without repeat normalization, split RAW lines at 512 values, report preset/register/full-buffer metadata, poll promptly, use wrap-safe timeouts and preserve Back for JS. |
| `src/modules/rf/protocols/rf_encoder.cpp` | Derive the RMT wait from the complete waveform duration plus margin instead of cutting long transmissions off at two seconds. Bound the wait to 60 seconds. |
| `src/modules/rfid/emv_protocol.*` | Portable bounded BER-TLV/APDU parser; discover variable-length AIDs and priorities, build dynamic PDOL, handle GPO 80/77, traverse AFL ranges, decode PAN/track-2 with padding, and handle SW61/SW6C with bounded retries. Optional cardholder/date fields do not prevent a successful read. |
| `src/modules/rfid/emv_reader.*` | Integrate the parser with PN532 I2C/SPI. Validate normal/extended frames, lengths and checksums; use the actual ISO14443-4 target; avoid response-length truncation and fragmented I2C status handling. Add bounded cancellation, ACK abort, field shutdown, adapter cleanup, scrollable results and explicit saves. Do not automatically print card details to Serial. |
| `lib/ProtoPirate/` | Pinned original protocol sources, compatibility layer, registry, scoped memory cleanup and JSON API. 26 decoder modules and 23 encoders; original licenses, file hashes and a reproducible local patch are included. |
| `src/modules/bjs_interpreter/protopirate_js.cpp`, `subghz_js.*` | Add the protocol API and complete-stream RMT transmit bridge. Validate frequency/preset, duration counts, ranges and duplicate fields; return actual TX status and release the radio. Existing raw-pulse TX APIs remain available. |
| `mqjs_stdlib.c`, `mqjs_stdlib.h`, `lib/mquickjs_headers/mqjs_stdlib.build.sha256` | Register new JS functions and regenerate the matching interpreter table/hash. Generated-header index changes account for much of that diff. |
| `sd_files/ProtoPirate.js` | ES5 frontend using native `menu.show`, `dialog.viewText`, `dialog.createTextViewer`, messages and `dialog.pickFile` for live receive, RAW/key-file decoding, history, SD browsing/save, timing analysis, original RAW replay and emulation with button/counter editing. Emulation is available by default; transmitting is explicit and confirmation defaults to Cancel. |
| `storage_js.cpp` | Validate arguments, accept string/typed-array data, retain bytes across path-conversion allocations/GC, and return write/flush failure rather than unconditional success. |
| `interpreter.cpp` | Restore navigation footer labels after clearing a touch display for the script. |
| `patch.py` | Resolve installed unified or legacy ESP32 objcopy tools and verify patch success before moving the original archive. Bound external minifier requests and fall back to original functional assets; `BRUCE_OFFLINE_WEB_ASSETS=1` allows local gzip without that service. |
| `tools/import_protopirate.py`, `THIRD_PARTY.md`, vendor manifests/licenses | Pinned imports and reproducible attribution remain with the source. Firmware `tests/` and `docs/` additions are removed on review; usage/API/provenance documentation belongs in this Wiki. |
| `src/modules/bjs_interpreter/menu_js.cpp` | Pass interpreter context into `loopOptions` so `menu.show` remains interactive while JS runs, instead of returning the first option immediately. |

## Protocol and RX1 corrections

Kia V1/V2 now emit their leading Manchester bit and count the seeded decoder bit
correctly. Kia V1 retains the full 57-bit key. Direct field handling supports the
full serial range, validates button/counter bounds and updates saved Key/CRC/
RawCnt fields consistently. Ford V0 starts Manchester encoding in the proper
state, reserves the needed upload capacity, validates its header/button/counter
fields and saves the rebuilt key. Subaru's signed-shift undefined behavior is
removed. These changes are isolated in `lib/ProtoPirate/bruce-protocol-fixes.patch`.

RX1 fixes the one-second capture timeout and the blocking result menu opened by
the first unmatched/noise capture. A synthetic three-repeat Chrysler waveform
lasts 2686.8 ms, before RMT's 30 ms idle completion. Auto profiles now dwell for
12 seconds by elapsed time; locked profiles use 30-second reads. Noise does not
advance the profile. Back stops receiving and keeps the last unmatched RAW in
History. **Receive matching signal** loads the file's frequency/preset, disables
hopping and begins receiving. Auto scanning is sequential and can still miss a
signal on another profile or across a timeout boundary.

## JavaScript contract and limits

All protocol functions are on `require('subghz')`. `protoInfo`, `protoDecode` and
`protoEncode` return JSON strings; the frontend parses and checks `ok`. Errors
have `ok:false` and `error`. `protoTransmit` returns a boolean.

| Function | Contract |
| --- | --- |
| `readRaw(timeoutSeconds=10, rxPreset='')` | Existing one-argument callers retain their default profile. Timeout is 1-60 seconds. Named presets require CC1101. Returns a RAW `.sub` string or an empty string. |
| `protoInfo()` | API version 1, pinned upstream revision, development status, emulation enabled, keystore availability and per-protocol encoder availability. |
| `protoDecode(subText)` | RAW pulse decoding or deserialization of an existing decoded key file. Returns frames with protocol, text, data, encoder, validation and optional fieldEditing, plus result-limit/serialization status. Loading a key file does not verify an over-air waveform. |
| `protoEncode(decodedSubText)` | Generates bounded RAW and updated decoded fields without touching the radio. The frontend advances the counter for an explicit send; preview/cancel do not commit it. |
| `protoTransmit(rawSubText)` | One complete RMT transaction across all RAW lines. Rejects full-buffer captures, unsupported presets, invalid frequencies and malformed/oversized timings. |

Decode input is limited to 256 KiB, encode input to 16 KiB, durations to 16384,
each duration to 1,000,000 us and encoded/transmitted total time to 10 seconds.
At most 16 unique decoded records are returned. Native protocol allocations are
bounded to 512 active allocations / 1 MiB with C-only error unwinding and cleanup.
The vendor scoped allocator and upload buffer are **not reentrant**. The shared service uses an atomic guard: a concurrent vendor call fails with a busy error instead of entering the allocator twice. RF hardware remains owned by the active UI operation. Reported
validation can be `not-verified`; an encoder module is not proof of integrity or
acceptance by a real receiver.

The app offers AM650/AM270 and FM476/FM238 live profiles. Older Bruce firmware
without these native functions must be upgraded with the app; the app displays
a compatibility error. The native RF integration requires rebuilding the firmware; copying a JS file cannot add the RF menu entry. Native JS bindings retain the existing
`LITE_VERSION` / `DISABLE_INTERPRETER` build guards.

## Native UI review revision

The custom drawing, scrolling and navigation loops have been removed from the JS frontend. Native menus start at index zero, so the frontend rotates the requested default to the first position and maps the result back. Cancel remains the default for replay/emulation confirmations; preview and cancel do not commit counter changes. Native file selection checks file size before `storage.read`. Receive status uses a nonblocking native TextViewer that is closed after drawing.

Twelve host-mocked UI scenarios passed: exit, picker cancel, oversized file rejection, default cancel, Back cancel, preview, explicit send, noise retry, profile scanning, file-matched receive, unknown-history save and missing-firmware handling. The unchanged engine also passed 38 Chrysler decode calls under ASan/UBSan. These checks do not establish physical RF, display or peripheral behavior. A fresh full firmware build and hardware validation of the native UI remain outstanding.

## Evidence and outstanding work

- Before the native UI review revision, the full T-Embed CC1101 non-LITE build succeeded. Application: 4,431,040 bytes in a
  4,653,056-byte partition; merged image: 4,496,576 bytes; static RAM: 127,168 bytes.
  Image hash, offsets and required ProtoPirate/NRF/EMV symbols were checked.
- Six synthetic protocol loopbacks pass. Kia V1/V2 and Ford V0 pass 600
  boundary/random/edit/save/reload cycles (1806 API calls in one process).
- Chrysler passes 38 native decode calls including paired frames, multiline RAW,
  leading noise, 32 jitter cases and rejection of truncated/invalid frames.
- RF tests cover five board/module configurations, RAW preset/timing preservation,
  buffer metadata, timeout reproduction and prompt Back. EMV tests include 5000
  malformed inputs and synthetic APDU/PN532 exchanges under ASan/UBSan.
- Frontend tests cover four receive flows, save/reload metadata, counter editing,
  default emulation, cancel/explicit TX and firmware compatibility. The complete
  noise-to-Chrysler UI flow runs in actual MQuickJS 0.0.6 with a 512000-byte limit.
- On 2026-10-15 the reporter confirmed Bruce transmission was received on their
  Flipper running original ProtoPirate. RF antenna improvement was also previously
  reporter-confirmed. This is evidence for the tested signal, not all protocols.

NRF24 implementation and board/shared-SPI defaults are inherited from this dev
baseline. NRF24 physical switching/communication, final EMV card/bus checks and
the full board matrix have not been independently verified here. Host tests do
not replace physical RF or card tests. Upstream build warnings remain, including
audio decoder bounds warnings; this PR does not claim they were fixed.

Full Flipper parity is not claimed: encrypted device keystores, custom F4/Honda
profiles, brute-force recovery, model database and remote analyzer are not ported.
Fiat V2, Ford V3 and Scher-Khan have no encoder in the pinned upstream source.
See the [user guide](../features/protopirate.md) for archived reproduction commands and the [build dependency manifest](protopirate-build-dependencies.json) for the original compiled dependencies. No firmware binaries or machine-specific
PlatformIO paths are committed.
