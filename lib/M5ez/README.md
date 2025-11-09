# M5ez Library - M5Unified Compatible Version

---

## 🙏 IMPORTANT CREDIT

### This is a **COMMUNITY MIGRATION** of the Original M5ez Library

**Original Author:** [Rop Gonggrijp](https://github.com/ropg)
**Original Repository:** [https://github.com/M5ez/M5ez](https://github.com/M5ez/M5ez)
**Original Version:** 2.3.0

**This migrated version** was created to make M5ez compatible with the modern **M5Unified** library suite.

---

## ⚠️ Important Notice

This is a **modified version** of M5ez that has been migrated to work with:
- **M5Unified v0.2.10+** (instead of the legacy M5Stack library)
- **M5GFX** (modern graphics library)
- **ESP32 Arduino Framework v3.x** (latest framework)

**For the original M5ez documentation and examples, please visit:**
📖 **[Original M5ez Repository](https://github.com/M5ez/M5ez)**

---

## 🔧 What Changed in This Version

This library has been modified to replace the old M5Stack library API with M5Unified APIs:

### Display API Changes
```cpp
// Old M5Stack API
m5.lcd.fillRect(...)
m5.lcd.drawString(...)

// New M5Unified API (used in this version)
M5.Display.fillRect(...)
M5.Display.drawString(...)
```

### Initialization Changes
```cpp
// Old
m5.begin();

// New (used in this version)
M5.begin();
```

### WiFi Event Handler Updates
- Updated to use new ESP32 v3.x WiFi event types
- `WiFiEvent_t` → `arduino_event_id_t`
- `system_event_info_t` → `arduino_event_info_t`

### Other Updates
- Power API: `m5.powerOFF()` → `M5.Power.powerOff()`
- Font API: `setFreeFont()` → `setFont()`
- WPS crypto_funcs removed (deprecated in ESP32 framework)
- Battery API simplified (isChargeFull removed)

**For complete technical details, see `MIGRATION_NOTES.md` in the project root.**

---

## 📚 Documentation

### Using M5ez

Since the M5ez API remains largely unchanged (only the underlying library calls were updated), the **original M5ez documentation** is your best resource:

**📖 [Complete M5ez User Manual](https://github.com/M5ez/M5ez#m5ez-user-manual)**

The original documentation covers:
- Getting started
- Screen, Header, Canvas, and Buttons
- Menus (text and image)
- Message boxes and progress bars
- 3-button text input
- WiFi configuration
- Settings and themes
- Complete API reference

---

## 🚀 Quick Start

### Prerequisites
- **Visual Studio Code** with PlatformIO extension
- **M5Stack Core** (ESP32 based)
- This project uses **PlatformIO**, not Arduino IDE

### Basic Example

```cpp
#include <M5ez.h>
#include <ezTime.h>

void setup() {
  #include <themes/default.h>
  ez.begin();
}

void loop() {
  ezMenu myMenu("Main Menu");
  myMenu.addItem("Hello World", helloWorld);
  myMenu.addItem("Settings", ez.settings.menu);
  myMenu.run();
}

void helloWorld() {
  ez.msgBox("Greeting", "Hello from M5ez + M5Unified!");
}
```

---

## ✨ Features

All original M5ez features are maintained:

- **Text & Image Menus** - With scrolling and dynamic updates
- **Multi-function Buttons** - Up to 9 button combinations
- **Message Boxes** - Customizable dialogs
- **3-Button Text Entry** - Clever text input system
- **WiFi Management** - Auto-connect with saved networks
- **On-screen Clock** - With timezone support
- **Theme System** - Multiple visual themes
- **Progress Bars** - For long operations
- **Settings Menu** - Persistent user preferences
- **Battery Indicator** - Real-time battery status

---

## 🔗 Dependencies

This version requires:
- **M5Unified** v0.2.10+ (auto-installed via PlatformIO)
- **M5GFX** (included with M5Unified)
- **ezTime** v0.8.3+ (for clock functionality)
- **ESP32 Arduino Framework** v3.x

---

## 📝 Migration Credit

**Migrated by:** Claude Code (Anthropic AI) in collaboration with the user
**Migration Date:** January 2025
**Migration Purpose:** Enable M5ez to work with modern M5Stack hardware and M5Unified library

All core functionality from the original M5ez v2.3.0 has been preserved. The migration focused solely on updating the underlying library calls from M5Stack to M5Unified.

---

## ⚖️ License

This library inherits the license from the original M5ez project. Please refer to the original repository for licensing terms.

---

## 🤝 Contributing

If you find issues or have improvements for this M5Unified-compatible version, please contribute! Areas that could use help:

1. Testing on different M5Stack models
2. WPS PIN event handling (currently disabled)
3. Additional themes
4. Documentation improvements
5. Bug fixes

---

## 🌟 Original M5ez Description

> *M5ez (pronounced "M5 easy") is a complete interface builder library for the M5Stack ESP32 system. It allows even novice programmers to create good looking interfaces. It comes with menus as text or as images, message boxes, very flexible button setup (including different length presses and multi-button functions), 3-button text input (you have to see it to believe it) and built-in Wifi support. Now you can concentrate on what your program does, and let M5ez worry about everything else.*

**— Rop Gonggrijp, Original M5ez Author**

---

## 🔗 Important Links

- **Original M5ez:** [https://github.com/M5ez/M5ez](https://github.com/M5ez/M5ez)
- **M5Unified:** [https://github.com/m5stack/M5Unified](https://github.com/m5stack/M5Unified)
- **M5Unified Docs:** [https://docs.m5stack.com](https://docs.m5stack.com)
- **ezTime:** [https://github.com/ropg/ezTime](https://github.com/ropg/ezTime)

---

**Thank you to Rop Gonggrijp and the M5ez community for creating such an excellent library!** 🎉

This migration ensures M5ez continues to be useful with modern M5Stack hardware and software.
