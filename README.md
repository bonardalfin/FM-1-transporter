# FM-1 Transporter

Read and write the flash of the **M-VAVE FM-1** (JieLi AC791N / WL82) from a computer, through a small RP2040/RP2350 board wired to the FM-1's USB lines.

FM-1 の USB 信号線につないだ小型ボードを使い、PC から FM-1 の Flash を読み書きするツールです。

```
Computer ──USB──► board ──D+ / D- / GND──► FM-1
```

## Hardware / ハードウェア

Three wires only. **Do not connect VBUS**; the FM-1 runs on its battery.
配線は 3 本だけです。**VBUS は接続しないでください。**

| Board / ボード | D+ | D- | Status / 状態 |
|---|---|---|---|
| Seeed XIAO RP2040 | D6 (GP0) | D7 (GP1) | ✅ Recommended / 推奨 |
| Raspberry Pi Debug Probe (UART connector) | TX (GP4) | RX (GP6) | ✅ Tested / 動作確認済み |
| Raspberry Pi Pico | GP0 | GP1 | ⚠️ Builds, untested / 未検証 |
| Raspberry Pi Pico 2 | GP0 | GP1 | ⚠️ Builds, untested. May need external 15 kΩ pull-downs on D+/D- (RP2350 E9) / 未検証。外付け 15 kΩ プルダウンが必要な場合あり |
| Waveshare RP2350-USB-A | | | ❌ Not supported / 非対応 |

All boards: GND to GND.

## Install / 導入

1. Download the UF2 for your board from [Releases](../../releases).
   Releases から使うボードの UF2 をダウンロードします。
2. Hold BOOTSEL, plug the board in, and copy the UF2 to the `RPI-RP2` drive.
   BOOTSEL を押しながら接続し、`RPI-RP2` ドライブに UF2 をコピーします。
3. Get `wl82loader.bin` from [jl-uboot-tool](https://github.com/kagaimiq/jl-uboot-tool). It is not included here; it is sent to the board's RAM when you use the tool.
   `wl82loader.bin` を jl-uboot-tool から入手します。同梱していません。使用時にボードの RAM へ送られます。

## Use / 使い方

**Web app** (Chrome or Edge): open `web/index.html` and follow the steps.
**ウェブアプリ**（Chrome / Edge）: `web/index.html` を開き、手順に従います。

**CLI** (needs pyserial):

```bash
python3 tools/fm1t.py --loader wl82loader.bin info
python3 tools/fm1t.py --loader wl82loader.bin dump backup.bin
python3 tools/fm1t.py --loader wl82loader.bin write --package FM-1_vNN.fwsc --ref backup.bin --write
```

Without `--write`, `write` only checks. If the FM-1 has no working firmware, run `fm1t.py rekey`, then power the FM-1 on. See `fm1t.py --help` for the rest.
`--write` なしの `write` は確認のみです。FM-1 のファームが壊れている場合は `fm1t.py rekey` のあと FM-1 の電源を入れます。

## Build / ビルド

pico-sdk 2.2.0, ARM GCC and CMake.

```bash
git submodule update --init
cmake -S . -B build -DPICO_SDK_PATH=$HOME/pico-sdk -DPICO_BOARD=seeed_xiao_rp2040
make -C build -j8 fm1_transporter
```

Pico: `-DPICO_BOARD=pico`. Pico 2: `-DPICO_BOARD=pico2`. Debug Probe: `-DFM1T_BOARD=debugprobe -DPICO_BOARD=pico`.

## Cautions / 注意事項

- Use at your own risk. Always keep a dump before writing.
  自己責任で使用してください。書き込む前に必ずダンプを保存してください。
- Only `0x4000`–`0x93000` is ever written.
  書き込むのは `0x4000`–`0x93000` だけです。
- After a session, power-cycle the FM-1 before using it over USB.
  作業後は FM-1 の電源を入れ直してください。
- Not affiliated with M-VAVE or JieLi.
  M-VAVE および JieLi とは無関係です。

[Architecture](docs/ARCHITECTURE.md) · [Protocol](docs/PROTOCOL.md) · [Dev log](docs/DEVLOG.md) · [AGENTS.md](AGENTS.md)

## License / ライセンス

MIT. Includes [Pico-PIO-USB](https://github.com/sekigon-gonnoc/Pico-PIO-USB) (MIT). Some routines are ported from jl-uboot-tool (MIT).
