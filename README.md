# Auditor-a-de-Hid-Rasberry-Pi-Pico

# HID Audit Automation using Raspberry Pi / USB HID

## 📌 Overview

This project documents the development of a HID (Human Interface Device) automation workflow using a Raspberry Pi-compatible HID device to perform controlled Windows audit and inventory collection tasks.

The project focuses on:

* Stability
* Reproducibility
* Keyboard layout reliability
* PowerShell automation
* Safe inventory collection
* HID payload engineering

This repository was created as a learning, testing, and controlled auditing environment.

---

# 🧠 Project Goals

The initial objective was to:

* Automate Windows inventory collection
* Use HID keyboard injection techniques
* Generate an audit report automatically
* Avoid instability caused by keyboard layouts
* Improve execution speed and reliability

---

# ⚠️ Important Lessons Learned

During development several critical problems appeared:

| Problem                       | Cause                          | Solution                       |
| ----------------------------- | ------------------------------ | ------------------------------ |
| Commands executed incorrectly | ES keyboard layout mismatch    | Use EN-US layout               |
| Missing characters            | Long payload strings           | Character-by-character writing |
| PowerShell syntax errors      | CMD commands inside PowerShell | Use PowerShell-native commands |
| Focus hijacking               | Browser or apps stealing focus | Open applications at the end   |
| Payload instability           | Pipes and complex symbols      | Use simple sequential commands |

---

# 🏗️ Architecture Diagram

<img width="4702" height="4464" alt="image" src="https://github.com/user-attachments/assets/7620ae95-cf54-4581-9734-bc5a0ca67e89" />


---

# ⚙️ Technologies Used

## Hardware

* Raspberry Pi Pico / HID-compatible device
* USB connection

## Software

* CircuitPython
* Adafruit HID Library
* PowerShell
* Windows 10 / 11

---

# 📂 Project Structure

```text
project/
│
├── code.py
├── boot.py
├── README.md
├── docs/
│   ├── architecture.md
│   ├── troubleshooting.md
│   └── payload_notes.md
│
└── examples/
    ├── basic_payload.py
    ├── audit_payload.py
    └── stable_payload.py
```

---

# 🔧 Environment Setup

## 1. Install CircuitPython

Download and flash CircuitPython to the HID device.

## 2. Install Required Libraries

Place inside:

```text
/lib
```

Required:

```text
adafruit_hid
```

---

# ⚙️ boot.py Configuration

```python
import usb_hid

usb_hid.enable((usb_hid.Device.KEYBOARD,))
```

---

# ⚙️ Core Payload Structure

## Basic HID Initialization

```python
import time
import board
import usb_hid

from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keyboard_layout_us import KeyboardLayoutUS
from adafruit_hid.keycode import Keycode

kbd = Keyboard(usb_hid.devices)
layout = KeyboardLayoutUS(kbd)
```

---

## Safe Writing Function

```python
def escribir(texto, d=0.025):
    for c in texto:
        layout.write(c)
        time.sleep(d)
```

### Why character-by-character?

Using:

```python
layout.write(payload)
```

for long strings caused:

* lost characters
* corrupted commands
* PowerShell syntax failures

Character-by-character writing greatly improved stability.

---

## ENTER Helper

```python
def enter():
    kbd.send(Keycode.ENTER)
    time.sleep(1)
```

---

# 🚀 Execution Flow

## Step 1 — Initial Delay

```python
time.sleep(5)
```

Allows:

* USB initialization
* Windows recognition
* HID enumeration

---

## Step 2 — Open Run Window

```python
kbd.press(Keycode.GUI, Keycode.R)
kbd.release_all()
```

Equivalent to:

```text
Windows + R
```

---

## Step 3 — Launch PowerShell

```python
escribir("powershell")
enter()
```

---

## Step 4 — Execute Sequential Commands

Commands are executed one-by-one.

Avoid:

* pipes (`|`)
* semicolons (`;`)
* long payloads
* nested commands

---

# 📊 Inventory Commands

## Identity

```powershell
whoami
hostname
```

---

## System Information

```powershell
systeminfo
wmic os get Caption
wmic os get Version
wmic computersystem get model
```

---

## Hardware

```powershell
wmic cpu get name
wmic memorychip get capacity
```

---

## Network

```powershell
ipconfig
arp -a
route print
netstat -ano
```

---

## WiFi Profiles

```powershell
netsh wlan show profiles
```

---

## Firewall

```powershell
netsh advfirewall show allprofiles
```

---

## Shared Resources

```powershell
net share
```

---

## Users and Groups

```powershell
net user
net localgroup
```

---

## Processes and Services

```powershell
tasklist
sc query
```

---

## Updates

```powershell
wmic qfe get HotFixID
```

---

# 📁 Output File

Generated file:

```text
audit.txt
```

Default location:

```text
C:\Users\<user>
```

---

# 🧠 Keyboard Layout Problems

## Critical Discovery

The payload only behaved reliably under:

```text
EN-US Keyboard Layout
```

### Examples of broken mappings

| Expected | Received |
| -------- | -------- |
| ;        | ñ        |
| :        | Ñ        |
| -        | '        |
| /        | -        |

---

# ❌ Commands That Caused Problems

## Avoid using:

```powershell
|
;
:
$env:
complex redirections
```

## Why?

HID injection becomes unstable with:

* long payloads
* parsing logic
* PowerShell pipes
* keyboard localization

---

# 🧪 Testing Methodology

## Tested On

* Windows 10
* Windows 11
* English keyboard layout
* Multiple timing configurations

---

# ⚡ Stability Recommendations

## Recommended Delays

```python
0.02 - 0.03
```

---

## If characters are lost

Increase:

```python
time.sleep()
```

and writing delay.

---

# 🚫 Scope Limitations

The project intentionally avoided:

* password extraction
* credential dumping
* hidden execution
* stealth persistence
* exfiltration

The focus remained:

* inventory
* automation
* reliability
* controlled auditing

---

# 📚 Troubleshooting

## Problem: Commands type incorrectly

### Solution

Use:

```text
EN-US keyboard
```

---

## Problem: PowerShell hangs

### Cause

Complex pipes or redirections.

### Fix

Use sequential commands.

---

## Problem: Lost characters

### Fix

Write character-by-character.

---

# 🔮 Future Improvements

* Modular command blocks
* Multi-target OS support
* Better timing adaptation
* Validation and logging
* GUI launcher

---

# 📌 Final Notes

This project demonstrated that:

* HID reliability depends heavily on keyboard layout
* Simplicity is more stable than complexity
* Sequential execution is safer than long payloads
* PowerShell and CMD are not interchangeable

---

# 🏷️ Tags

```text
#HID
#USB
#PowerShell
#Automation
#Windows
#Audit
#Cybersecurity
#RedTeam
#Python
#CircuitPython
```

---

---

# 📓 OBSIDIAN STUDY NOTEBOOK VERSION

# HID Payload Engineering Notebook

## 📚 Purpose

This notebook serves as:

* A long-term study reference
* A replication guide
* A troubleshooting knowledge base
* A payload engineering handbook

---

# MODULE 1 — HID Fundamentals

## What is HID?

HID = Human Interface Device.

Examples:

* Keyboard
* Mouse
* Gamepad

The target computer trusts HID devices automatically.

---

## Why HID Automation Works

The operating system interprets the device as:

```text
A real keyboard
```

Therefore:

* no drivers are usually required
* keystrokes are accepted directly

---

# MODULE 2 — CircuitPython Setup

## Installing CircuitPython

1. Download firmware
2. Put board into bootloader mode
3. Flash UF2 file
4. Device mounts as USB drive

---

## Required Files

```text
code.py
boot.py
/lib/adafruit_hid
```

---

## boot.py

```python
import usb_hid
usb_hid.enable((usb_hid.Device.KEYBOARD,))
```

Purpose:

* enables keyboard emulation

---

# MODULE 3 — Understanding HID Timing

## Most Important Lesson

HID automation fails mostly because of:

```text
Timing issues
```

---

## Symptoms

* missing letters
* incomplete commands
* random failures

---

## Solution

Use delays:

```python
time.sleep()
```

and:

```python
for c in texto:
```

---

# MODULE 4 — Keyboard Layout Problems

## The Biggest Real-World Problem

Different layouts transform characters.

---

## ES vs EN Examples

| EN | ES |
| -- | -- |
| ;  | ñ  |
| :  | Ñ  |
| -  | '  |
| /  | -  |

---

## Final Decision

Always use:

```text
EN-US layout
```

---

# MODULE 5 — PowerShell vs CMD

## Important Difference

These are NOT identical.

---

## Example of Failure

```powershell
date /t
```

works in CMD but fails in PowerShell.

---

## Correct PowerShell Equivalent

```powershell
Get-Date
```

---

# MODULE 6 — Payload Engineering

## Bad Payload Practices

### Avoid:

```powershell
pipes |
complex chains
nested commands
long one-liners
```

---

## Good Payload Practices

### Use:

* simple commands
* one command per ENTER
* sequential execution
* short strings

---

# MODULE 7 — Stable Audit Collection

## Recommended Commands

### Identity

```powershell
whoami
hostname
```

---

### System

```powershell
systeminfo
```

---

### Network

```powershell
ipconfig
netstat -ano
```

---

### Firewall

```powershell
netsh advfirewall show allprofiles
```

---

### Shares

```powershell
net share
```

---

### Services

```powershell
sc query
```

---

# MODULE 8 — Troubleshooting Checklist

## If nothing happens

* verify HID detection
* verify USB cable
* verify boot.py

---

## If commands type incorrectly

* verify keyboard language
* switch to EN-US

---

## If PowerShell hangs

* remove pipes
* split commands

---

## If characters disappear

* increase delays

---

# MODULE 9 — Recommended Workflow

## Stable Development Cycle

1. Test Notepad
2. Test Win + R
3. Test PowerShell
4. Test one command
5. Test redirection
6. Add complexity slowly

---

# MODULE 10 — Notes and Best Practices

## Best Practice

Simple payloads are:

* faster
* more reliable
* easier to debug

---

## Golden Rule

```text
Simplicity beats complexity in HID automation.
```

# ⚠️ Disclaimer

This project was developed exclusively for educational purposes, controlled security research, and authorized auditing environments.

The implementation focuses on:

* HID automation research
* PowerShell scripting
* USB HID device behavior
* Windows inventory and audit collection
* Stability and payload engineering

This project is **NOT designed for malicious activity**, credential theft, persistence, unauthorized access, or covert execution.
For this reason:

* execution is intentionally visible
* no stealth or persistence mechanisms were implemented
* no background exfiltration features were added

The author assumes:

* all testing is performed on systems owned by the tester or explicitly authorized environments
* users understand and comply with applicable laws and policies

The author is **not responsible for misuse, unauthorized deployment, or illegal activities** performed using any concepts, code, or documentation contained in this repository.

Use responsibly and ethically.

## 🎓 Educational Focus

This project was created as a learning and research exercise focused on:

* USB HID device behavior
* CircuitPython automation
* PowerShell interaction
* Windows inventory collection
* HID payload stability
* Keyboard layout handling
* Timing and execution reliability
* Real-world troubleshooting methodologies

The primary objective is to document the engineering process, lessons learned, and technical challenges encountered while building stable HID-based automation workflows in controlled environments.

The repository emphasizes:

* debugging
* testing methodology
* architecture design
* execution flow analysis
* reliability improvements

rather than offensive capabilities or covert execution.

---

# ⚠️ Project Limitations

This public repository intentionally excludes:

* fully operational payloads
* stealth execution mechanisms
* persistence techniques
* credential extraction
* exfiltration functionality
* hidden/background execution
* automated offensive workflows

Several implementation details were deliberately removed or sanitized to prevent misuse while preserving the educational and research value of the project.

The focus remains on:

* HID engineering concepts
* PowerShell behavior
* automation reliability
* hardware/software interaction
* troubleshooting and debugging practices

---

# 🛠️ HID Troubleshooting & Layout Challenges

One of the most important aspects documented in this project is the instability that can occur during HID automation.

## Key Issues Encountered

### Keyboard Layout Mismatches

Different keyboard layouts caused character translation problems:

| Expected | Received |
| -------- | -------- |
| ;        | ñ        |
| :        | Ñ        |
| /        | -        |
| -        | '        |

This demonstrated how strongly HID payloads depend on the target system language and keyboard configuration.

---

## Timing Instability

Long payloads and rapid execution caused:

* missing characters
* malformed commands
* PowerShell syntax errors
* interrupted execution

### Mitigation

The final approach prioritized:

* character-by-character writing
* controlled delays
* sequential execution
* simplified commands

---

## PowerShell vs CMD Behavior

Several commands behaved differently depending on the shell environment.

Example:

```powershell id="95vjlwm"
date /t
```

works in CMD but fails in PowerShell.

This highlighted the importance of:

* environment awareness
* shell-specific syntax
* incremental testing

---

## Focus and Window Control

Applications such as browsers or editors occasionally intercepted keyboard input, breaking payload execution.

The project therefore adopted:

* minimal focus switching
* sequential workflows
* simplified execution chains

---

# 🔒 Responsible Disclosure & Public Release Notes

This repository is a sanitized public release intended for:

* education
* research
* documentation
* portfolio demonstration

Operational payloads and sensitive implementation details were intentionally omitted.

The project is not intended to facilitate unauthorized access, credential collection, persistence, or malicious deployment.

All demonstrations and testing were performed in controlled and authorized environments.

---

# 📌 Additional Notes

The value of this project is not the payload itself, but rather:

* the engineering process
* troubleshooting methodology
* HID reliability analysis
* layout handling strategies
* PowerShell behavior analysis
* iterative debugging techniques

The repository should be viewed as:

* a technical research notebook
* a HID automation study
* a documentation and learning resource

rather than an operational offensive toolkit.

