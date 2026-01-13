# Circuitpython - Getting Started

**Pages:** 2

---

## Adafruit CircuitPython API Reference

**URL:** https://docs.circuitpython.org/en/latest/docs/

**Contents:**
- Adafruit CircuitPython API Reference
- CircuitPython
- Get CircuitPython
- Documentation
- Contributing
- Branding
- Differences from MicroPython
  - Behavior
  - API
  - Modules

Welcome to the API reference documentation for Adafruit CircuitPython. This contains low-level API reference docs which may link out to separate “getting started” guides. Adafruit has many excellent tutorials available through the Adafruit Learning System.

circuitpython.org | Get CircuitPython | Documentation | Contributing | Branding | Differences from Micropython | Project Structure

CircuitPython is a beginner friendly, open source version of Python for tiny, inexpensive computers called microcontrollers. Microcontrollers are the brains of many electronics including a wide variety of development boards used to build hobby projects and prototypes. CircuitPython in electronics is one of the best ways to learn to code because it connects code to reality. Simply install CircuitPython on a supported USB board usually via drag and drop and then edit a code.py file on the CIRCUITPY drive. The code will automatically reload. No software installs are needed besides a text editor (we recommend Mu for beginners.)

Starting with CircuitPython 7.0.0, some boards may only be connectable over Bluetooth Low Energy (BLE). Those boards provide serial and file access over BLE instead of USB using open protocols. (Some boards may use both USB and BLE.) BLE access can be done from a variety of apps including code.circuitpython.org.

CircuitPython features unified Python core APIs and a growing list of 300+ device libraries and drivers that work with it. These libraries also work on single board computers with regular Python via the Adafruit Blinka Library.

CircuitPython is based on MicroPython. See below for differences. Most, but not all, CircuitPython development is sponsored by Adafruit and is available on their educational development boards. Please support both MicroPython and Adafruit.

Official binaries for all supported boards are available through circuitpython.org/downloads. The site includes stable, unstable and continuous builds. Full release notes are available through GitHub releases as well.

Guides and videos are available through the Adafruit Learning System under the CircuitPython category. An API reference is also available on Read the Docs. A collection of awesome resources can be found at Awesome CircuitPython.

Specifically useful documentation when starting out:

Welcome to CircuitPython

CircuitPython Essentials

See CONTRIBUTING.md for full guidelines but please be aware that by contributing to this project you are agreeing to the Code of Conduct. Contributors who follow the Code of Conduct are welcome to submit pull requests and they will be promptly reviewed by project admins. Please join the Discord too.

While we are happy to see CircuitPython forked and modified, we’d appreciate it if forked releases not use the name “CircuitPython” or the Blinka logo. “CircuitPython” means something special to us and those who learn about it. As a result, we’d like to make sure products referring to it meet a common set of requirements.

If you’d like to use the term “CircuitPython” and Blinka for your product here is what we ask:

Your product is supported by the primary “adafruit/circuitpython” repo. This way we can update any custom code as we update the CircuitPython internals.

Your product is listed on circuitpython.org (source here). This is to ensure that a user of your product can always download the latest version of CircuitPython from the standard place.

Your product supports at least one standard “Workflow” for serial and file access:

With a user accessible USB plug which appears as a CIRCUITPY drive when plugged in.

With file and serial access over Bluetooth Low Energy using the BLE Workflow.

With file access over WiFi using the WiFi Workflow with serial access over USB and/or WebSocket.

Boards that do not support the USB Workflow should be clearly marked.

If you choose not to meet these requirements, then we ask you call your version of CircuitPython something else (for example, SuperDuperPython) and not use the Blinka logo. You can say it is “CircuitPython-compatible” if most CircuitPython drivers will work with it.

Supports native USB on most boards and BLE otherwise, allowing file editing without special tools.

Floats (aka decimals) are enabled for all builds.

Error messages are translated into 10+ languages.

Concurrency within Python is not well supported. Interrupts and threading are disabled. async/await keywords are available on some boards for cooperative multitasking. Some concurrency is achieved with native modules for tasks that require it such as audio file playback.

The order that files are run and the state that is shared between them. CircuitPython’s goal is to clarify the role of each file and make each file independent from each other.

boot.py runs only once on start up before workflows are initialized. This lays the ground work for configuring USB at startup rather than it being fixed. Since serial is not available, output is written to boot_out.txt.

code.py (or main.py) is run after every reload until it finishes or is interrupted. After it is done running, the vm and hardware is reinitialized. This means you cannot read state from code.py in the REPL anymore, as the REPL is a fresh vm. CircuitPython’s goal for this change includes reducing confusion about pins and memory being used.

After the main code is finished the REPL can be entered by pressing any key. - If the file repl.py exists, it is executed before the REPL Prompt is shown - In safe mode this functionality is disabled, to ensure the REPL Prompt can always be reached

Autoreload state will be maintained across reload.

Adds a safe mode that does not run user code after a hard crash or brown out. This makes it possible to fix code that causes nasty crashes by making it available through mass storage after the crash. A reset (the button) is needed after it’s fixed to get back into normal mode.

A 1 second delay is added to the boot process during which time the status LED will flash, and resetting the device or pressing the boot button will force the device into safe mode. This delay can be removed by a compile time option (CIRCUITPY_SKIP_SAFE_MODE_WAIT).

Safe mode may be handled programmatically by providing a safemode.py. safemode.py is run if the board has reset due to entering safe mode, unless the safe mode initiated by the user by pressing button(s). USB is not available so nothing can be printed. safemode.py can determine why the safe mode occurred using supervisor.runtime.safe_mode_reason, and take appropriate action. For instance, if a hard crash occurred, safemode.py may do a microcontroller.reset() to automatically restart despite the crash. If the battery is low, but is being charged, safemode.py may put the board in deep sleep for a while. Or it may simply reset, and have code.py check the voltage and do the sleep.

RGB status LED indicating CircuitPython state. - One green flash - code completed without error. - Two red flashes - code ended due to an exception. - Three yellow flashes - safe mode. May be due to CircuitPython internal error.

Re-runs code.py or other main file after file system writes by a workflow. (Disable with supervisor.disable_autoreload())

Autoreload is disabled while the REPL is active.

code.py may also be named code.txt, main.py, or main.txt.

boot.py may also be named boot.txt.

safemode.py may also be named safemode.txt.

Unified hardware APIs. Documented on ReadTheDocs.

API docs are Python stubs within the C files in shared-bindings.

No module aliasing. (uos and utime are not available as os and time respectively.) Instead os, time, and random are CPython compatible.

New storage module which manages file system mounts. (Functionality from uos in MicroPython.)

Modules with a CPython counterpart, such as time, os and random, are strict subsets of their CPython version. Therefore, code from CircuitPython is runnable on CPython but not necessarily the reverse.

tick count is available as time.monotonic()

Here is an overview of the top-level source code directories.

The core code of MicroPython is shared amongst ports including CircuitPython:

docs High level user documentation in Sphinx reStructuredText format.

drivers External device drivers written in Python.

examples A few example Python scripts.

extmod Shared C code used in multiple ports’ modules.

lib Shared core C code including externally developed libraries such as FATFS.

logo The CircuitPython logo.

mpy-cross A cross compiler that converts Python files to byte code prior to being run in MicroPython. Useful for reducing library size.

py Core Python implementation, including compiler, runtime, and core library.

shared-bindings Shared definition of Python modules, their docs and backing C APIs. Ports must implement the C API to support the corresponding module.

shared-module Shared implementation of Python modules that may be based on common-hal.

tests Test framework and test scripts.

tools Various tools, including the pyboard.py module.

Ports include the code unique to a microcontroller line.

The following ports are available: atmel-samd, cxd56, espressif, litex, mimxrt10xx, nordic, raspberrypi, renode, silabs (efr32), stm, unix.

However, not all ports are fully functional. Some have limited functionality and known serious bugs. For details, refer to the Port status section in the latest release notes.

Each port has a boards directory containing boards which belong to a specific microcontroller line.

A list of native modules supported by a particular board can be found here.

Design and porting reference

---

## Adafruit CircuitPython API Reference

**URL:** https://docs.circuitpython.org/en/latest/docs/index.html

**Contents:**
- Adafruit CircuitPython API Reference
- CircuitPython
- Get CircuitPython
- Documentation
- Contributing
- Branding
- Differences from MicroPython
  - Behavior
  - API
  - Modules

Welcome to the API reference documentation for Adafruit CircuitPython. This contains low-level API reference docs which may link out to separate “getting started” guides. Adafruit has many excellent tutorials available through the Adafruit Learning System.

circuitpython.org | Get CircuitPython | Documentation | Contributing | Branding | Differences from Micropython | Project Structure

CircuitPython is a beginner friendly, open source version of Python for tiny, inexpensive computers called microcontrollers. Microcontrollers are the brains of many electronics including a wide variety of development boards used to build hobby projects and prototypes. CircuitPython in electronics is one of the best ways to learn to code because it connects code to reality. Simply install CircuitPython on a supported USB board usually via drag and drop and then edit a code.py file on the CIRCUITPY drive. The code will automatically reload. No software installs are needed besides a text editor (we recommend Mu for beginners.)

Starting with CircuitPython 7.0.0, some boards may only be connectable over Bluetooth Low Energy (BLE). Those boards provide serial and file access over BLE instead of USB using open protocols. (Some boards may use both USB and BLE.) BLE access can be done from a variety of apps including code.circuitpython.org.

CircuitPython features unified Python core APIs and a growing list of 300+ device libraries and drivers that work with it. These libraries also work on single board computers with regular Python via the Adafruit Blinka Library.

CircuitPython is based on MicroPython. See below for differences. Most, but not all, CircuitPython development is sponsored by Adafruit and is available on their educational development boards. Please support both MicroPython and Adafruit.

Official binaries for all supported boards are available through circuitpython.org/downloads. The site includes stable, unstable and continuous builds. Full release notes are available through GitHub releases as well.

Guides and videos are available through the Adafruit Learning System under the CircuitPython category. An API reference is also available on Read the Docs. A collection of awesome resources can be found at Awesome CircuitPython.

Specifically useful documentation when starting out:

Welcome to CircuitPython

CircuitPython Essentials

See CONTRIBUTING.md for full guidelines but please be aware that by contributing to this project you are agreeing to the Code of Conduct. Contributors who follow the Code of Conduct are welcome to submit pull requests and they will be promptly reviewed by project admins. Please join the Discord too.

While we are happy to see CircuitPython forked and modified, we’d appreciate it if forked releases not use the name “CircuitPython” or the Blinka logo. “CircuitPython” means something special to us and those who learn about it. As a result, we’d like to make sure products referring to it meet a common set of requirements.

If you’d like to use the term “CircuitPython” and Blinka for your product here is what we ask:

Your product is supported by the primary “adafruit/circuitpython” repo. This way we can update any custom code as we update the CircuitPython internals.

Your product is listed on circuitpython.org (source here). This is to ensure that a user of your product can always download the latest version of CircuitPython from the standard place.

Your product supports at least one standard “Workflow” for serial and file access:

With a user accessible USB plug which appears as a CIRCUITPY drive when plugged in.

With file and serial access over Bluetooth Low Energy using the BLE Workflow.

With file access over WiFi using the WiFi Workflow with serial access over USB and/or WebSocket.

Boards that do not support the USB Workflow should be clearly marked.

If you choose not to meet these requirements, then we ask you call your version of CircuitPython something else (for example, SuperDuperPython) and not use the Blinka logo. You can say it is “CircuitPython-compatible” if most CircuitPython drivers will work with it.

Supports native USB on most boards and BLE otherwise, allowing file editing without special tools.

Floats (aka decimals) are enabled for all builds.

Error messages are translated into 10+ languages.

Concurrency within Python is not well supported. Interrupts and threading are disabled. async/await keywords are available on some boards for cooperative multitasking. Some concurrency is achieved with native modules for tasks that require it such as audio file playback.

The order that files are run and the state that is shared between them. CircuitPython’s goal is to clarify the role of each file and make each file independent from each other.

boot.py runs only once on start up before workflows are initialized. This lays the ground work for configuring USB at startup rather than it being fixed. Since serial is not available, output is written to boot_out.txt.

code.py (or main.py) is run after every reload until it finishes or is interrupted. After it is done running, the vm and hardware is reinitialized. This means you cannot read state from code.py in the REPL anymore, as the REPL is a fresh vm. CircuitPython’s goal for this change includes reducing confusion about pins and memory being used.

After the main code is finished the REPL can be entered by pressing any key. - If the file repl.py exists, it is executed before the REPL Prompt is shown - In safe mode this functionality is disabled, to ensure the REPL Prompt can always be reached

Autoreload state will be maintained across reload.

Adds a safe mode that does not run user code after a hard crash or brown out. This makes it possible to fix code that causes nasty crashes by making it available through mass storage after the crash. A reset (the button) is needed after it’s fixed to get back into normal mode.

A 1 second delay is added to the boot process during which time the status LED will flash, and resetting the device or pressing the boot button will force the device into safe mode. This delay can be removed by a compile time option (CIRCUITPY_SKIP_SAFE_MODE_WAIT).

Safe mode may be handled programmatically by providing a safemode.py. safemode.py is run if the board has reset due to entering safe mode, unless the safe mode initiated by the user by pressing button(s). USB is not available so nothing can be printed. safemode.py can determine why the safe mode occurred using supervisor.runtime.safe_mode_reason, and take appropriate action. For instance, if a hard crash occurred, safemode.py may do a microcontroller.reset() to automatically restart despite the crash. If the battery is low, but is being charged, safemode.py may put the board in deep sleep for a while. Or it may simply reset, and have code.py check the voltage and do the sleep.

RGB status LED indicating CircuitPython state. - One green flash - code completed without error. - Two red flashes - code ended due to an exception. - Three yellow flashes - safe mode. May be due to CircuitPython internal error.

Re-runs code.py or other main file after file system writes by a workflow. (Disable with supervisor.disable_autoreload())

Autoreload is disabled while the REPL is active.

code.py may also be named code.txt, main.py, or main.txt.

boot.py may also be named boot.txt.

safemode.py may also be named safemode.txt.

Unified hardware APIs. Documented on ReadTheDocs.

API docs are Python stubs within the C files in shared-bindings.

No module aliasing. (uos and utime are not available as os and time respectively.) Instead os, time, and random are CPython compatible.

New storage module which manages file system mounts. (Functionality from uos in MicroPython.)

Modules with a CPython counterpart, such as time, os and random, are strict subsets of their CPython version. Therefore, code from CircuitPython is runnable on CPython but not necessarily the reverse.

tick count is available as time.monotonic()

Here is an overview of the top-level source code directories.

The core code of MicroPython is shared amongst ports including CircuitPython:

docs High level user documentation in Sphinx reStructuredText format.

drivers External device drivers written in Python.

examples A few example Python scripts.

extmod Shared C code used in multiple ports’ modules.

lib Shared core C code including externally developed libraries such as FATFS.

logo The CircuitPython logo.

mpy-cross A cross compiler that converts Python files to byte code prior to being run in MicroPython. Useful for reducing library size.

py Core Python implementation, including compiler, runtime, and core library.

shared-bindings Shared definition of Python modules, their docs and backing C APIs. Ports must implement the C API to support the corresponding module.

shared-module Shared implementation of Python modules that may be based on common-hal.

tests Test framework and test scripts.

tools Various tools, including the pyboard.py module.

Ports include the code unique to a microcontroller line.

The following ports are available: atmel-samd, cxd56, espressif, litex, mimxrt10xx, nordic, raspberrypi, renode, silabs (efr32), stm, unix.

However, not all ports are fully functional. Some have limited functionality and known serious bugs. For details, refer to the Port status section in the latest release notes.

Each port has a boards directory containing boards which belong to a specific microcontroller line.

A list of native modules supported by a particular board can be found here.

Design and porting reference

---
