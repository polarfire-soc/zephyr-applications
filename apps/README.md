# PolarFire SoC Zephyr Applications

## Blinky

The `blinky` application is a very basic example designed to familarize the user
with the concepts of Zephyr such as using a peripherals API, and compiling applications using `Devicetree overlays`.

The application blink's an LED forever using the GPIO API.

The application also comes with an extra Devicetree overlay file, `mpfs_icicle_scratchpad.overlay`, located in the `boards/` directory which will compile the application to execute from Scratchpad. To compile an application using the above overlay:

```bash
west build -p -b mpfs_icicle polarfire-soc/apps/blinky -- -DDTC_OVERLAY_FILE=boards/mpfs_icicle_scratchpad.overlay
```

## Button

A simple button demo showcasing the use of GPIO input with interrupts.
The sample prints a message to the console each time a button is pressed.

## Thread Synchronization Support

A simple application that demonstrates basic sanity of the kernel.
Two threads (A and B) take turns printing a greeting message to the console,
and use sleep requests and semaphores to control the rate at which messages
are generated. This demonstrates that kernel scheduling, communication,
and timing are operating correctly.

To build this application and generate an eclipse project configuration file which can be used in Microchip's SoftConsole IDE:

```bash
west build -p -b mpfs_icicle polarfire-soc/apps/synchronization
```

## Symmetric Multi Processing Support

This application calculates Pi independently in many threads, and
demonstrates the benefit of multiple execution units (CPU cores)
when compute-intensive tasks can be run in parallel, with
no cross-dependencies or shared resources.

By changing the value of CONFIG_MP_MAX_NUM_CPUS on SMP systems, you
can see that using more cores takes almost linearly less time
to complete the computational task.

You can also edit the application source code to change the
number of digits calculated (``DIGITS_NUM``), and the
number of threads to use (``THREADS_NUM``).

The application is provided with Devicetree overlays demonstrating the
application running from:

- DDR with two HARTS
- Scratchpad with two HARTS

To build a SMP application for DDR with two HARTS

```bash
west build -p -b mpfs_icicle polarfire-soc/apps/smp
```

To build a SMP application for Scratchpad with two HARTS

```bash
west build -p -b mpfs_icicle polarfire-soc/apps/smp -- -DDTC_OVERLAY_FILE=boards/mpfs_icicle_scratchpad.overlay
```

## Zephyr OS Application Build Directory

The output of the build process can be found in:

```bash
build/zephyr/
```

Here you will find the application ELF binary, zephyr.elf, etc.

## Debugging a Zephyr OS Application using Microchip SoftConsole IDE

`Eclipse CDT - 4` style projects can be built for any of the examples above by appending the argument below:

```bash
 - G "Eclipse CDT4 - Unix Makefiles"
```

[Follow the instructions here](../softconsole-launch-configs/README.md) to start a debug session using Microchips SoftConsole IDE.
