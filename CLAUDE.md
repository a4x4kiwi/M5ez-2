# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a PlatformIO project for the M5Stack Core ESP32 device, built on the M5ez interface library. M5ez is a comprehensive UI framework that simplifies creating graphical interfaces for the M5Stack platform, providing menus, buttons, message boxes, text input, and WiFi/settings management.

The project structure follows the M5ez "z-sketch" pattern where independent sketches can run standalone or be integrated into larger applications.

## Build and Development Commands

### Build the project
```bash
pio run
```

### Upload to M5Stack
```bash
pio run --target upload
```

### Monitor serial output
```bash
pio run --target monitor
```

### Clean build files
```bash
pio run --target clean
```

### Upload and monitor (combined)
```bash
pio run --target upload --target monitor
```

### Build with specific environment
```bash
pio run -e m5stack-core-esp32
```

## Architecture and Code Structure

### M5ez Framework Fundamentals

The M5ez library provides a complete UI framework accessed through the global `ez` object. After including `<M5ez.h>`, the framework is initialized with `ez.begin()` (which internally calls `m5.begin()`).

**Core architectural components:**

- **Screen Management**: The screen consists of an optional header (top), optional buttons (bottom), and the canvas (middle area)
- **Header**: 23px high bar at top, can display title and widgets (clock, wifi status, custom widgets)
- **Canvas**: The main drawable area between header and buttons. Inherits from Arduino `Print` class for easy text output
- **Buttons**: Up to 9 button functions can be defined (3 buttons × short/long press + 3 two-button combinations)
- **Menus**: Two types - text menus (scrollable lists) and image menus (horizontal image carousel)

### Project-Specific Structure

**Main application file**: `src/main.cpp`
- Defines the main demo menu showcasing M5ez features
- Uses `#define MAIN_DECLARED` to enable z-sketch integration
- Includes two themes (default and dark) in `setup()`

**z-sketches**: Files prefixed with `z-` (e.g., `src/z-sysInfo.cpp`)
- Can be compiled standalone or included in larger programs
- Use `#ifndef MAIN_DECLARED` guards around standalone `setup()` and `loop()`
- When included, they export functions that can be called as menu items

**Libraries in `lib/`**:
- `lib/M5ez/`: The M5ez framework (version 2.3.0)
- `lib/ezTime/`: Time library with NTP sync and timezone support

### Key M5ez Patterns

**Menu creation pattern**:
```cpp
ezMenu myMenu("Menu Title");
myMenu.addItem("Item Name", functionPointer);
myMenu.addItem("Item Name | Display Caption");
myMenu.run();  // Runs until "Back"/"Exit"/"Done" selected
// or
myMenu.runOnce();  // Returns after any selection
```

**Button definitions**: Specified as hash-separated strings
- 1 button: `"OK"`
- 3 buttons: `"yes # no # maybe"`
- 6 buttons (short + long press): `"yes # no # maybe # four # five # six"`
- 9 buttons (+combinations): `"a # b # c # d # e # f # AB # BC # AC"`

**Event loop pattern**: User code typically spends time in `ez.buttons.poll()` or `ez.buttons.wait()` which internally calls `ez.yield()` to maintain clock updates, WiFi indicators, and user-registered events.

## Configuration and Settings

### PlatformIO Configuration
- Platform: `espressif32`
- Board: `m5stack-core-esp32`
- Framework: `arduino`
- Monitor speed: `115200` baud
- Upload speed: `921600` baud
- Partition scheme: `default.csv` (comment shows `huge_app.csv` alternative)

### M5ez Feature Flags (in `lib/M5ez/src/M5ez.h`)

Enabled by default:
- `M5EZ_WIFI` - WiFi support (disable to compile without networking)
- `M5EZ_BATTERY` - Battery level icon in header
- `M5EZ_BACKLIGHT` - Backlight brightness control
- `M5EZ_CLOCK` - On-screen clock using ezTime
- `M5EZ_FACES` - FACES keyboard support
- `M5EZ_WPS` - WPS WiFi setup

Disabled by default:
- `M5EZ_BLE` - Bluetooth Low Energy support
- `M5EZ_WIFI_DEBUG` - WiFi debug messages

Comment out any `#define` to remove that feature and reduce code size.

## Important Development Notes

### z-sketches Integration
When creating a z-sketch (standalone-capable sub-program):
1. Use `#ifndef MAIN_DECLARED` guard around standalone `setup()`/`loop()`
2. Optionally vary UI based on `MAIN_DECLARED` (e.g., show/hide "Exit" button)
3. Place file in same directory as main sketch - PlatformIO will auto-include it
4. Avoid naming conflicts with other sketches in the directory

### Theme System
Themes are included in `setup()` using `#include` directives:
```cpp
void setup() {
  #include <themes/default.h>
  #include <themes/dark.h>
  ez.begin();
}
```
Multiple included themes enable theme chooser in settings menu. If no themes included, default theme loads automatically.

### Canvas Printing
The canvas object is like Arduino `Serial`:
- `ez.canvas.print()` / `ez.canvas.println()` for output
- `ez.canvas.font()` to set font
- `ez.canvas.color()` to set text color
- `ez.canvas.lmargin()` to set left margin
- `ez.canvas.x()` / `ez.canvas.y()` to set cursor position
- `ez.canvas.scroll(true)` enables scrollback (stores content in RAM)
- `ez.canvas.wrap(true/false)` controls text wrapping

### Button Handling
Buttons captioned `up`, `down`, `left`, or `right` display as triangular arrows. Buttons named (not captioned) `Back`, `Exit`, or `Done` cause menus to exit. The naming vs caption distinction uses pipe syntax: `"name | Caption Text"`.

### Settings Persistence
M5ez stores settings in ESP32 NVS (Non-Volatile Storage) using the Arduino `Preferences` library. WiFi networks, timezone, backlight level, and custom settings persist across reboots.

### Image Resources
Images for menus are encoded as byte arrays in `.h` files (e.g., `src/images.h`). Images can also be loaded from SPIFFS or SD card filesystems.

## Common Development Workflows

### Adding a New Feature/Submenu
1. Create a function that implements the feature UI
2. Add menu item in main loop: `mainmenu.addItem("Feature Name", myFeatureFunction);`
3. In feature function, use `ez.header.show()`, `ez.canvas.*`, `ez.buttons.show()` to build UI
4. Use `ez.buttons.wait()` or `ez.buttons.poll()` for user input
5. Return from function to exit back to menu

### Creating a Standalone z-sketch
1. Create file starting with `z-` (e.g., `z-myfeature.cpp`)
2. Add guards for standalone operation:
   ```cpp
   #ifndef MAIN_DECLARED
   void setup() { ez.begin(); myFeature(); }
   void loop() { }
   #else
   String exit_button = "Exit";
   #endif
   ```
3. Implement feature function that works in both modes

### Working with WiFi
The M5ez WiFi system auto-connects to saved networks. Access via:
- `ez.wifi.menu` - User-facing WiFi settings menu
- `ez.wifi.networks` - Vector of saved WifiNetwork_t structs
- `ez.wifi.add(ssid, key)` / `ez.wifi.remove(ssid)` - Manage networks
- `ez.wifi.writeFlash()` - Save changes to NVS

### Handling Over-The-Air Updates
Use `ez.wifi.update(url, root_cert, &progressBar)` for HTTPS firmware updates. The `tools/get_cert` script extracts the correct root certificate. After successful download, call `ESP.restart()` to boot new firmware.

## Technical Constraints

### Memory Management
- Scrolling canvas stores all printed content in RAM - disable or clear frequently to prevent heap exhaustion
- Image menus load full JPG into memory - use appropriately sized images
- The M5Stack has ~520KB RAM total; monitor with `ESP.getFreeHeap()`

### Display Specifications
- Resolution: 320×240 pixels (defined as `TFT_W` × `TFT_H`)
- Default header height: 23 pixels
- Button area varies based on single/double row (short+long presses)

### ESP32 Specifics
- Dual-core CPU (referenced as 2 cores in sysInfo)
- Flash and SPIFFS partitions configurable via partition CSV
- NVS used for persistent storage (WiFi credentials, preferences)
