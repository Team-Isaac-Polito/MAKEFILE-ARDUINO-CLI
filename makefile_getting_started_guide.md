🧰 Guide to Using the Makefile for Raspberry Pi Pico / Pico W (macOS Version)
This guide explains how to compile, upload, and monitor a sketch on the Raspberry Pi Pico or Pico W using make and arduino-cli — all directly from the Terminal, without opening the Arduino IDE.

⚙️ 1. Install Arduino CLI
You can install arduino-cli in two ways:
Option A – via Homebrew (recommended)
brew install arduino-cli

Option B – via the official installer
curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | sh
sudo mv bin/arduino-cli /usr/local/bin/

Verify installation
arduino-cli version

⚙️ 2. Install the Raspberry Pi Pico Core
Run the following commands:
arduino-cli config init
arduino-cli config add board_manager.additional_urls https://github.com/earlephilhower/arduino-pico/releases/download/global/package_rp2040_index.json
arduino-cli core update-index
arduino-cli core install rp2040:rp2040

⚙️ 3. Install Required Libraries
Install the required libraries using:
arduino-cli lib update-index
arduino-cli lib install "Adafruit GFX Library" "Adafruit SH110X"

🧩 4. Verify Development Tools
On macOS, gcc and make are already installed (if you have Xcode Command Line Tools).
You can verify this with:
gcc --version
make --version

If they are missing, install them with:
xcode-select --install

📁 5. Project Structure
my_project/
├── my_project.ino
├── include/
├── lib/
│   ├── libA/
│   │   └── src/
│   │       ├── file.cpp
│   │       └── file.h
│   └── libB/
│       └── src/
│           ├── file.cpp
│           └── file.h
├── Makefile
└── build/

🪛 6. Board Selection
In your Makefile, find the line:
BOARD_FQBN ?= rp2040:rp2040:rpipico

Change it to match your board:


| Board | BOARD_FQBN |
| :-- | :-- |
| Raspberry Pi Pico | rp2040:rp2040:rpipico |
| Raspberry Pi Pico W | rp2040:rp2040:rpipicow |

⚡ 7. Compile the Project
Navigate to your project folder:
cd path/to/project

Compile:
make compile

This generates .bin and .uf2 files in build/output/.
Other options:
make compile_fast
make compile_all

🔼 8. Upload the Program
Method 1 — BOOTSEL Mode (recommended)
Hold the BOOTSEL button on your Pico.
Connect it via USB while keeping BOOTSEL pressed.
Release the button — your Pico will appear as a USB drive (e.g., /Volumes/RPI-RP2/).
In your Makefile, make sure you have:
DESTINATION ?= '/Volumes/RPI-RP2/'

Then upload:
make upload_bootsel

Method 2 — Upload via Serial Port
If you want to upload via the serial interface instead:
List available serial devices:
ls /dev/tty.*

You should see something like /dev/tty.usbmodem14101.
Upload:
make upload PORT=/dev/tty.usbmodem14101

🖥️ 9. Open Serial Monitor
To read Serial.print() outputs:
make monitor PORT=/dev/tty.usbmodem14101

Default baud rate: 115200.

🧹 10. Clean Build Files
Remove all build files:
make clean_all

Remove only compiled outputs:
make clean_output

🧾 11. Full Command Reference


| Command | Description |
| :-- | :-- |
| make compile | Compile the project |
| make compile_fast | Fast compile (skip extra libs) |
| make compile_all | Compile all variants |
| make upload | Upload via serial |
| make upload_bootsel | Upload via USB BOOTSEL |
| make monitor | Open serial monitor |
| make clean_all | Remove all build files |
| make clean_output | Remove only output files |
| make help | Show help menu |

🩵 Tips \& Troubleshooting


| Problem | Cause | Fix |
| :-- | :-- | :-- |
| Pico not visible as /Volumes/RPI-RP2 | Not in BOOTSEL mode | Hold BOOTSEL while connecting |
| make upload fails | Wrong serial device | Check with ls /dev/tty.* |
| Serial monitor empty | Wrong baud rate or port | Verify Serial.begin(115200) and make monitor port |
| Compilation error | Missing libraries | Install with arduino-cli lib install ... |