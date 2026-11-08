# Experimental 90 Hz panel modules

Used only by `evox/build.sh --panel-90hz`; the build otherwise uses the stock
modules in `modules/vendor_ramdisk/`.

The FT8201P panel (`m100t_ft8201p_hjr085005d_a0_wuxga_90hz`) has a 60 Hz and a
90 Hz mode in `cust_panel.ko` with the same horizontal timings and nearly the
same pixel clock (251883 / 252946 kHz); only the vertical front porch differs
(1258 / 162 lines). `panel_wb_common_lcm.ko` exposes a single mode built from
`modes[0]` and recomputes its clock as `htotal * vtotal * 60`.

| File | Patch |
|---|---|
| `cust_panel.ko` | relocation of `common_desc.modes[0]` (`.rodata+0xcb20`): `0xcb90` (60 Hz mode) -> `0xcc08` (90 Hz mode), 2 bytes |
| `panel_wb_common_lcm.ko` | `common_panel_probe` `.text+0x1654`: `mov w9, #60` -> `mov w9, #90`, 2 bytes |

Result: one 1200x1920 mode, 1266 x 2220 total, 252946 kHz = 90.00 Hz, the
panel's own 90 Hz timing with the unchanged DSI rate.
