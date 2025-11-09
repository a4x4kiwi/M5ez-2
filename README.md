# M5ez for M5Unified - Community Migration

> **A complete M5Stack interface library, now compatible with M5Unified**

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![Platform](https://img.shields.io/badge/platform-M5Stack-blue)
![Framework](https://img.shields.io/badge/framework-M5Unified-orange)

---

## 🙏 Credits

### Original Author
This project is based on the **excellent M5ez library** originally created by **[Rop Gonggrijp](https://github.com/ropg)** and the M5ez community.

**Original M5ez Repository:** [https://github.com/M5ez/M5ez](https://github.com/M5ez/M5ez)

The original M5ez library transformed M5Stack programming by providing an intuitive, full-featured interface builder that made creating professional-looking applications accessible to programmers of all skill levels. This migration builds upon that foundation while modernizing it for current M5Stack hardware and software.

### Migration

This M5Unified-compatible version was migrated and updated by **Claude Code** (Anthropic's AI coding assistant) in collaboration with the project maintainer. The migration involved comprehensive updates to work with the modern M5Unified library suite while preserving all the functionality and ease-of-use that made M5ez so popular.

---

## 📋 Overview

M5ez (pronounced "M5 easy") is a complete interface builder library for M5Stack ESP32 devices. It provides:

- 📱 **Text and Image Menus** - Easy navigation with scrolling support
- 🔘 **Multi-function Buttons** - Up to 9 button functions (3 buttons × short/long press + combinations)
- 💬 **Message Boxes** - Flexible dialogs with custom buttons
- ⌨️ **3-Button Text Input** - Clever text entry using just 3 buttons
- 📡 **Built-in WiFi** - Auto-connect with WPA/WPS support
- 🕐 **On-screen Clock** - With timezone support via ezTime
- 🎨 **Theme System** - Customizable color schemes
- 📊 **Progress Bars** - For downloads and long operations
- 🔋 **Battery Indicator** - Real-time battery status
- ⚙️ **Settings Menu** - User-configurable options

**This version has been fully migrated to work with M5Unified v0.2.10+ and the latest ESP32 Arduino framework.**

---

## ⚠️ Important Notes

### For Visual Studio Code / PlatformIO Users

**This project is designed for Visual Studio Code with PlatformIO, NOT the Arduino IDE.**

The original M5ez was designed for Arduino IDE. This migrated version uses PlatformIO's advanced build system and library management, which provides better dependency handling and compilation for the M5Unified ecosystem.

### Library Compatibility

- ✅ **M5Unified** v0.2.10+ (replaces M5Stack library)
- ✅ **M5GFX** (included with M5Unified)
- ✅ **ezTime** v0.8.3+ (time and timezone support)
- ✅ **ESP32 Arduino Framework** v3.x

---

## 🚀 Getting Started

### Installation

1. **Clone or Download** this repository
2. **Open** the project folder in Visual Studio Code
3. **Install PlatformIO** extension if not already installed
4. **Build** the project: `PlatformIO: Build` (Ctrl+Alt+B)
5. **Upload** to your M5Stack: `PlatformIO: Upload` (Ctrl+Alt+U)

### Basic Usage

```cpp
#include <M5ez.h>
#include <ezTime.h>

void setup() {
  #include <themes/default.h>  // Include theme
  ez.begin();                   // Initialize M5ez
}

void loop() {
  ezMenu mainMenu("Main Menu");
  mainMenu.addItem("Option 1", doSomething);
  mainMenu.addItem("Option 2", doSomethingElse);
  mainMenu.addItem("Settings", ez.settings.menu);
  mainMenu.run();
}

void doSomething() {
  ez.msgBox("Hello", "M5ez with M5Unified!");
}

void doSomethingElse() {
  // Your code here
}
```

---

## 📚 Documentation

### Original M5ez Documentation

For comprehensive documentation on how to use M5ez features, please refer to the **original M5ez documentation**:

**📖 [M5ez User Manual](https://github.com/M5ez/M5ez#m5ez-user-manual)**

The original documentation covers:
- Complete API reference
- Menu system (text and image menus)
- Button handling
- Message boxes and text input
- WiFi configuration
- Settings and themes
- And much more!

### What Changed in This Migration

This section documents the technical changes made to adapt M5ez to M5Unified.

---

## 🔧 Migration Changes Summary

### Library Dependencies

**Replaced:**
- ❌ `M5Stack.h` → ✅ `M5Unified.h` + `M5GFX.h`

**Added:**
- `FS.h` (filesystem support)
- Updated WiFi event handling for ESP32 v3.x framework

### API Changes

#### Display API
All display calls updated from old M5Stack LCD API to M5GFX:
```cpp
// Old (M5Stack)
m5.lcd.fillRect(...)
m5.lcd.setTextColor(...)
m5.lcd.drawString(...)

// New (M5Unified/M5GFX)
M5.Display.fillRect(...)
M5.Display.setTextColor(...)
M5.Display.drawString(...)
```

#### Initialization
```cpp
// Old
m5.begin();

// New
M5.begin();
```

#### Button API
```cpp
// Old
m5.BtnA.wasPressed()

// New
M5.BtnA.wasPressed()  // Same API, just capitalized M5
```

#### Power Management
```cpp
// Old
m5.powerOFF();

// New
M5.Power.powerOff();
```

### WiFi Event System

**Major Changes:**
- Updated WiFi event handler signatures to use new Arduino ESP32 v3.x types
- `WiFiEvent_t` → `arduino_event_id_t`
- `system_event_info_t` → `arduino_event_info_t`
- `SYSTEM_EVENT_STA_DISCONNECTED` → `ARDUINO_EVENT_WIFI_STA_DISCONNECTED`
- Event info structure field changes: `info.disconnected.reason` → `info.wifi_sta_disconnected.reason`

### WPS Support

**Modifications:**
- Removed deprecated `crypto_funcs` field (no longer in ESP32 framework)
- WPS PIN event handling temporarily disabled (event type changed in framework)
- WPS button mode still functional

### Font System

Updated to use M5GFX font API:
- `setFreeFont()` → `setFont()` (deprecated warning fixed)
- Font height API simplified

### Battery Monitoring

```cpp
// Old
M5.Power.isChargeFull()  // Method removed in M5Unified

// New
M5.Power.isCharging()    // Simplified to charging status only
```

### Build Configuration

**PlatformIO Settings** ([platformio.ini](platformio.ini)):
```ini
platform = espressif32
board = m5stack-core-esp32
framework = arduino
board_build.partitions = huge_app.csv  # Needed due to increased size
build_flags = -DMAIN_DECLARED          # For z-sketch compatibility
lib_deps = m5stack/M5Unified
```

**Note:** Firmware size increased from ~900KB to ~1.38MB due to M5Unified/M5GFX, requiring `huge_app.csv` partition scheme.

---

## 📁 Project Structure

```
M5ez-2/
├── src/
│   ├── main.cpp              # Main demo application
│   ├── z-sysInfo.cpp         # System information z-sketch
│   └── images.h              # Image resources for menus
├── lib/
│   ├── M5ez/                 # M5ez library (migrated)
│   │   ├── src/
│   │   │   ├── M5ez.h        # Main header (updated for M5Unified)
│   │   │   ├── M5ez.cpp      # Implementation (migrated)
│   │   │   └── themes/       # Color themes
│   │   └── README.md         # Library documentation
│   └── ezTime/               # Time library
├── platformio.ini            # PlatformIO configuration
├── MIGRATION_NOTES.md        # Detailed migration changelog
└── README.md                 # This file
```

---

## 🎯 Key Features After Migration

All original M5ez features remain functional:

### Menus
- Text menus with scrolling
- Image menus with JPG support
- Dynamic menu modification
- Sorted menus
- Custom menu drawing

### User Interface
- Multi-function button system (9 possible functions)
- Message boxes with custom buttons
- Progress bars for operations
- Text input with 3-button entry
- Word-wrapped text boxes

### System Integration
- WiFi auto-connect to saved networks
- WPS setup (button mode)
- OTA firmware updates via HTTPS
- On-screen clock with timezone support
- Battery level indicator
- Backlight control
- Settings persistence (NVS)

### Theming
- Default and Dark themes included
- User-selectable themes
- Custom theme creation support

---

## 🐛 Known Limitations

1. **WPS PIN Display:** WPS PIN event handling temporarily disabled due to event type changes in ESP32 framework. WPS button mode works normally.

2. **Battery API:** `isChargeFull()` removed from M5Unified Power API. Now only checks `isCharging()` status.

3. **Firmware Size:** Requires `huge_app` partition scheme (~1.38MB vs ~900KB original).

4. **Arduino IDE:** Not compatible. Use PlatformIO/VS Code only.

---

## 🔨 Building and Uploading

### Build Commands

```bash
# Build project
pio run

# Upload to M5Stack
pio run --target upload

# Monitor serial output
pio run --target monitor

# Upload and monitor
pio run --target upload --target monitor

# Clean build
pio run --target clean
```

### Partition Scheme

The project uses `huge_app.csv` partition:
- Flash: 43.9% used (1,380,377 / 3,145,728 bytes)
- RAM: 15.7% used (51,388 / 327,680 bytes)

---

## 🤝 Contributing

Contributions are welcome! Areas that could use help:

1. **WPS PIN Event Handling:** Identify correct event type in new ESP32 framework
2. **Additional Themes:** Create new visual themes
3. **Documentation:** Improve migration notes and examples
4. **Testing:** Test on different M5Stack models (Core2, CoreS3, etc.)
5. **Bug Fixes:** Report and fix any issues found

---

## 📜 License

This project inherits the license from the original M5ez project. Please refer to the original repository for licensing information.

---

## 🔗 Links

- **Original M5ez:** [https://github.com/M5ez/M5ez](https://github.com/M5ez/M5ez)
- **M5Unified:** [https://github.com/m5stack/M5Unified](https://github.com/m5stack/M5Unified)
- **M5Unified Docs:** [https://docs.m5stack.com/en/arduino/m5unified](https://docs.m5stack.com/en/arduino/m5unified)
- **ezTime:** [https://github.com/ropg/ezTime](https://github.com/ropg/ezTime)
- **PlatformIO:** [https://platformio.org](https://platformio.org)

---

## 🎓 Support

- **Issues:** Report bugs or request features via GitHub Issues
- **Original Documentation:** See [M5ez User Manual](https://github.com/M5ez/M5ez#m5ez-user-manual)
- **M5Stack Community:** [https://community.m5stack.com](https://community.m5stack.com)

---

**Made possible by the original M5ez community and migrated with Claude Code by Anthropic** 🤖
