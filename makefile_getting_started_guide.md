
# Guide to Using the Makefile for Raspberry Pi Pico / Pico W

This document explains how to **compile**, **upload**, and **monitor** a sketch on the **Raspberry Pi Pico** or **Raspberry Pi Pico W** using `make` and `arduino-cli`.
This allows you to manage everything from the terminal, without opening the Arduino IDE.

***

## ⚠️ Before Using the Makefile

🔹 **Before executing any Makefile command, ensure you have installed `arduino-cli`.**


#### 1. Download Arduino CLI
- Go to the official release page:
```bash
  curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | sh
  sudo mv bin/arduino-cli /usr/local/bin/
  ```
---

####  2. Verify the Installation
Open **Command Prompt (cmd)** and type:
```bash
arduino-cli version
```


Before using the Makefile, ensure that you have installed the correct rp2040 core. You can do this by running the following command in your command prompt:

```bash
arduino-cli version

arduino-cli config init

arduino-cli config add board_manager.additional_urls https://github.com/earlephilhower/arduino-pico/releases/download/global/package_rp2040_index.json
arduino-cli core update-index

arduino-cli core install rp2040:rp2040
```

Additionally, to run the Makefile on Windows, you need to install MinGW-w64. MinGW-w64 provides a complete toolchain including the GNU Compiler Collection (GCC) and other essential tools for compiling code on Windows. You can download it from 
```bash
sudo apt install -y mingw-w64

x86_64-w64-mingw32-gcc --version
```

Next, you need to install GnuWin32 Make from a new cmd

```bash
sudo apt update
sudo apt install -y build-essential make automake autoconf gcc g++ coreutils grep sed findutils diffutils

sudo nano /etc/apt/sources.list
cat /etc/os-release
```

Next, paste

```bash
PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
VERSION="13 (trixie)"
VERSION_CODENAME=trixie
DEBIAN_VERSION_FULL=13.1
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

Then

```bash
sudo apt update
sudo apt upgrade -y
```

To verify that the installation of GnuWin32 was successful, open a new command prompt and run:

```bash
make --version
```

```bash
gcc --version
```
If there are no errors and the respective versions of both tools are displayed, proceed with the installation of the libraries.

Regarding the libraries, you can install the necessary ones using Arduino CLI with the following commands.


```bash
arduino-cli lib update-index
arduino-cli lib install "Adafruit GFX Library"
arduino-cli lib install "Adafruit SH110X"
```
To test that everything is working correctly, open a terminal in Visual Studio and run "make help". If there are no errors, you can run "make compile" to compile.



## Project Structure

```bash
my_project/              ← main folder (SKETCH_PATH)
├── my_project.ino       ← main file
├── include/             ← any .h headers
├── lib/                 ← any custom libraries
│   ├── libA/
│   │   └── src/
│   │       ├── file.cpp
│   │       └── file.h
│   └── libB/
│       └── src/
│           ├── file.cpp
│           └── file.h
├── Makefile             ← this file
└── build/               ← (auto-created)
```


# Board Selection

In the Makefile, there is the variable:

```make
BOARD_FQBN ?= rp2040:rp2040:rpipico
```

Depending on your board, change it as follows:

- Raspberry Pi Pico --> rp2040:rp2040:rpipico
- Raspberry Pi Pico W --> rp2040:rp2040:rpipicow

To change it, open the Makefile and replace the line:

```bash
BOARD_FQBN ?= rp2040:rp2040:rpipico
```

with:

```bash
BOARD_FQBN ?= rp2040:rp2040:rpipicow
```


***

# Main Commands

Navigate to the project folder:

```bash
cd path/to/project
```


## Compile the Project

```bash
make compile
```

Compiles the sketch and generates `.bin` and `.uf2` files in `build/output`.

- Compile a specific variant:

```bash
make compile MODULE_DEFINE="MK2_MOD2"
```

- Fast compile (no extra libraries):

```bash
make compile_fast
```

- Compile all variants:

```bash
make compile_all
```

Compiles two versions (e.g., MK2_MOD1 and MK2_MOD2) in separate folders (`out_MK2_MOD1` and `out_MK2_MOD2`).

## Uploading the Program to the Pico

After compilation, you can upload the program in two ways:

### Method 1: Upload in BOOTSEL Mode

This uses the .uf2 file and does not require the serial port.

Procedure:

- Press and hold the BOOTSEL button (the only one on the board).
- Connect the Pico to the PC via USB while keeping BOOTSEL pressed.
- Release the button: the PC will detect the Pico as an external drive (e.g. E:).
- Open “This PC” and check the drive letter.
- Open the Makefile and look for this line:

```bash
DESTINATION ?= 'D:\'
```

! Replace D: with the correct letter (e.g., 'E:\').
You only need to do this once: the PC will always recognize the same drive.

Uploading:

```bash
make upload_bootsel
```

The .uf2 file will be automatically copied to the Pico and the program will start immediately.

For subsequent uploads:
Put the Pico in BOOTSEL (hold the button before connecting) and run:

```bash
make upload_bootsel
```


### Method 2: Upload via Serial Port (COM)

This uses the serial port of the Pico connected to the PC normally.

Procedure:

- Connect the Pico to the PC (do not press BOOTSEL).
- List available COM ports:

```bash
make port
```


A list such as the following will appear:

```bash
COM1
COM2 (Raspberry Pi Pico)
```

If your Pico is connected on COM2, run:

```bash
make upload PORT=COM2
```

The Makefile will use the compiled .bin file and upload it automatically.

## Open the Serial Monitor

To view Serial.print or Serial.println messages from your program:

Connect the Pico to the PC.

Find the COM port:

```bash
make port
```

Open the serial monitor by specifying the port:

```bash
make monitor PORT=COM2
```

The default baud rate is 115200.

## Clean Build Files

Clean the entire build folder:

```bash
make clean_all
```

Partial clean (output folder only):

```bash
make clean_output
```


## Complete List of Commands

| Command | Description |
| :-- | :-- |
| make compile | Compiles the project |
| make compile_fast | Fast compile |
| make compile_all | Compiles both versions (MK2_MOD1 and MK2_MOD2) |
| make upload | Upload via serial port (COM) |
| make upload_bootsel | Upload in BOOTSEL mode (USB drive) |
| make monitor | Opens the serial monitor |
| make port | Shows available COM ports |
| make auto_com_port | Automatically detects Pico's COM |
| make clean_all | Removes all build files |
| make clean_output | Removes only output files |
| make help | Shows command help |

## Useful Tips

After the first upload in BOOTSEL, you don’t need to change DESTINATION again.

If you have multiple Picos connected, always check which COM is assigned.

You can chain commands:

```bash
make compile && make upload PORT=COM2
```

⚠️ Troubleshooting


| Problem | Possible Cause | Solution |
| :-- | :-- | :-- |
| Pico not listed in COM ports | Driver not installed | Install Pico USB drivers or use **BOOTSEL** mode |
| `make upload` command fails | Incorrect COM port | Check with `make port` and update `PORT=COMx` |
| Pico not appearing as external drive (BOOTSEL) | BOOTSEL button not held | Hold BOOTSEL before connecting Pico |
| Compilation failed | Missing libraries | Make sure all libraries are in `lib/` or installed via `arduino-cli` |
| Serial monitor shows nothing | Wrong baud rate or port | Ensure both code and Makefile have **115200** and the correct port |