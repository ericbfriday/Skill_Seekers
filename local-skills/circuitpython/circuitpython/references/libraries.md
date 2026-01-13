# Circuitpython - Libraries

**Pages:** 18

---

## Standard Libraries

**URL:** https://docs.circuitpython.org/en/latest/docs/library/index.html

**Contents:**
- Standard Libraries
- Python standard libraries
- Omitted string functions
- CircuitPython/MicroPython-specific libraries

The libraries below implement a subset of the corresponding standard Python (CPython) library. They are implemented in C, not Python.

CircuitPython’s long-term goal is that code written in CircuitPython using Python standard libraries will be runnable on CPython without changes.

These libraries are not enabled on CircuitPython builds with limited flash memory: binascii, errno, json, re.

These libraries are not currently enabled in any CircuitPython build, but may be in the future: platform

A few string operations are not enabled on small builds due to limited flash memory: string.center(), string.partition(), string.splitlines(), string.reversed().

Functionality specific to the CircuitPython/MicroPython implementations is available in the following libraries.

---

## Architecture

**URL:** https://docs.circuitpython.org/en/latest/docs/porting.html

**Contents:**
- Architecture
- Porting
- Step 1: Getting building
- Step 2: Init
- Step 3: REPL

We love CircuitPython and would love to see it come to more microcontroller platforms. Since 3.0 we’ve reworked CircuitPython to make it easier than ever to add support. While there are some major differences between ports, this page covers the similarities that make CircuitPython what it is and how that core fits into a variety of microcontrollers.

There are three core pieces to CircuitPython:

The first is the Python VM that the awesome MicroPython devs have created. These VMs are written to be portable so there is not much needed when moving to a different microcontroller, especially if it is ARM based.

The second is the infrastructure around those VMs which provides super basic operating system functionality such as initializing hardware, running USB, prepping file systems and automatically running user code on boot. In CircuitPython we’ve dubbed this component the supervisor because it monitors and facilitates the VMs which run user Python code. Porting involves the supervisor because many of the tasks it does while interfacing with the hardware. Once complete, the REPL works and debugging can migrate to a Python based approach rather than C.

The third core piece is the plethora of low level APIs that CircuitPython provides as the foundation for higher level libraries including device drivers. These APIs are called from within the running VMs through the Python interfaces defined in shared-bindings. These bindings rely on the underlying common_hal C API to implement the functionality needed for the Python API. By splitting the two, we work to ensure standard functionality across which means that libraries and examples apply across ports with minimal changes.

The first step to porting to a new microcontroller is getting a build running. The primary goal of it should be to get main.c compiling with the assistance of the supervisor/supervisor.mk file. Port specific code should be isolated to the port’s directory (in the top level until the ports directory is present). This includes the Makefile and any C library resources. Make sure these resources are compatible with the MIT License of the rest of the code!

Circuitpython has a number of modules enabled by default in py/circuitpy_mpconfig.mk. Most of these modules will need to be disabled in mpconfigboard.mk during the early stages of a port in order for it to compile. As the port progresses in module support, this list can be pruned down as a natural “TODO” list. An example minimal build list is shown below:

Once your build is setup, the next step should be to get your clocks going as you expect from the supervisor. The supervisor calls port_init to allow for initialization at the beginning of main. This function also has the ability to request a safe mode state which prevents the supervisor from running user code while still allowing access to the REPL and other resources.

The core port initialization and reset methods are defined in supervisor/port.c and should be the first to be implemented. It’s required that they be implemented in the supervisor directory within the port directory. That way, they are always in the expected place.

The supervisor also uses three linker variables, _ezero, _estack and _ebss to determine memory layout for stack overflow checking.

Getting the REPL going is a huge step. It involves a bunch of initialization to be done correctly and is a good sign you are well on your porting way. To get the REPL going you must implement the functions and definitions from supervisor/serial.h with a corresponding supervisor/serial.c in the port directory. This involves sending and receiving characters over some sort of serial connection. It could be UART or USB for example.

**Examples:**

Example 1 (typescript):
```typescript
# These modules are implemented in ports/<port>/common-hal:

# Typically the first module to create
CIRCUITPY_MICROCONTROLLER = 0
# Typically the second module to create
CIRCUITPY_DIGITALIO = 0
# Other modules:
CIRCUITPY_ANALOGIO = 0
CIRCUITPY_BUSIO = 0
CIRCUITPY_COUNTIO = 0
CIRCUITPY_NEOPIXEL_WRITE = 0
CIRCUITPY_PULSEIO = 0
CIRCUITPY_OS = 0
CIRCUITPY_NVM = 0
CIRCUITPY_AUDIOBUSIO = 0
CIRCUITPY_AUDIOIO = 0
CIRCUITPY_ROTARYIO = 0
CIRCUITPY_RTC = 0
CIRCUITPY_SDCARDIO = 0
CIRCUITPY_FRAMEBUFFERIO = 0
CIRCUITPY_FREQUENCYIO = 0
CIRCUITPY_I2CTARGET = 0
CIRCUITPY_SPITARGET = 0
# Requires SPI, PulseIO (stub ok):
CIRCUITPY_DISPLAYIO = 0

# These modules are implemented in shared-module/ - they can be included in
# any port once their prerequisites in common-hal are complete.
# Requires DigitalIO:
CIRCUITPY_BITBANGIO = 0
# Requires neopixel_write or SPI (dotstar)
CIRCUITPY_PIXELBUF = 0
# Requires OS
CIRCUITPY_RANDOM = 0
# Requires OS, filesystem
CIRCUITPY_STORAGE = 0
# Requires Microcontroller
CIRCUITPY_TOUCHIO = 0
# Requires USB
CIRCUITPY_USB_HID = 0
CIRCUITPY_USB_MIDI = 0
# Does nothing without I2C
CIRCUITPY_REQUIRE_I2C_PULLUPS = 0
# No requirements, but takes extra flash
CIRCUITPY_ULAB = 0
```

---

## Design Guide

**URL:** https://docs.circuitpython.org/en/latest/docs/design_guide.html

**Contents:**
- Design Guide
- Start libraries with the cookiecutter
- Module Naming
- Terminology
- Lifetime and ContextManagers
- Verify your device
- Getters/Setters
- Exceptions and asserts
- Design for compatibility with CPython
  - Example

This guide covers a variety of development practices for CircuitPython core and library APIs. These APIs are both built-into CircuitPython and those that are distributed on GitHub and in the Adafruit and Community bundles. Consistency with these practices ensures that beginners can learn a pattern once and apply it throughout the CircuitPython ecosystem.

Cookiecutter is a tool that lets you bootstrap a new repo based on another repo. We’ve made one here for CircuitPython libraries that include configs for Travis CI and ReadTheDocs along with a setup.py, license, code of conduct, readme among other files.

Cookiecutter will provide a series of prompts relating to the library and then create a new directory with all of the files. See the CircuitPython cookiecutter README for more details.

Adafruit funded libraries should be under the adafruit organization and have the format Adafruit_CircuitPython_<name> and have a corresponding adafruit_<name> directory (aka package) or adafruit_<name>.py file (aka module).

If the name would normally have a space, such as “Thermal Printer”, use an underscore instead (“Thermal_Printer”). This underscore will be used everywhere even when the separation between “adafruit” and “circuitpython” is done with a -. Use the underscore in the cookiecutter prompts.

Community created libraries should have the repo format CircuitPython_<name> and not have the adafruit_ module or package prefix.

Both should have the CircuitPython repository topic on GitHub.

As our Code of Conduct states, we strive to use “welcoming and inclusive language.” Whether it is in documentation or in code, the words we use matter. This means we disfavor language that due to historical and social context can make community members and potential community members feel unwelcome.

There are specific terms to avoid except where technical limitations require it. While specific cases may call for other terms, consider using these suggested terms first:

Note that “technical limitations” refers e.g., to the situation where an upstream library or URL has to contain those substrings in order to work. However, when it comes to documentation and the names of parameters and properties in CircuitPython, we will use alternate terms even if this breaks tradition with past practice.

A driver should be initialized and ready to use after construction. If the device requires deinitialization, then provide it through deinit() and also provide __enter__ and __exit__ to create a context manager usable with with.

For example, a user can then use deinit()`:

This will deinit the underlying hardware at the end of the program as long as no exceptions occur.

Alternatively, using a with statement ensures that the hardware is deinitialized:

Python’s with statement ensures that the deinit code is run regardless of whether the code within the with statement executes without exceptions.

For small programs like the examples this isn’t a major concern because all user usable hardware is reset after programs are run or the REPL is run. However, for more complex programs that may use hardware intermittently and may also handle exceptions on their own, deinitializing the hardware using a with statement will ensure hardware isn’t enabled longer than needed.

Whenever possible, make sure the device you are talking to is the device you expect. If not, raise a RuntimeError. Beware that I2C addresses can be identical on different devices so read registers you know to make sure they match your expectation. Validating this upfront will help catch mistakes.

When designing a driver for a device, use properties for device state and use methods for sequences of abstract actions that the device performs. State is a property of the device as a whole that exists regardless of what the code is doing. This includes things like temperature, time, sound, light and the state of a switch. For a more complete list see the sensor properties bullet below.

Another way to separate state from actions is that state is usually something the user can sense themselves by sight or feel for example. Actions are something the user can watch. The device does this and then this.

Making this separation clear to the user will help beginners understand when to use what.

Here is more info on properties from Python.

Raise an appropriate Exception, along with a useful message, whenever a critical test or other condition fails.

If memory is constrained and a more compact method is needed, use The assert statement instead.

CircuitPython is aimed to be one’s first experience with code. It will be the first step into the world of hardware and software. To ease one’s exploration out from this first step, make sure that functionality shared with CPython shares the same API. It doesn’t need to be the full API it can be a subset. However, do not add non-CPython APIs to the same modules. Instead, use separate non-CPython modules to add extra functionality. By distinguishing API boundaries at modules you increase the likelihood that incorrect expectations are found on import and not randomly during runtime.

When adding a new module for additional functionality related to a CPython module do NOT simply prefix it with u. This is not a large enough differentiation from CPython. This is the MicroPython convention and they use u* modules interchangeably with the CPython name. This is confusing. Instead, think up a new name that is related to the extra functionality you are adding.

For example, storage mounting and unmounting related functions were moved from uos into a new storage module. These names better match their functionality and do not conflict with CPython names. Make sure to check that you don’t conflict with CPython libraries too. That way we can port the API to CPython in the future.

When adding extra functionality to CircuitPython to mimic what a normal operating system would do, either copy an existing CPython API (for example file writing) or create a separate module to achieve what you want. For example, mounting and unmounting drives is not a part of CPython so it should be done in a module, such as a new storage module, that is only available in CircuitPython. That way when someone moves the code to CPython they know what parts need to be adapted.

Whenever possible, document your code right next to the code that implements it. This makes it more likely to stay up to date with the implementation itself. Use Sphinx’s automodule to format these all nicely in ReadTheDocs. The cookiecutter helps set these up.

Use Sphinx flavor rST for markup.

Lots of documentation is a good thing but it can take a lot of space. To minimize the space used on disk and on load, distribute the library as both .py and .mpy, MicroPython and CircuitPython’s bytecode format that omits comments.

After the license comment:

After the import statements:

At the class level document what class does and how to initialize it:

DS3231 real-time clock.

i2c_bus (I2C) – The I2C bus the DS3231 is connected to.

address (int) – The I2C address of the device. Defaults to 0x40

Although there are different ways to document class and functions definitions in Python, the following is the prevalent method of documenting parameters for CircuitPython libraries. When documenting class parameters you should use the following structure:

The type of the parameter. This could be, among others, int, float, str, bool, etc. To document an object in the CircuitPython domain, you need to include a ~ before the definition as shown in the following example:

To include references to CircuitPython modules, cookiecutter creates an entry in the intersphinx_mapping section in the conf.py file located within the docs directory. To add different types outside CircuitPython you need to include them in the intersphinx_mapping:

The intersphinx_mapping above includes references to Python, BusDevice and CircuitPython Documentation

When the parameter have two different types, you should reference them as follows:

Base class for character LCD

rs (DigitalInOut) – The reset data line

blue (PWMOut,DigitalInOut) – Blue RGB Anode

Parameter name used in the class or method definition

Parameter description. When the parameter defaults to a particular value, it is good practice to include the default:

Attributes are state on objects. (See Getters/Setters above for more discussion about when to use them.) They can be defined internally in a number of different ways. Each approach is enumerated below with an explanation of where the comment goes.

Regardless of how the attribute is implemented, it should have a short description of what state it represents including the type, possible values and/or units. It should be marked as (read-only) or (write-only) at the end of the first line for attributes that are not both readable and writable.

Comment comes from after the assignment:

The pin drive mode. One of:

digitalio.DriveMode.PUSH_PULL

digitalio.DriveMode.OPEN_DRAIN

Comment comes from the getter:

The current date and time as a time.struct_time.

The current temperature in degrees Celsius. (read-only)

The device may require calibration to get accurate readings.

Comment is after the definition:

True if the device has lost power since the time was set.

First line after the method definition:

Turns the bot degrees right.

degrees (float) – Degrees to turn right

When you need to make references to documentation in other libraries you should refer the class using single backticks :class:`~adafruit_motor.servo.Servo`. You must also add the reference in the conf.py file in the intersphinx_mapping section by adding a new entry:

Register is a foundational library that manages packing and unpacking data from I2C device registers. There is also Register SPI for SPI devices. When possible, use one of these libraries for unpacking and packing registers. This ensures the packing code is shared amongst all registers (even across drivers). Furthermore, it simplifies device definitions by making them declarative (only data.)

Values with non-consecutive bits in a register or that represent FIFO endpoints may not map well to existing register classes. In unique cases like these, it is ok to read and write the register directly.

Do not add all registers from a datasheet upfront. Instead, only add the ones necessary for the functionality the driver exposes. Adding them all will lead to unnecessary file size and API clutter. See this video about outside-in design from @tannewt.

BusDevice is an awesome foundational library that manages talking on a shared I2C or SPI device for you. The devices manage locking which ensures that a transfer is done as a single unit despite CircuitPython internals and, in the future, other Python threads. For I2C, the device also manages the device address. The SPI device, manages baudrate settings, chip select line and extra post-transaction clock cycles.

When documenting classes, you should use the following template to illustrate basic usage. It is similar with the simpletest example, however this will display the information in the Read The Docs documentation. The advantage of using this template is it makes the documentation consistent across the libraries.

This is an example for a AHT20 temperature sensor. Include the following after the class parameter:

When writing a driver, take in objects that provide the functionality you need rather than taking their arguments and constructing them yourself or subclassing a parent class with functionality. This technique is known as composition and leads to code that is more flexible and testable than traditional inheritance.

Wikipedia has more information on “dependency inversion”.

For example, if you are writing a driver for an I2C device, then take in an I2C object instead of the pins themselves. This allows the calling code to provide any object with the appropriate methods such as an I2C expansion board.

Another example is to expect a DigitalInOut for a pin to toggle instead of a Pin from board. Taking in the Pin object alone would limit the driver to pins on the actual microcontroller instead of pins provided by another driver such as an IO expander.

CircuitPython boards tend to have a small amount of internal flash and a small amount of ram but large amounts of external flash for the file system. So, create many small libraries that can be loaded as needed instead of one large file that does everything.

Speed isn’t as important as API clarity and code size. So, prefer simple APIs like properties for state even if it sacrifices a bit of speed.

Although Python doesn’t require managing memory, it’s still a good practice for library writers to think about memory allocations. Avoid them in drivers if you can because you never know how much something will be called. Fewer allocations means less time spent cleaning up. So, where you can, prefer bytearray buffers that are created in __init__ and used throughout the object with methods that read or write into the buffer instead of creating new objects. Unified hardware API classes such as busio.SPI are design to read and write to subsections of buffers.

It’s ok to allocate an object to return to the user. Just beware of causing more than one allocation per call due to internal logic.

However, this is a memory tradeoff so do not do it for large or rarely used buffers.

Use struct.pack_into instead of struct.pack.

The MicroPython const() feature, as discussed in this forum post, and in this issue thread, provides some optimizations that can be useful on smaller, memory constrained devices. However, when using const(), keep in mind these general guide lines:

Always use via an import, ex: from micropython import const

Limit use to global (module level) variables only.

Only used when the user will not need access to variable and prefix name with a leading underscore, ex: _SOME_CONST.

When adding examples, cookiecutter will add a <name>_simpletest.py file in the examples directory for you. Be sure to include code with the library minimal functionalities to work on a device. You could other examples if needed featuring different functionalities of the library. If you add additional examples, be sure to include them in the examples.rst. Naming of the examples files should use the name of the library followed by a description, using underscore to separate them.

The Adafruit Unified Sensor Driver Arduino library has a great list of measurements and their units. Use the same ones including the property name itself so that drivers can be used interchangeably when they have the same properties.

(float, float, float)

x, y, z meter per second per second

(float, float, float)

x, y, z micro-Tesla (uT)

(float, float, float)

(float, float, float)

x, y, z radians per second

equivalent/estimated CO2 in ppm (estimated from some other measurement)

Total Volatile Organic Compounds in ppb

non-unit-specific proximity values (monotonic but not actual distance)

non-unit-specific light levels (should be monotonic but is not lux)

RGB, eight bits per channel (0xff0000 is red)

Sample alarm time and string to characterize frequency such as “hourly”

16-bit PWM duty cycle (regardless of output resolution)

16-bit Analog value, unit-less

non-unit-specific sound level (monotonic but not actual decibels)

When adding variables for constant values for a driver. Do not include the device’s name in the variable name. For example, in adafruit_fancy123.py, variables should not start with FANCY123_. Adding this prefix increases RAM usage and .mpy file size because variable names are preserved. User code should refer to these constants as adafruit_fancy123.HELLO_WORLD for clarity. adafruit_fancy123.FANCY123_HELLO_WORLD would be overly verbose.

The Python API for a new module should be defined and documented in shared-bindings and define an underlying C API. If the implementation is port-agnostic or relies on underlying APIs of another module, the code should live in shared-module. If it is port specific then it should live in common-hal within the port’s folder. In either case, the file and folder structure should mimic the structure in shared-bindings.

To test your native modules or core enhancements, follow these Adafruit Learning Guides for building local firmware to flash onto your device(s):

Keeping compatibility with MicroPython isn’t a high priority. It should be done when it’s not in conflict with any of the above goals.

**Examples:**

Example 1 (python):
```python
import digitalio
import board
import time

led = digitalio.DigitalInOut(board.LED)
led.direction = digitalio.Direction.OUTPUT

for i in range(10):
    led.value = True
    time.sleep(0.5)

    led.value = False
    time.sleep(0.5)
led.deinit()
```

Example 2 (python):
```python
import digitalio
import board
import time

with digitalio.DigitalInOut(board.LED) as led:
    led.direction = digitalio.Direction.OUTPUT

    for i in range(10):
        led.value = True
        time.sleep(0.5)

        led.value = False
        time.sleep(0.5)
```

Example 3 (unknown):
```unknown
if not 0 <= pin <= 7:
    raise ValueError("Pin number must be 0-7.")
```

Example 4 (unknown):
```unknown
assert 0 <= pin <= 7, "Pin number must be 0-7."
```

---

## Glossary

**URL:** https://docs.circuitpython.org/en/latest/docs/reference/glossary.html

**Contents:**
- Glossary

A system without a (full-fledged) operating system, for example an MCU-based system. When running on a baremetal system, MicroPython effectively functions like a small operating system, running user programs and providing a command interpreter (REPL).

Any Python object that can be automatically converted into bytes, such as bytes, bytearray, memoryview and str objects, which all implement the “buffer protocol”.

Typically this refers to a printed circuit board (PCB) containing a microcontroller and supporting components. MicroPython firmware is typically provided per-board, as the firmware contains both MCU-specific functionality but also board-level functionality such as drivers or pin names.

A compact representation of a Python program that generated by compiling the Python source code. This is what the VM actually executes. Bytecode is typically generated automatically at runtime and is invisible to the user. Note that while CPython and MicroPython both use bytecode, the format is different. You can also pre-compile source code offline using the cross-compiler.

This is a MicroPython-specific construct where, for efficiency reasons, some built-in functions or methods may reuse the same underlying tuple object to return data. This avoids having to allocate a new tuple for every call, and reduces heap fragmentation. Programs should not hold references to callee-owned tuples and instead only extract data from them (or make a copy).

A variant of MicroPython developed by Adafruit Industries.

CPython is the reference implementation of the Python programming language, and the most well-known one. It is, however, one of many implementations (including Jython, IronPython, PyPy, and MicroPython). While MicroPython’s implementation differs substantially from CPython, it aims to maintain as much compatibility as possible.

Also known as mpy-cross. This tool runs on your PC and converts a .py file containing MicroPython code into a .mpy file containing MicroPython bytecode. This means it loads faster (the board doesn’t have to compile the code), and uses less space on flash (the bytecode is more space efficient).

A MicroPython library that implements support for a particular component, such as a sensor or display.

Acronym for Foreign Function Interface. A mechanism used by the MicroPython Unix port to access operating system functionality. This is not available on baremetal ports.

Most MicroPython ports and boards provide a filesystem stored in flash that is available to user code via the standard Python file APIs such as open(). Some boards also make this internal filesystem accessible to the host via USB mass-storage.

A Python module that has been cross compiled and bundled into the firmware image. This reduces RAM requirements as the code is executed directly from flash.

A background process that runs in Python (and MicroPython) to reclaim unused memory in the heap.

General-purpose input/output. The simplest means to control electrical signals (commonly referred to as “pins”) on a microcontroller. GPIO typically allows pins to be either input or output, and to set or get their digital value (logical “0” or “1”). MicroPython abstracts GPIO access using the machine.Pin and machine.Signal classes.

A group of GPIO pins, usually based on hardware properties of these pins (e.g. controllable by the same register).

A region of RAM where MicroPython stores dynamic data. It is managed automatically by the Garbage Collector. Different MCUs and boards have vastly different amounts of RAM available for the heap, so this will affect how complex your program can be.

An optimisation used by MicroPython to improve the efficiency of working with strings. An interned string is referenced by its (unique) identity rather than its address and can therefore be quickly compared just by its identifier. It also means that identical strings can be de-duplicated in memory. String interning is almost always invisible to the user.

Microcontroller. Microcontrollers usually have much less resources than a desktop, laptop, or phone, but are smaller, cheaper and require much less power. MicroPython is designed to be small and optimized enough to run on an average modern microcontroller.

MicroPython is (usually) distributed as a single executable/binary file with just few builtin modules. There is no extensive standard library comparable with CPython’s. Instead, there is a related, but separate project micropython-lib which provides implementations for many modules from CPython’s standard library.

Some of the modules are implemented in pure Python, and are able to be used on all ports. However, the majority of these modules use FFI to access operating system functionality, and as such can only be used on the MicroPython Unix port (with limited support for Windows).

Unlike the CPython stdlib, micropython-lib modules are intended to be installed individually - either using manual copying or using mip.

MicroPython supports different boards, RTOSes, and OSes, and can be relatively easily adapted to new systems. MicroPython with support for a particular system is called a “port” to that system. Different ports may have widely different functionality. This documentation is intended to be a reference of the generic APIs available across different ports (“MicroPython core”). Note that some ports may still omit some APIs described here (e.g. due to resource constraints). Any such differences, and port-specific extensions beyond the MicroPython core functionality, would be described in the separate port-specific documentation.

The unix port is one of the major MicroPython ports. It is intended to run on POSIX-compatible operating systems, like Linux, MacOS, FreeBSD, Solaris, etc. It also serves as the basis of Windows port. The Unix port is very useful for quick development and testing of the MicroPython language and machine-independent features. It can also function in a similar way to CPython’s python executable.

A package installer for MicroPython (mip - “mip installs packages”). It installs MicroPython packages either from micropython-lib, GitHub, or arbitrary URLs. mip can be used on-device on network-capable boards, and internally by tools such as mpremote.

A tool for interacting with a MicroPython device.

The output of the cross-compiler. A compiled form of a .py file that contains MicroPython bytecode instead of Python source code.

Usually refers to “native code”, i.e. machine code for the target microcontroller (such as ARM Thumb, Xtensa, x86/x64). The @native decorator can be applied to a MicroPython function to generate native code instead of bytecode for that function, which will likely be faster but use more RAM.

Usually short for MicroPython port, but could also refer to GPIO port.

A file containing Python source code.

An acronym for “Read, Eval, Print, Loop”. This is the interactive Python prompt, useful for debugging or testing short snippets of code. Most MicroPython boards make a REPL available over a UART, and this is typically accessible on a host PC via USB.

Also known as a “file-like object”. A Python object which provides sequential read-write access to the underlying data. A stream object implements a corresponding interface, which consists of methods like read(), write(), readinto(), seek(), flush(), close(), etc. A stream is an important concept in MicroPython; many I/O objects implement the stream interface, and thus can be used consistently and interchangeably in different contexts. For more information on streams in MicroPython, see the io module.

Acronym for “Universal Asynchronous Receiver/Transmitter”. This is a peripheral that sends data over a pair of pins (TX & RX). Many boards include a way to make at least one of the UARTs available to a host PC as a serial port over USB.

A now-obsolete package manager for MicroPython, inspired by CPython’s pip, but much smaller and with reduced functionality. See its replacement, mip.

A way of connecting to the REPL (and transferring files) on a device over the internet from a browser. See https://micropython.org/webrepl

---

## Adafruit CircuitPython Libraries

**URL:** https://docs.circuitpython.org/en/latest/docs/libraries.html

**Contents:**
- Adafruit CircuitPython Libraries
- CircuitPython Library Bundles

Documentation for all Adafruit-sponsored CircuitPython libraries is at: <https://docs.circuitpython.org/projects/bundle/en/latest/drivers.html>.

Many Python libraries, including device drivers, have been written for use with CircuitPython. They are maintained in separate GitHub repos, one per library.

Libraries are packaged in bundles, which are ZIP files that are snapshots in time of a group of libraries.

Adafruit sponsors and maintains several hundred libraries, packaged in the Adafruit Library Bundle. Adafruit-sponsored libraries are also available on <https://pypi.org>.

Yet other libraries are maintained by members of the CircuitPython community, and are packaged in the CircuitPython Community Library Bundle.

The Adafruit bundles are available on GitHub: <https://github.com/adafruit/Adafruit_CircuitPython_Bundle/releases>. The Community bundles are available at: <https://github.com/adafruit/CircuitPython_Community_Bundle/releases>.

More detailed information about the bundles, and download links for the latest bundles are at <https://circuitpython.org/libraries>.

Documentation about bundle construction is at: <https://circuitpython.readthedocs.io/projects/bundle/en/latest/>.

Documentation for Community Libraries is not available on ReadTheDocs at this time. See the GitHub repository for each library for any included documentation.

---

## collections – collection and container types

**URL:** https://docs.circuitpython.org/en/latest/docs/library/collections.html

**Contents:**
- collections – collection and container types
- Classes

Limitations: Not implemented on the smallest CircuitPython boards for space reasons.

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: collections.

This module implements advanced collection and container types to hold/accumulate various objects.

Deques (pronounced “deck” and short for “double-ended queue”) are fixed length list-like containers that support O(1) appends and pops from either side of the deque. New deques are created using the following arguments:

iterable must be specified as an empty or non-empty iterable. If the iterable is empty, the new deque is created empty. If the iterable is not empty, the new deque is created with the items from the iterable.

maxlen must be specified and the deque will be bounded to this maximum length. Once the deque is full, any new items added will discard items from the opposite end.

flag is optional and can be set to 1 to check for overflow when adding items. If the deque is full and overflow checking is enabled, an IndexError will be raised when adding items.

Deque objects have the following methods:

Add x to the right side of the deque. Raises IndexError if overflow checking is enabled and there is no more room in the queue.

Add x to the left side of the deque. Raises IndexError if overflow checking is enabled and there is no more room in the queue.

Remove and return an item from the right side of the deque. Raises IndexError if no items are present.

Remove and return an item from the left side of the deque. Raises IndexError if no items are present.

Extend the right side of the deque by appending items from the iterable argument. Raises IndexError if overflow checking is enabled and there is no more room left for all of the items in iterable.

In addition to the above, deques support iteration, bool, len(d), reversed(d), membership testing with the in operator, and subscript references like d[0]. Note: Indexed access is O(1) at both ends but slows to O(n) in the middle of the deque, so for fast random access use a list instead.

This is factory function to create a new namedtuple type with a specific name and set of fields. A namedtuple is a subclass of tuple which allows to access its fields not just by numeric index, but also with an attribute access syntax using symbolic field names. Fields is a sequence of strings specifying field names. For compatibility with CPython it can also be a a string with space-separated field named (but this is less efficient). Example of use:

dict type subclass which remembers and preserves the order of keys added. When ordered dict is iterated over, keys/items are returned in the order they were added:

**Examples:**

Example 1 (python):
```python
from collections import namedtuple

MyTuple = namedtuple("MyTuple", ("id", "name"))
t1 = MyTuple(1, "foo")
t2 = MyTuple(2, "bar")
print(t1.name)
assert t2.name == t2[1]
```

Example 2 (python):
```python
from collections import OrderedDict

# To make benefit of ordered keys, OrderedDict should be initialized
# from sequence of (key, value) pairs.
d = OrderedDict([("z", 1), ("a", 2)])
# More items can be added as usual
d["w"] = 5
d["b"] = 3
for k, v in d.items():
    print(k, v)
```

Example 3 (unknown):
```unknown
z 1
a 2
w 5
b 3
```

---

## platform – access to underlying platform’s identifying data

**URL:** https://docs.circuitpython.org/en/latest/docs/library/platform.html

**Contents:**
- platform – access to underlying platform’s identifying data
- Functions

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: platform.

This module tries to retrieve as much platform-identifying data as possible. It makes this information available via function APIs.

Returns a string identifying the underlying platform. This string is composed of several substrings in the following order, delimited by dashes (-):

the name of the platform system (e.g. Unix, Windows or MicroPython)

the MicroPython version

the architecture of the platform

the version of the underlying platform

the concatenation of the name of the libc that MicroPython is linked to and its corresponding version.

For example, this could be "MicroPython-1.20.0-xtensa-IDFv4.2.4-with-newlib3.0.0".

Returns a string identifying the compiler used for compiling MicroPython.

Returns a tuple of strings (lib, version), where lib is the name of the libc that MicroPython is linked to, and version the corresponding version of this libc.

---

## sys – system specific functions

**URL:** https://docs.circuitpython.org/en/latest/docs/library/sys.html

**Contents:**
- sys – system specific functions
- Functions
- Constants

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: sys.

Terminate current program with a given exit code. Underlyingly, this function raise as SystemExit exception. If an argument is given, its value given as an argument to SystemExit.

This function is deprecated and will be removed starting in CircuitPython 10.x, traceback.print_exception() should be used instead.

Print exception with a traceback to a file-like object file (or sys.stdout by default).

Difference to CPython

This is simplified version of a function which appears in the traceback module in CPython. Unlike traceback.print_exception(), this function takes just exception value instead of exception type, exception value, and traceback object; file argument should be positional; further arguments are not supported.

A mutable list of arguments the current program was started with.

The byte order of the system ("little" or "big").

Object with information about the current Python implementation. For CircuitPython, it has following attributes:

name - string “circuitpython”

version - tuple (major, minor, micro), e.g. (1, 7, 0)

_machine - string describing the underlying machine

_mpy - supported mpy file-format version (optional attribute)

This object is the recommended way to distinguish CircuitPython from other Python implementations (note that it still may not exist in the very minimal ports).

Difference to CPython

CPython mandates more attributes for this object, but the actual useful bare minimum is implemented in CircuitPython.

Maximum value which a native integer type can hold on the current platform, or maximum value representable by the CircuitPython integer type, if it’s smaller than platform max value (that is the case for CircuitPython ports without long int support).

This attribute is useful for detecting “bitness” of a platform (32-bit vs 64-bit, etc.). It’s recommended to not compare this attribute to some value directly, but instead count number of bits in it:

Dictionary of loaded modules. On some ports, it may not include builtin modules.

A mutable list of directories to search for imported modules.

Difference to CPython

On MicroPython, an entry with the value ".frozen" will indicate that import should search frozen modules at that point in the search. If no frozen module is found then search will not look for a directory called .frozen, instead it will continue with the next entry in sys.path.

The platform that CircuitPython is running on. For OS/RTOS ports, this is usually an identifier of the OS, e.g. "linux". For baremetal ports it is an identifier of the chip on a board, e.g. "MicroChip SAMD51". It thus can be used to distinguish one board from another. If you need to check whether your program runs on CircuitPython (vs other Python implementation), use sys.implementation instead.

Standard error stream.

Standard input stream.

Standard output stream.

Python language version that this implementation conforms to, as a string.

Python language version that this implementation conforms to, as a tuple of ints.

Difference to CPython

Only the first three version numbers (major, minor, micro) are supported and they can be referenced only by index, not by name.

**Examples:**

Example 1 (yaml):
```yaml
bits = 0
v = sys.maxsize
while v:
    bits += 1
    v >>= 1
if bits > 32:
    # 64-bit (or more) platform
    ...
else:
    # 32-bit (or less) platform
    # Note that on 32-bit platform, value of bits may be less than 32
    # (e.g. 31) due to peculiarities described above, so use "> 16",
    # "> 32", "> 64" style of comparisons.
```

---

## select – wait for events on a set of streams

**URL:** https://docs.circuitpython.org/en/latest/docs/library/select.html

**Contents:**
- select – wait for events on a set of streams
- Functions
- class Poll
  - Methods

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: cpython:select.

This module provides functions to efficiently wait for events on multiple stream objects (select streams which are ready for operations).

Create an instance of the Poll class.

Wait for activity on a set of objects.

This function is provided by some MicroPython ports for compatibility and is not efficient. Usage of Poll is recommended instead.

Register stream obj for polling. eventmask is logical OR of:

select.POLLIN - data available for reading

select.POLLOUT - more data can be written

Note that flags like select.POLLHUP and select.POLLERR are not valid as input eventmask (these are unsolicited events which will be returned from poll() regardless of whether they are asked for). This semantics is per POSIX.

eventmask defaults to select.POLLIN | select.POLLOUT.

It is OK to call this function multiple times for the same obj. Successive calls will update obj’s eventmask to the value of eventmask (i.e. will behave as modify()).

Unregister obj from polling.

Modify the eventmask for obj. If obj is not registered, OSError is raised with error of ENOENT.

Wait for at least one of the registered objects to become ready or have an exceptional condition, with optional timeout in milliseconds (if timeout arg is not specified or -1, there is no timeout).

Returns list of (obj, event, …) tuples. There may be other elements in tuple, depending on a platform and version, so don’t assume that its size is 2. The event element specifies which events happened with a stream and is a combination of select.POLL* constants described above. Note that flags select.POLLHUP and select.POLLERR can be returned at any time (even if were not asked for), and must be acted on accordingly (the corresponding stream unregistered from poll and likely closed), because otherwise all further invocations of poll() may return immediately with these flags set for this stream again.

In case of timeout, an empty list is returned.

Difference to CPython

Tuples returned may contain more than 2 elements as described above.

Like poll.poll(), but instead returns an iterator which yields a “callee-owned tuple”. This function provides an efficient, allocation-free way to poll on streams.

If flags is 1, one-shot behaviour for events is employed: streams for which events happened will have their event masks automatically reset (equivalent to poll.modify(obj, 0)), so new events for such a stream won’t be processed until new mask is set with poll.modify(). This behaviour is useful for asynchronous I/O schedulers.

Difference to CPython

This function is a MicroPython extension.

---

## json – JSON encoding and decoding

**URL:** https://docs.circuitpython.org/en/latest/docs/library/json.html

**Contents:**
- json – JSON encoding and decoding
- Functions

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: json.

This modules allows to convert between Python objects and the JSON data format.

Serialise obj to a JSON string, writing it to the given stream.

If specified, separators should be an (item_separator, key_separator) tuple. The default is (', ', ': '). To get the most compact JSON representation, you should specify (',', ':') to eliminate whitespace.

Return obj represented as a JSON string.

The arguments have the same meaning as in dump.

Parse the given stream, interpreting it as a JSON string and deserialising the data to a Python object. The resulting object is returned.

Parsing continues until end-of-file is encountered. A ValueError is raised if the data in stream is not correctly formed.

Parse the JSON str and return an object. Raises ValueError if the string is not correctly formed.

---

## heapq – heap queue algorithm

**URL:** https://docs.circuitpython.org/en/latest/docs/library/heapq.html

**Contents:**
- heapq – heap queue algorithm
- Functions

Though this MicroPython-based library may be available for use in some builds of CircuitPython, it is unsupported and its functionality may change in the future, perhaps significantly. As CircuitPython continues to develop, it may be changed to comply more closely with the corresponding standard Python library. You will likely need to change your code later if you rely on any non-standard functionality it currently provides.

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: heapq.

This module implements the min heap queue algorithm.

A heap queue is essentially a list that has its elements stored in such a way that the first item of the list is always the smallest.

Push the item onto the heap.

Pop the first item from the heap, and return it. Raise IndexError if heap is empty.

The returned item will be the smallest item in the heap.

Convert the list x into a heap. This is an in-place operation.

---

## re – simple regular expressions

**URL:** https://docs.circuitpython.org/en/latest/docs/library/re.html

**Contents:**
- re – simple regular expressions
- Functions
- Regex objects
- Match objects

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: re.

This module implements regular expression operations. Regular expression syntax supported is a subset of CPython re module (and actually is a subset of POSIX extended regular expressions).

Supported operators and special sequences are:

Match set of characters. Individual characters and ranges are supported, including negated sets (e.g. [^a-c]).

Match the start of the string.

Match the end of the string.

Match zero or one of the previous sub-pattern.

Match zero or more of the previous sub-pattern.

Match one or more of the previous sub-pattern.

Non-greedy version of ?, match zero or one, with the preference for zero.

Non-greedy version of *, match zero or more, with the preference for the shortest match.

Non-greedy version of +, match one or more, with the preference for the shortest match.

Match either the left-hand side or the right-hand side sub-patterns of this operator.

Grouping. Each group is capturing (a substring it captures can be accessed with match.group() method).

Matches digit. Equivalent to [0-9].

Matches non-digit. Equivalent to [^0-9].

Matches whitespace. Equivalent to [ \t-\r].

Matches non-whitespace. Equivalent to [^ \t-\r].

Matches “word characters” (ASCII only). Equivalent to [A-Za-z0-9_].

Matches non “word characters” (ASCII only). Equivalent to [^A-Za-z0-9_].

Escape character. Any other character following the backslash, except for those listed above, is taken literally. For example, \* is equivalent to literal * (not treated as the * operator). Note that \r, \n, etc. are not handled specially, and will be equivalent to literal letters r, n, etc. Due to this, it’s not recommended to use raw Python strings (r"") for regular expressions. For example, r"\r\n" when used as the regular expression is equivalent to "rn". To match CR character followed by LF, use "\r\n".

counted repetitions ({m,n})

named groups ((?P<name>...))

non-capturing groups ((?:...))

more advanced assertions (\b, \B)

special character escapes like \r, \n - use Python’s own escaping instead

Compile regular expression, return regex object.

Compile regex_str and match against string. Match always happens from starting position in a string.

Compile regex_str and search it in a string. Unlike match, this will search string for first position which matches regex (which still may be 0 if regex is anchored).

Compile regex_str and search for it in string, replacing all matches with replace, and returning the new string.

replace can be a string or a function. If it is a string then escape sequences of the form \<number> and \g<number> can be used to expand to the corresponding group (or an empty string for unmatched groups). If replace is a function then it must take a single argument (the match) and should return a replacement string.

If count is specified and non-zero then substitution will stop after this many substitutions are made. The flags argument is ignored.

Note: availability of this function depends on MicroPython port.

Flag value, display debug information about compiled expression. (Availability depends on MicroPython port.)

Compiled regular expression. Instances of this class are created using re.compile().

Similar to the module-level functions match(), search() and sub(). Using methods is (much) more efficient if the same regex is applied to multiple strings.

Split a string using regex. If max_split is given, it specifies maximum number of splits to perform. Returns list of strings (there may be up to max_split+1 elements if it’s specified).

Match objects as returned by match() and search() methods, and passed to the replacement function in sub().

Return matching (sub)string. index is 0 for entire match, 1 and above for each capturing group. Only numeric groups are supported.

Return a tuple containing all the substrings of the groups of the match.

Note: availability of this method depends on MicroPython port.

Return the index in the original string of the start or end of the substring group that was matched. index defaults to the entire group, otherwise it will select a group.

Note: availability of these methods depends on MicroPython port.

Returns the 2-tuple (match.start(index), match.end(index)).

Note: availability of this method depends on MicroPython port.

**Examples:**

Example 1 (python):
```python
import re

# As re doesn't support escapes itself, use of r"" strings is not
# recommended.
regex = re.compile("[\r\n]")

regex.split("line1\rline2\nline3\r\n")

# Result:
# ['line1', 'line2', 'line3', '', '']
```

---

## errno – system error codes

**URL:** https://docs.circuitpython.org/en/latest/docs/library/errno.html

**Contents:**
- errno – system error codes
- Constants

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: errno.

This module provides access to symbolic error codes for OSError exception. Some codes are not available on the smallest CircuitPython builds, such as SAMD21, for space reasons.

Error codes, based on ANSI C/POSIX standard. All error codes start with “E”. Errors are usually accessible as exc.errno where exc is an instance of OSError. Usage example:

Dictionary mapping numeric error codes to strings with symbolic error code (see above):

**Examples:**

Example 1 (yaml):
```yaml
try:
    os.mkdir("my_dir")
except OSError as exc:
    if exc.errno == errno.EEXIST:
        print("Directory already exists")
```

Example 2 (swift):
```swift
>>> print(errno.errorcode[errno.EEXIST])
EEXIST
```

---

## binascii – binary/ASCII conversions

**URL:** https://docs.circuitpython.org/en/latest/docs/library/binascii.html

**Contents:**
- binascii – binary/ASCII conversions
- Functions

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: binascii.

This module implements conversions between binary data and various encodings of it in ASCII form (in both directions).

Convert the bytes in the data object to a hexadecimal representation. Returns a bytes object.

If the additional argument sep is supplied it is used as a separator between hexadecimal values.

Convert hexadecimal data to binary representation. Returns bytes string. (i.e. inverse of hexlify)

Decode base64-encoded data, ignoring invalid characters in the input. Conforms to RFC 2045 s.6.8. Returns a bytes object.

Encode binary data in base64 format, as in RFC 3548. Returns the encoded data followed by a newline character if newline is true, as a bytes object.

Compute CRC-32, the 32-bit checksum of the bytes in data starting with an initial CRC of value. The default initial CRC is 0. The algorithm is consistent with the ZIP file checksum.

---

## io – input/output streams

**URL:** https://docs.circuitpython.org/en/latest/docs/library/io.html

**Contents:**
- io – input/output streams
- Conceptual hierarchy
- Functions
- Classes

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: io.

This module contains additional types of stream (file-like) objects and helper functions.

Difference to CPython

Conceptual hierarchy of stream base classes is simplified in MicroPython, as described in this section.

(Abstract) base stream classes, which serve as a foundation for behavior of all the concrete classes, adhere to few dichotomies (pair-wise classifications) in CPython. In MicroPython, they are somewhat simplified and made implicit to achieve higher efficiencies and save resources.

An important dichotomy in CPython is unbuffered vs buffered streams. In MicroPython, all streams are currently unbuffered. This is because all modern OSes, and even many RTOSes and filesystem drivers already perform buffering on their side. Adding another layer of buffering is counter- productive (an issue known as “bufferbloat”) and takes precious memory. Note that there still cases where buffering may be useful, so we may introduce optional buffering support at a later time.

But in CPython, another important dichotomy is tied with “bufferedness” - it’s whether a stream may incur short read/writes or not. A short read is when a user asks e.g. 10 bytes from a stream, but gets less, similarly for writes. In CPython, unbuffered streams are automatically short operation susceptible, while buffered are guarantee against them. The no short read/writes is an important trait, as it allows to develop more concise and efficient programs - something which is highly desirable for MicroPython. So, while MicroPython doesn’t support buffered streams, it still provides for no-short-operations streams. Whether there will be short operations or not depends on each particular class’ needs, but developers are strongly advised to favor no-short-operations behavior for the reasons stated above. For example, MicroPython sockets are guaranteed to avoid short read/writes. Actually, at this time, there is no example of a short-operations stream class in the core, and one would be a port-specific class, where such a need is governed by hardware peculiarities.

The no-short-operations behavior gets tricky in case of non-blocking streams, blocking vs non-blocking behavior being another CPython dichotomy, fully supported by MicroPython. Non-blocking streams never wait for data either to arrive or be written - they read/write whatever possible, or signal lack of data (or ability to write data). Clearly, this conflicts with “no-short-operations” policy, and indeed, a case of non-blocking buffered (and this no-short-ops) streams is convoluted in CPython - in some places, such combination is prohibited, in some it’s undefined or just not documented, in some cases it raises verbose exceptions. The matter is much simpler in MicroPython: non-blocking stream are important for efficient asynchronous operations, so this property prevails on the “no-short-ops” one. So, while blocking streams will avoid short reads/writes whenever possible (the only case to get a short read is if end of file is reached, or in case of error (but errors don’t return short data, but raise exceptions)), non-blocking streams may produce short data to avoid blocking the operation.

The final dichotomy is binary vs text streams. MicroPython of course supports these, but while in CPython text streams are inherently buffered, they aren’t in MicroPython. (Indeed, that’s one of the cases for which we may introduce buffering support.)

Note that for efficiency, MicroPython doesn’t provide abstract base classes corresponding to the hierarchy above, and it’s not possible to implement, or subclass, a stream class in pure Python.

Open a file. Builtin open() function is aliased to this function. All ports (which provide access to file system) are required to support mode parameter, but support for other arguments vary by port.

This is type of a file open in binary mode, e.g. using open(name, "rb"). You should not instantiate this class directly.

This is type of a file open in text mode, e.g. using open(name, "rt"). You should not instantiate this class directly.

In-memory file-like objects for input/output. StringIO is used for text-mode I/O (similar to a normal file opened with “t” modifier). BytesIO is used for binary-mode I/O (similar to a normal file opened with “b” modifier). Initial contents of file-like objects can be specified with string parameter (should be normal string for StringIO or bytes object for BytesIO). All the usual file methods like read(), write(), seek(), flush(), close() are available on these objects, and additionally, a following method:

Get the current contents of the underlying buffer which holds data.

Create an empty StringIO/BytesIO object, preallocated to hold up to alloc_size number of bytes. That means that writing that amount of bytes won’t lead to reallocation of the buffer, and thus won’t hit out-of-memory situation or lead to memory fragmentation. These constructors are a MicroPython extension and are recommended for usage only in special cases and in system-level libraries, not for end-user applications.

Difference to CPython

These constructors are a MicroPython extension.

---

## builtins – builtin functions and exceptions

**URL:** https://docs.circuitpython.org/en/latest/docs/library/builtins.html

**Contents:**
- builtins – builtin functions and exceptions
- Functions and types
- Exceptions
- Constants

All builtin functions and exceptions are described here. They are also available via the builtins module.

For more information about built-ins, see the following CPython documentation:

Builtin CPython Functions

Builtin CPython Exceptions

Builtin CPython Constants

Not all of these functions, types, exceptions, and constants are turned on in all CircuitPython ports, for space reasons.

See CPython documentation: bytearray.

See CPython documentation: bytes.

The argument name should be a string, and this function deletes the named attribute from the object given by obj.

frozenset() is not enabled on the smallest CircuitPython boards for space reasons.

reversed() is not enabled on the smallest CircuitPython boards for space reasons.

The slice builtin is the type that slice objects have.

ReloadException is used internally to deal with soft restarts.

Not a part of the CPython standard library

See CPython documentation: SystemExit.

See CPython documentation: TypeError.

---

## gc – control the garbage collector

**URL:** https://docs.circuitpython.org/en/latest/docs/library/gc.html

**Contents:**
- gc – control the garbage collector
- Functions

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: gc.

Enable automatic garbage collection.

Disable automatic garbage collection. Heap memory can still be allocated, and garbage collection can still be initiated manually using gc.collect().

Run a garbage collection.

Return the number of bytes of heap RAM that are allocated by Python code.

Difference to CPython

This function is a MicroPython extension.

Return the number of bytes of heap RAM that is available for Python code to allocate, or -1 if this amount is not known.

Difference to CPython

This function is a MicroPython extension.

Set or query the additional GC allocation threshold. Normally, a collection is triggered only when a new allocation cannot be satisfied, i.e. on an out-of-memory (OOM) condition. If this function is called, in addition to OOM, a collection will be triggered each time after amount bytes have been allocated (in total, since the previous time such an amount of bytes have been allocated). amount is usually specified as less than the full heap size, with the intention to trigger a collection earlier than when the heap becomes exhausted, and in the hope that an early collection will prevent excessive memory fragmentation. This is a heuristic measure, the effect of which will vary from application to application, as well as the optimal value of the amount parameter.

Calling the function without argument will return the current value of the threshold. A value of -1 means a disabled allocation threshold.

Difference to CPython

This function is a MicroPython extension. CPython has a similar function - set_threshold(), but due to different GC implementations, its signature and semantics are different.

---

## array – arrays of numeric data

**URL:** https://docs.circuitpython.org/en/latest/docs/library/array.html

**Contents:**
- array – arrays of numeric data
- Classes

This module implements a subset of the corresponding CPython module, as described below. For more information, refer to the original CPython documentation: array.

Supported format codes: b, B, h, H, i, I, l, L, q, Q, f, d (the latter 2 depending on the floating-point support).

Create array with elements of given type. Initial contents of the array are given by an iterable. If it is not provided, an empty array is created.

In addition to the methods below, array objects also implement the buffer protocol. This means the contents of the entire array can be accessed as raw bytes via a memoryview or other interfaces which use this protocol.

Append new element val to the end of array, growing it.

Append new elements as contained in iterable to the end of array, growing it.

Indexed read of the array, called as a[index] (where a is an array). Returns a value if index is an int and an array if index is a slice. Negative indices count from the end and IndexError is thrown if the index is out of range.

Note: __getitem__ cannot be called directly (a.__getitem__(index) fails) and is not present in __dict__, however a[index] does work.

Indexed write into the array, called as a[index] = value (where a is an array). value is a single value if index is an int and an array if index is a slice. Negative indices count from the end and IndexError is thrown if the index is out of range.

Note: __setitem__ cannot be called directly (a.__setitem__(index, value) fails) and is not present in __dict__, however a[index] = value does work.

Returns the number of items in the array, called as len(a) (where a is an array).

Note: __len__ cannot be called directly (a.__len__() fails) and the method is not present in __dict__, however len(a) does work.

Return a new array that is the concatenation of the array with other, called as a + other (where a and other are both arrays).

Note: __add__ cannot be called directly (a.__add__(other) fails) and is not present in __dict__, however a + other does work.

Concatenates the array with other in-place, called as a += other (where a and other are both arrays). Equivalent to extend(other).

Note: __iadd__ cannot be called directly (a.__iadd__(other) fails) and is not present in __dict__, however a += other does work.

Returns the string representation of the array, called as str(a) or repr(a)` (where a is an array). Returns the string "array(<type>, [<elements>])", where <type> is the type code letter for the array and <elements> is a comma separated list of the elements of the array.

Note: __repr__ cannot be called directly (a.__repr__() fails) and is not present in __dict__, however str(a) and repr(a) both work.

---
