# Development log

Hardware results, 2026-10-01. XIAO RP2040, three wires, no VBUS.

| Item | Result |
|---|---|
| UBOOT entry through the PIO host | ✅ handoff in ~1 ms, `4C4A:8057` mounted |
| Loader and chip info | ✅ key `980F`, flash `856014` |
| 1 MiB dump | ✅ 3.3 s, identical to the V15 reference |
| Sector rewrite (`0x92000`) | ✅ verified; V15 still boots |
| Out-of-range writes | ✅ refused (`0x0`, `0x3000`, `0x93000`) |
| Soft key from V15 | ✅ UBOOT in ~1 s |
| USB_KEY after a watchdog reset | ✅ ACK within 5 ms |

## Lessons

- `CFG_TUH_API_EDPT_XFER` must be 1. Without it, raw bulk transfers never complete.
- Keep the 1 ms pulses going until the host starts. Stock V15 needs them too.
- Without VBUS, stock V15 enables USB only after a cold power-on.
- The loader NAKs between packets. Speed came from 4 KiB reads plus NAK retries within each frame.

Logs: [logs/](logs/)
