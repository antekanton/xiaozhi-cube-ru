# xiaozhi-cube-ru

Patched, Russian-language rebuilds of [XiaoZhi](https://github.com/78/xiaozhi-esp32) firmware releases for one XingZhi Cube 1.54" TFT Wi-Fi board (`xingzhi-cube-1.54tft-wifi`) whose BOOT and Vol+ buttons are broken.

Why it is built this way: [ADR 0001](docs/adr/0001-rebuild-upstream-releases-with-patches.md).

## What the patch changes

- **Vol−** is the talk button. Click: start or stop a conversation. Click while the device is starting: Wi-Fi setup.
- **BOOT** (GPIO0) and **Vol+** (GPIO40) are not polled at all. Vol− long press does nothing.
- Volume is changed by voice ("сделай громче", "громкость 50").
- Build variant name: `xingzhi-cube-1.54tft-wifi-voldown`.

## How releases appear

[`.github/workflows/build-patched-release.yml`](.github/workflows/build-patched-release.yml) runs every Monday at 03:00 UTC. If the latest upstream release tag (for example `v2.5.3`) has no release here yet, it checks out upstream at that tag, applies `patches/*.patch`, builds with `--language ru-RU` and publishes a release with the same tag.

Manual run: **Actions → Build patched release → Run workflow**. Inputs:

| Input | Default | Meaning |
|---|---|---|
| `tag` | empty | Upstream tag; empty means the latest release |
| `force` | false | Rebuild even if the release already exists here |
| `language` | `ru-RU` | `build.py --language` |
| `wake_word` | empty | `build.py --wake-word`; empty means the default ("Hi ESP") |

If the patch no longer applies to a new upstream release, the run fails, GitHub emails you, and nothing is published.

## Flashing

Each release has two files:

- `*_merged-binary.bin`: the full image. Flash it at offset `0x0`:
  ```sh
  esptool.py --chip esp32s3 -p /dev/cu.usbmodem* write_flash 0x0 <file>_merged-binary.bin
  ```
  or use the [ESP web flasher](https://espressif.github.io/esptool-js/).
- `*_xiaozhi.bin`: the application image, for OTA from your own server.

The xiaozhi.me OTA server will not update this build automatically. Flash new releases yourself.

## Updating the patch

1. In the fork `antekanton/xiaozhi-esp32`, check out the new upstream tag and re-apply your change on a branch.
2. Build it: `python3 scripts/build.py xingzhi-cube-1.54tft-wifi --name xingzhi-cube-1.54tft-wifi-voldown --language ru-RU`.
3. Export: `git diff <tag> HEAD -- main/boards/nologo/xingzhi-cube-1.54tft-wifi > patches/0001-voldown-talk-button.patch`.
4. Commit it here and run the workflow with `force: true`.
