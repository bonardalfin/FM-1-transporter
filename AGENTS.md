# AGENTS.md

Firmware (RP2040/RP2350, Pico SDK) and tools to read and write the flash of an M-VAVE FM-1 (JieLi AC791N / WL82) through PIO USB host. See `README.md`, `docs/ARCHITECTURE.md`, `docs/PROTOCOL.md`.

## Layout

- `src/`: firmware. Core 1 runs the PIO USB host and JieLi protocol, core 0 the Mac-facing CDC.
- `pio/usb_key.pio`: USB_KEY entry sequence.
- `lib/`: Pico-PIO-USB submodule plus patches applied to a copy at configure time. Do not edit the submodule.
- `tools/fm1t.py`: CLI client. `web/index.html`: Web Serial client. Both speak the text protocol in `src/transporter_proto.h`.

## Build

```bash
git submodule update --init
cmake -S . -B build -DPICO_SDK_PATH=$HOME/pico-sdk -DPICO_BOARD=seeed_xiao_rp2040
make -C build -j8 fm1_transporter
```

`PICO_BOARD=pico` or `pico2` uses the same pins (D+ GP0, D- GP1). Debug Probe: `-DFM1T_BOARD=debugprobe -DPICO_BOARD=pico`. Releases are built by `.github/workflows/release.yml` on `v*` tags. Builds must stay warning-free.

## Rules

- Never commit `wl82loader.bin` or embed it in firmware or releases. It is uploaded at run time (`ldr` command, `fm1t.py --loader`).
- Flash writes are limited to whole 4 KiB sectors in `[0x4000, 0x93000)`, enforced in firmware. Do not widen this, and add no block or chip erase.
- Changes to writing must keep: backup first, only changed sectors, per-sector read-back, final full read.
- No hardware is available in CI or agent sessions. State what was and was not tested on real hardware.
- Protocol changes: update `src/transporter_proto.h`, `docs/PROTOCOL.md`, `tools/fm1t.py` and `web/index.html` together.
- Keep comments and docs short. Docs in English; README stays bilingual.
- Commit with the user's own identity. No AI attribution, no Co-authored-by trailers.
