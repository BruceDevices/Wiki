# ProtoPirate

ProtoPirate is available directly under **RF → ProtoPirate** in the full firmware build. No JavaScript file is required for that menu. It shares Bruce beta's native RF protocol engine with Scan/copy, file handling and the optional JS frontend. Emulation is available by default; sending requires an explicit confirmation.

## Install and open

This page documents the proposed implementation in [firmware PR #2979](https://github.com/BruceDevices/firmware/pull/2979), not a shipped stable release.

Build or install the matching full firmware for your exact board and open **RF → ProtoPirate**. Its menus, text viewer and file picker are Bruce's existing native controls. Native saves go to `/BruceRF/ProtoPirate/` on the selected SD/LittleFS storage. The feature is excluded from LITE builds and works without the JS interpreter.

The existing `sd_files/ProtoPirate.js` remains optional: copy it to `/BruceJS/ProtoPirate.js` on SD and open it through JS Interpreter. It calls the same engine, uses Bruce's native UI bindings and saves to `/subghz/ProtoPirate/` on SD. It requires matching firmware; an old RX1 binary is not sufficient. Both frontends check the 256 KiB input limit before reading a file.

## Shared beta protocol collection

Bruce's beta workflow builds from `dev`; there is no separate `beta` branch. This work uses the current dev baseline below, including its native RF additions. The existing decoder directory contains 83 source/header files, not 83 distinct protocols.

The combined catalogue has **92 unique entries: 66 beta entries and 26 ProtoPirate entries**. The beta entries include the existing registry and dedicated KeeLoq/Chamberlain paths. Each codec implementation exists once: the shared service in `src/modules/rf/protocols/rf_protocol_engine.*` dispatches to the existing beta codecs or the attributed vendor code in `lib/ProtoPirate`. Neither frontend owns a second set of codecs. Keeping the upstream vendor layout makes future updates and attribution reviewable; it does not build another frontend-specific copy.

Native Scan/copy also uses this service. Full PSRAM builds allow 8192 RMT symbols for longer captures, and the capture list has a 200 kB payload limit. The native ProtoPirate receiver adds the four-profile scanning workflow described below; standard Scan/copy retains its existing receive controls.

Ten beta callbacks are placeholders and are correctly listed as receive-only: SecPlus_v2, Somfy_Telis, Somfy_Keytis, CAME_Atomo, Nice_Flor_S and the five TPMS entries. Other existing receive-only entries remain so. For beta encoders, Button/Counter are decoded information rather than editable controls: those encoders consume the saved Key (CAME_Twee consumes Serial). The shared dispatcher passes the full native record to callbacks, preserving those fields. Existing KeeLoq counter regeneration remains in Bruce's established native KeeLoq flow. Supported ProtoPirate variants retain their existing field editing.

## Source baseline

- Bruce `dev`: `dc089b920aab214c32fba89ad863a45c821a1627` (retrieved 2026-10-15).
- Reference: Bruce PR #2961. Its RF/EMV changes were applied onto that newer dev,
  retaining newer dev APIs and board/bus handling.
- RocketGod ProtoPirate 4.0 mirror: `4c9afe58f881a28ddb51015f63b84578e101068d`.
- Compared with the user's Sor3nt ESP32 port branch `pr/protopirate-3.6.3`,
  `4c5e129911607afab93218051f5a4f57a7ac1b06`.
- Original file hashes, licenses, compatibility changes and the reproducible
  protocol patch are in `lib/ProtoPirate`.

## Implemented

The native RF app and optional JS app provide live capture using four standard AM/FM presets, frequency
selection/hopping, decoding saved RAW and decoded `.sub` files, bounded history,
file browsing, saving, timing analysis, whole-stream RAW replay, and protocol
emulation with button/counter editing and an automatically advanced counter for each explicit send, as in the upstream emulation scene. Preview/cancel do not commit counter changes. There are 26 imported decoder modules and
23 encoder modules, alongside the beta catalogue above. An encoder's presence is not evidence of device validation.
Fiat V2, Ford V3 and Scher-Khan have no encoder in the pinned upstream source.

Capture preserves signed timings, multiple RAW lines and truncation metadata.
Truncated captures and known checksum failures are not offered as valid emulation
inputs. RAW replay uses a single transmission, preserving inter-line timing.
Native protocol calls have bounded inputs and scoped allocation cleanup.

## Why Kia V1, Kia V2 and Ford V0 failed

Kia V1/V2 omitted the leading Manchester bit, while their decoders counted the
seeded start bit twice. Ford V0 generated the first Manchester `1` from the wrong
state, losing a bit and shifting the frame. Saved key fields also did not always
follow edited serial/button/counter values. These paths are corrected, field
ranges checked, and encoded key/CRC fields saved consistently.

The regression tests now cover 100 boundary/random records per affected protocol,
including a second cycle with edited button/counter values: 600 encode/decode and
save/reload cycles, 1,806 native calls in one process, with ASan and UBSan. This is
synthetic software verification, not independent physical signal verification.

## RX1: live capture versus Sub Decode

The previous JS loop requested one-second RAW reads and advanced its auto preset
after every returned capture, including noise. RMT completes a capture after the
signal ends (30 ms idle) or its buffer fills. A synthetic Chrysler three-repeat
burst lasts 2,686.8 ms, so the one-second read can discard a still-pending capture.
The first unmatched capture also opened a blocking result menu and stopped
listening. Those are reproducible receive-path defects; without the user's two
RAW recordings we cannot establish which caused their particular failure.

RX1 holds auto profiles for 12 seconds of elapsed time and uses 30-second reads
for a locked profile. Unmatched captures do not advance the preset or open a
menu. Back remains responsive inside the native read; on stopping, the last
unmatched RAW capture is available in History for inspection and saving.
Auto scanning remains sequential and can miss a transmission on another profile
or at a timeout boundary. For a known signal, load its `.sub` file and choose
**Receive matching signal**. That locks the frequency and supported preset from
the file and disables hopping, then starts listening without a nested UI loop.

Sub Decode on a RAW file runs the waveform decoder. Loading an already decoded
key file instead restores its stored fields; that alone is not proof that an
over-air transmission was captured correctly. The receive error text now makes
this distinction and identifies full-buffer captures as potentially incomplete.

The original RX1 revision changed only the JS frontend. The subsequent native UI revision also fixes `menu.show` to keep Bruce's native option loop active while the interpreter is running. Rebuild the matching firmware for that revision. Emulation remains enabled by default; transmission still requires explicit confirmation, with Cancel selected initially.

New checks cover 38 native Chrysler decode calls using independent synthetic
timings, including paired frames, leading noise, 32 jitter cases, multiline RAW,
and rejection of truncated frames and integrity failures. A fake-radio test of
the production receive function reproduces the one-second timeout and successful
longer capture, plus prompt Back during a long read. UI integration tests cover
noise retry, matching-file settings, auto scanning, and retaining/saving unmatched
RAW. The noise-to-Chrysler UI flow also runs in MQuickJS 0.0.6. These tests do not
replace a two-device RF test with the user's actual waveform.

## Current limits

This is not complete Flipper feature parity or a production-certified release.
Device-encrypted keystore loading, brute-force recovery, the model database,
remote analyzer, and F4/Honda-specific custom radio presets are not ported.
Encrypted frames can remain undeciphered. The app reports these limits in About.
The other imported protocols have not all passed independent protocol fixtures.

## Validation

The shared integration passed 55 native API checks under ASan/UBSan, covering the 92-entry catalogue, six ProtoPirate and four beta encode/decode/save round trips, byte-preserved Chrysler 80-bit records and extra metadata, and malformed input rejection. The native RF frontend compiled in the host harness and passed confirmed-send/cancel/history-save flows. The optional JS frontend passed its 12 mocked UI scenarios. These are software checks; the new native menu and combined firmware require physical validation. Previous hardware observations below apply to the earlier RX1 build.


- Six synthetic protocol loopbacks pass: Kia V0/V1/V2, Ford V0, Subaru, Mazda V0.
- Kia V1/V2 and Ford V0: 600 boundary/random/edit/save/reload cycles pass.
- JS frontend tests cover parsing, metadata, edit ranges, errors, default emulation,
  exit/cancel paths, and explicit whole-stream transmission.
- The app syntax also runs in Bruce's MQuickJS 0.0.6 host interpreter.
- RF antenna regression covers five board/module variants and band switching.
- RAW capture regression covers presets, timing preservation, full-buffer metadata,
  event handling and normal versus large capture buffers.
- EMV regression covers synthetic APDU exchanges and 5,000 malformed inputs with
  address/undefined-behavior sanitizers.
- NRF24 implementation, board pin defaults and shared SPI management are inherited
  unchanged from the selected dev baseline. Compilation is not an NRF hardware test.

Before broad publication, test on the exact physical board: cold boot, SD read/write,
CC1101 receive/replay/emulation with an independent receiver, NRF24 communication
with a controlled peer, EMV reading with authorized test cards, and repeated
switching among RF, NRF, NFC/EMV and SD without rebooting. Check cancellation,
low memory, malformed files, long captures, and reconnects. No hardware testing
was available in the build environment.

## Validation provenance

### Reporter hardware confirmation, 2026-10-15

The reporter confirmed that transmission from this Bruce ProtoPirate build was
received by their Flipper running the original ProtoPirate application. The
provided photos show RX1 with Emulation ON, a Chrysler V0 decoded record with
`Chk:OK`, the emulation controls and a second device used in the test. This is
reporter-confirmed interoperability for the tested signal, not a claim that a
vehicle accepted a command or that every protocol was tested. The RF antenna
routing improvement was also previously confirmed on the reporter's T-Embed.
Independent NRF24, final EMV card/bus and full board-matrix checks remain open.

### Historical reproduction commands

At the maintainer's request, the firmware PR no longer adds `tests/` or `docs/`. These commands apply to the [archived pre-cleanup source](https://github.com/salikisazazil/firmware/tree/a87b5132fbf2c2de7dac8e9c55d380fb9b33b365), which retains the validation harnesses. They are not commands for the current cleaned branch.

```sh
python3 tests/protopirate/build_host.py
python3 tests/protopirate/test_engine.py
python3 tests/protopirate/test_protocol_regressions.py
node tests/protopirate/frontend.test.cjs
python3 tests/protopirate/test_chrysler_receive.py /tmp/chrysler-results.json /tmp/chrysler-fixture.json
node tests/protopirate/receive.test.cjs /tmp/chrysler-fixture.json
python3 tests/test_tembed_rf_antenna.py
ASAN_OPTIONS=detect_leaks=0 python3 tests/test_rf_raw_capture.py
ASAN_OPTIONS=detect_leaks=0 python3 tests/test_emv_protocol.py
```

Use the pinned upstream checkout and verified Flipper sources to reproduce imports:
`python3 tools/import_protopirate.py /path/to/ProtoPirate /path/to/flipper-sources`.

## Firmware build result

The following result predates the native UI revision and its `menu.show` fix. It does not certify a firmware binary rebuilt after that change. The native UI revision passes 12 host-mocked UI scenarios and the unchanged protocol engine passes 38 Chrysler decode checks under ASan/UBSan; physical native menu testing and a fresh board build remain required.

The full `lilygo-t-embed-cc1101` firmware compiled and linked successfully.
The merged image is 4,496,576 bytes; the app is 4,431,040 bytes and fits the
4,653,056-byte application partition. Static RAM is 127,168 bytes. Image checksum,
validation hash, bootloader/partition/app offsets, and required ProtoPirate,
NRF24 and EMV symbols were checked. This does not prove peripheral behavior.
Upstream compiler warnings remain, including audio decoder bounds warnings.
They are preserved in the build log and were not treated as resolved here.

For a source build, check out the exact Bruce baseline, initialize its submodules,
apply the supplied patch, and run:

```sh
BRUCE_OFFLINE_WEB_ASSETS=1 python3 -m platformio run -e lilygo-t-embed-cc1101
```

[Build dependency manifest](../interpreter/protopirate-build-dependencies.json) records the versions and Git commits that
were compiled. The supplied binary was built using equivalent verified local
platform/framework archives and installed tools to overcome download failures;
those environment-specific absolute paths are not part of the source patch.

## Maintainer reference

See the [implementation and API reference](../interpreter/protopirate.md) for the source map, limits and validation evidence.
