# Companion Apps

Bruce has two companion apps. They are separate projects with separate releases:

* **[Bruce App](https://github.com/BruceDevices/App){target="_blank" rel="noopener"}** — desktop and Android. Flash firmware, run serial commands, and mirror the device screen live.
* **[Bruce Remote for IOS](https://github.com/BruceDevices/App-IOS){target="_blank" rel="noopener"}** — iPhone. Connects over Bluetooth Low Energy, mirrors the screen with a D-pad that drives the real firmware menus, plus IR / Sub-GHz / NFC capture and replay, file manager, offline capture library and the community app store.

!!! info "Coming to the official stores"
    Both apps are being prepared for publication on the **Apple App Store**, **Google Play** and **F-Droid**. Until then, use the GitHub release downloads below.

## Available Releases

| Platform | Download | App |
| --- | --- | --- |
| Linux (x86-64) | `BruceApp-linux-x86-64-latest.zip` | [Bruce App releases](https://github.com/BruceDevices/App/releases/latest){target="_blank" rel="noopener"} |
| Windows | `BruceApp-windows-latest.zip` | [Bruce App releases](https://github.com/BruceDevices/App/releases/latest){target="_blank" rel="noopener"} |
| macOS | `BruceApp-macos-latest.zip` | [Bruce App releases](https://github.com/BruceDevices/App/releases/latest){target="_blank" rel="noopener"} |
| Android | `signed-bruce-app-release.apk` | [Bruce App releases](https://github.com/BruceDevices/App/releases/latest){target="_blank" rel="noopener"} |
| iOS / iPadOS | `Bruce-app-ios.ipa` | [Bruce Remote releases](https://github.com/BruceDevices/App-IOS/releases/latest){target="_blank" rel="noopener"} |

To download: open the release page, scroll to **Assets**, and pick the file for your platform.

## Installing

### Linux

The archive holds an `app-image` directory — unzip it anywhere and run the launcher in its `bin/` folder:

```sh
unzip BruceApp-linux-x86-64-latest.zip
# then run the executable under app-image/*/bin/
```

For firmware flashing you also need Python 3 and esptool:

```sh
pip install esptool
```

Serial access needs your user in the right group (`dialout` on Debian/Ubuntu, `uucp` on Arch):

```sh
sudo usermod -aG dialout $USER
```

Log out and back in for the group change to apply.

### Windows

Unzip the archive and run the installer `.exe` inside it. SmartScreen may warn about an unknown publisher — choose **More info → Run anyway**.

Install the USB-serial driver for your board if it is not detected (CP210x or CH340, depending on the device), and `pip install esptool` for flashing.

### macOS

Unzip the archive, open the `.dmg` inside it and drag the app to `/Applications`. The build is not notarized yet, so the first launch has to be approved: right-click the app, choose **Open**, and confirm. If macOS still refuses, clear the quarantine flag:

```sh
xattr -dr com.apple.quarantine /Applications/<app name>.app
```

Flashing needs `pip install esptool`.

### Android

1. Download `signed-bruce-app-release.apk` to the phone.
2. Allow installs from your browser or file manager when prompted (**Install unknown apps**).
3. Open the APK and install it.

Connecting over USB requires a phone with USB OTG support and an OTG cable or adapter.

### iOS

The `.ipa` is not signed for general distribution, so it has to be installed with your own signing identity. Pick whichever you already use:

* **Sideloadly / AltStore** — install the `.ipa` with a free Apple ID. Apps signed this way expire after **7 days** and need to be re-installed.
* **Xcode** — clone the repo and build to the device (Xcode 16+). A paid Apple Developer account raises the expiry to a year.

Requires **iOS 17 or later** on a physical iPhone, and the device BLE API switched on: **Config → Advanced → Toggle BLE API** (it is off by default, and nothing will be found until you enable it).

!!! note
    Bruce Remote cannot flash firmware. iOS does not expose Web Serial or USB serial, and the firmware has no OTA path — the Firmware screen only compares versions and links to the [Web Flasher](https://bruce.computer/flasher){target="_blank" rel="noopener"}, which runs on a computer.

## Connecting

**Bruce App (desktop / Android)** talks to the device over USB serial for flashing and commands. For screen mirroring, enable WebUI on the device, connect to the Bruce WiFi AP, then enter `bruce.local` or the device IP in the Screen Mirror panel and press **Start Mirror**.

**Bruce Remote (iOS)** talks over BLE only. Enable the BLE API on the device, then tap **Connect** — the Bruce advertises as `Bruce`.
