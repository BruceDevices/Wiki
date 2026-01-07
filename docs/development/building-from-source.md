---
tags:
 -  _TODO
todo: All build instructions need checking
---
# Building From Source

## VS Code

> You might find that PlatformIO is not used and instead PIOArduino will be installed.

Clone the repository to your PC and open the project folder in Visual Studio Code with PlatformIO extension and click on "PlatformIO: Build" at the bottom.

After compiling the project, you need to merge the bootloader, partitions and the actual firmware on a single one, for that you can run the PlatformIO Custom task named "build-firmware" to output the single combined binary.

The .bin file will be in the root folder of the project. You can use a [manual flashing](../how-to-install.md#manual-flashing) method to flash these to your device.


## Command Line Build

### Linux

Requirements

```sh
sudo apt update
sudo apt install python3-pip git
pip3 install platformio
export PATH=$PATH:~/.local/bin
source ~/.bashrc
echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
source ~/.bashrc
git clone https://github.com/pr3y/Bruce.git
cd Bruce
```

For Fedora use

```sh
sudo dnf update
sudo dnf install python3-pip git
```

### Build the Project

```sh
pio run --target clean
pio run -e m5stack-cardputer
pio run -e m5stack-cplus2
pio run -e m5stack-cplus1_1
# or pio run to all builds
# pio run
pio run -e m5stack-cardputer -t build-firmware
pio run -e m5stack-cplus2 -t build-firmware
pio run -e m5stack-cplus1_1 -t build-firmware
```


### Windows

* Install [Git](https://git-scm.com/download/win)
* Install Python using the Microsoft Store
* Install VSCode
* Download or Git clone the repository
* Within VSCode, `File -> Open Folder...` and select the folder you just extracted/cloned
* If it doesn't prompt to install PlatformIO, click on "Extentions" (4 swares icon on the left) and install PlatformIO (and restart VSCode)
* In the Status bar (down on the screen) click where is written `Default (Bruce-main)`
* A menu will appear in the Search bar (up on screen), select your device "env"
* Now click in the :heavy_check_mark: Icon in the Status Bar (beside the cute little House), it will start building your project
* If you want to upload directly, you can do it clicking in the "->" arrow to upload to your device
* If you want to output a binary you can flash, use the "build-firmware" Custom task in the PlatformIO Extension Sidebar Menu named "project tasks"


## GitHub Actions

You can use the Github workflow to build the binaries for you with Actions, the last releases are available in the Artifacts, but you can fork the project and make the changes you want to this, then build on your using your own Actions.
