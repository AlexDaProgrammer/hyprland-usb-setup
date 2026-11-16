# Running Hyprland from a USB Stick (Persistent, No Dual-Boot)

A step-by-step tutorial for installing a full, persistent Linux desktop — **EndeavourOS + Hyprland + [Caelestia](https://github.com/caelestia-dots/caelestia)** — entirely onto a USB flash drive, so it never touches your laptop's internal Windows drive.

![status](https://img.shields.io/badge/tested%20on-Lenovo%20(UEFI)-blue) ![distro](https://img.shields.io/badge/distro-EndeavourOS-7f3fbf) ![wm](https://img.shields.io/badge/WM-Hyprland-00e5ff)

## Why a USB install instead of a live/persistence image?

| Approach | Saves your changes? | Speed | Notes |
|---|---|---|---|
| Live USB (Rufus, no persistence) | ❌ Resets every boot | Fast to set up | Good for a quick look only |
| Ventoy + `persistence.dat` | ✅ Partial | Medium | Only works with some distros (Ubuntu-family), fiddly on Arch-based ones |
| **Full install onto the USB** (this guide) | ✅ Fully | Slower (USB speed) | Behaves like a real install — apps, settings, and updates all persist |

This guide uses the third approach: the USB stick *is* the hard drive. Nothing is written to the internal SSD.

## What you end up with

- A genuinely bootable Linux install on a USB drive, selectable from the firmware boot menu (F12 on most laptops), that leaves Windows completely untouched.
- [Hyprland](https://hyprland.org/) (a tiling Wayland compositor) themed with [Caelestia](https://github.com/caelestia-dots/caelestia) for a modern, animated desktop out of the box.

## Requirements

- A **second** USB drive (4 GB+) to act as the installer stick — can be any cheap/old drive, it gets wiped.
- Your **target** USB drive (ideally 32 GB+, USB 3.0 or faster) — this is where Linux will actually live.
- A UEFI laptop or PC.
- [Rufus](https://rufus.ie/) and the [EndeavourOS ISO](https://endeavouros.com/).

> ⚠️ Both USB sticks get **completely erased**. Back up anything on them first.

## 1. Flash the installer

1. Download Rufus and the latest EndeavourOS ISO.
2. Open Rufus, and under **Device** select your *throwaway* installer stick — **not** the drive you want Linux on.
3. Under **Boot selection**, select the ISO.
4. Partition scheme: **GPT**. Target system: **UEFI (non CSM)**.
5. Click **START**. If prompted, choose **"Write in DD Image mode."**
6. Wait for the status bar to read **READY**.

## 2. Boot the installer

1. Plug in **both** sticks (installer + target) and reboot.
2. Tap **F12** (varies by vendor) to open the boot menu.
3. Select the installer stick's **EFI USB Device** entry.

### If you get "boot failed"

This almost always means **Secure Boot** is on, and the installer image isn't signed for it.

1. If your drive is BitLocker-encrypted, grab your recovery key first from [account.microsoft.com/devices/recoverykey](https://account.microsoft.com/devices/recoverykey) — disabling Secure Boot can trigger a one-time recovery prompt.
2. Reboot into BIOS/UEFI setup (usually **F2** or **Fn+F2**).
3. Under **Security**, set **Secure Boot** to **Disabled**. Save and exit (**F10**).
4. Boot from the installer stick again — it should work now.

## 3. Run the installer

1. Launch the installer, choose **Online** install, connect to Wi-Fi.
2. Pick **Hyprland** as the desktop if it's offered (otherwise pick KDE Plasma as a fallback base — you can still layer Hyprland on top later).
3. **Disk step — the one that matters:** choose **Erase disk**, then pick your **target** USB drive by its size. Double- and triple-check this isn't your internal drive.
4. Use **ext4**, no encryption, finish the rest of the wizard, and install.

## 4. First boot

1. Shut down (not restart) when the installer finishes.
2. **Unplug the installer stick** — you only need the target drive from now on.
3. Boot from the target USB via F12.

From here on, this drive behaves like a normal install: anything you save, install, or configure persists across reboots.

## 5. Install the Caelestia shell

Caelestia is a [Quickshell](https://github.com/quickshell-mirror/quickshell)-based config for Hyprland with a modern, animated look.

```sh
sudo pacman -Syu
sudo pacman -S --needed hyprland xdg-desktop-portal-hyprland xdg-desktop-portal-gtk ttf-jetbrains-mono-nerd git base-devel

paru -S caelestia-cli     # or: yay -S caelestia-cli
caelestia install
```

Log out, pick the **Hyprland** session at the login screen, and log back in.

### Key bindings

| Key | Action |
|---|---|
| `Super` (tap) | Open launcher |
| `Super + N` | Toggle sidebar |
| `Super + K` | Show all panels |
| `Ctrl + Alt + Delete` | Session menu |

> Don't edit `~/.config/hypr/` directly — updates will overwrite it. Put personal tweaks in `~/.config/caelestia/hypr-vars.lua` and `hypr-user.lua`. Update with `caelestia update`.

## Troubleshooting

- **Installer "boot failed"** → Secure Boot, see step 2.
- **No Hyprland session at login** → switch to a TTY (`Ctrl+Alt+F3`), log in, run `Hyprland` directly, and read the error.
- **Windows asks for a recovery key after flipping Secure Boot** → that's expected if BitLocker is on; use the key you saved beforehand.
- **Want to go back to Windows only** → shut down, unplug the USB drive, boot normally. Nothing on the internal disk was touched.

## Result

A fully persistent, animated Hyprland desktop that boots from a USB stick, with zero changes to the host machine's own Windows install.
