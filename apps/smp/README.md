# SMP Pi

## Overview

This sample application calculates Pi independently in many threads, and
demonstrates the benefit of multiple execution units (CPU cores)
when compute-intensive tasks can be run in parallel, with
no cross-dependencies or shared resources.

By changing the value of CONFIG_MP_MAX_NUM_CPUS on SMP systems, you
can see that using more cores takes almost linearly less time
to complete the computational task.

You can also edit the sample source code to change the
number of digits calculated (``DIGITS_NUM``), and the
number of threads to use (``THREADS_NUM``).

## Building and Running

This project outputs Pi values calculated by each thread and in the end total time
required for all the calculation to be done.

By default, it is configured to execute from DDR and to use four HARTS (u54_1 -> u54_4), where u54_1 is the boot HART. To modify the boot HART and the amount of HART's used, edit the `CONFIG_RISCV_BOOT_HART` and `CONFIG_MP_MAX_NUM_CPUS` Kconfig symbols found in `smp/prj.conf`.

It can be built for Microchip's PolarFire SoC board `mpfs_icicle` as follows:

```bash
west build -p -b mpfs_icicle polarfire-soc-zephyr-applications/mpfs-applications/smp
```

A Devicetree Overlay file is provided to run the sample application from Scratchpad memory. To build the application for Scratchpad, do:

```bash
west build -p -b mpfs_icicle polarfire-soc-zephyr-applications/mpfs-applications/smp -- -DDTC_OVERLAY_FILE="boards/mpfs_icicle_scratchpad.overlay"
```

## Sample Output

````bash
Calculate first 240 digits of Pi independently by 16 threads.
Pi value calculated by thread #0: 3141592653589793238462643383279502884197...
Pi value calculated by thread #1: 3141592653589793238462643383279502884197...
...
Pi value calculated by thread #14: 314159265358979323846264338327950288419...
Pi value calculated by thread #15: 314159265358979323846264338327950288419...
All 16 threads executed by 4 cores in 28 msec
````
