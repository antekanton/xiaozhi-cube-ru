# Rebuild upstream releases with patches instead of maintaining a fork

My XingZhi Cube 1.54" TFT Wi-Fi board has broken BOOT and Vol+ buttons, and I want Russian firmware. Instead of keeping a long-lived fork of `78/xiaozhi-esp32` in sync, this repo only holds patches and a workflow: once a week it takes the latest upstream release tag, applies `patches/*.patch`, builds the `xingzhi-cube-1.54tft-wifi-voldown` variant with `--language ru-RU`, and publishes it here under the same tag. The fork `antekanton/xiaozhi-esp32` is kept only as a workspace for developing and test-building patches.

## Considered Options

- **Long-lived fork with a custom variant.** Needs regular upstream merges and conflict resolution even when nothing relevant changed.
- **Change the existing board for everyone.** Rejected: the broken buttons are a property of one physical unit, and upstream rules forbid altering an existing board for different hardware.
- **Kconfig option on the board.** Unnecessary here: the patch is only ever applied in this pipeline, so it changes the board code unconditionally.

## Consequences

- The build variant name differs from the stock board, so the xiaozhi.me OTA server will not offer stock firmware (with the broken buttons re-enabled) to this device. Updates are flashed manually from this repo's releases.
- When upstream changes the patched files, `git apply` fails, the run fails, and nothing is published. The patch must then be regenerated in the fork against the new tag, and the workflow re-run with `force`.
