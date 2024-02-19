# Microchip PolarFire SoC Zephyr Support

This repository provides Microchip PolarFire SoC support for the Zephyr RTOS project with:

- Sample Zephyr applications for Microchip's PolarFire SoC
- Extended `West` Commands to generate and flash a payload to the eMMC
- Debug launch configurations to debug a Zephyr application in Microchip's `SoftConsole` IDE

## Prerequisites

This section describes the requirements needed before building and flashing
a Zephyr application.

### Supported Build Hosts

This document assumes you are running on a modern Linux system. The process documented here was tested using Ubuntu 20.04/18.04 LTS.

### Install Zephyr SDK and Build System on Host PC

To build Zephyr applications on a host PC, please follow the installation instructions for the Zephyr SDK and build system found in the [Zephyr OS Install Guide](https://docs.zephyrproject.org/latest/getting_started/index.html)

## Build Instructions

The following commands make a workspace and clone this repository into a directory called `polarfire-soc`:

```bash
mkdir zephyr_workspace && cd zephyr_workspace
git clone https://github.com/polarfire-soc/zephyr-applications.git polarfire-soc
```

The next step initializes the workspace:

```bash
cd polarfire-soc
west init -l
```

Now using Zephyr's build-system, `West`, pull in Zephyr's code base:

```bash
cd ../
west update
west zephyr-export
source zephyr/zephyr-env.sh
```

To build a Zephyr application, use the command:

```bash
west build -p -b mpfs_icicle <application>
```

Or to build a Zephyr application and generate an Eclipse CDT-4 based project
which can be imported into Microchip's `SoftConsole` IDE:

```bash
west build -p -b mpfs_icicle <application> -G "Eclipse CDT4 - Unix Makefiles"
```

Where `mpfs_icicle` is the supported board for PolarFire SoC Icicle Kit, `<application>` is the Zephyr OS application.

The table below lists the applications available for PolarFire Soc Icicle Kit that demonstrate various functionallity:

| `application` |
| --- |
| `apps/blinky` |
| `apps/button` |
| `apps/smp` |
| `apps/synchronization` |

## Using `West` Commands for PolarFire SoC

### Python Requirements

This repository comes with scripts that extend Zephyr's build system,
`West`. The scripts are written in `python` and can be found in the `scripts/` directory.
The scripts have python packages requirements which can be installed with `pip`:

```bash
pip install -r scripts/requirements.txt
```

Information on the `West` extensions for PolarFire SoC can be seen by:

```bash
west help
```

The output should contain:

```bash
extension commands from project manifest (path: polarfire-soc):
  generate-payload:     invoke the Hart Software Services (HSS) payload
                        generator to generate a payload suitable for
                        flashing to the boards eMMC
  flash-payload:        flash a payload onto the boards eMMC.
```

### Fetching Blobs With `West`

Zephyr's build system `West` has a mechanism for fetching binaries and executables from source control. These are known as `blobs`. This repository is capable of fetching the `Hart Software Services (HSS)`, Payload Generator executable. To do this:

```bash
west blobs fetch
```

This will retrieve the the Payload Generator Executable. Note, this is required to use the West extension `generate-payload`

## Generating a payload

The `generate-payload` West command utilizes the Hart Software Services Payload Generator to generate a binary that is suitable to be flashed to a devices eMMC. More information on the HSS Payload Generator can be found [here](https://github.com/polarfire-soc/hart-software-services/blob/master/tools/hss-payload-generator/README.md)

To generate a payload, a compiled Zephyr application (ex: zephyr.elf) and a configuration file written in yaml are required. Example configuration files are provided in `payload-configs/`.

The instructions below demonstrates how to use `generate-payload`:

```bash
# from the zephyr workspace:
#
# build the smp example
west build -p -b mpfs_icicle polarfire-soc/apps/smp

# generate a payload for an SMP, DDR, application
west generate-payload polarfire-soc/payload-configs/smp_ddr.yaml output.bin
```

The instructions above generate a payload called `output.bin`

## Flashing a Payload to the eMMC

The `flash-payload` West command interacts with the Hart Software Services on a board to flash a generated payload to the eMMC. It requires a generated payload and the serial device path for the HSS.

**Note:** Elevated privileges are required to write to a mounted block device on the host PC. This command prompt's the user to enter their password, which it uses to execute the write commmand.

The command is interactive: it prompt's the user to power on their board. On power up:

- It opens a serial connection to the HSS and interacts with the command line interface
- It envokes the HSS to mount the eMMC to the host PC as a mass storage device
- It searches the host PC to find the mass storage device and prompts the user to verify it is correct
- It prompts the user for their password and envokes the write command

The following instructions demonstrate executing the `flash-payload` West command:

```bash
# from the Zephyr workspace:
#
# 'output.bin' generated from generate-payload
#
# '/dev/ttyUSB2' the serial device path for the HSS in this example
west flash-payload output.bin /dev/ttyUSB2
```

After executing the above command, the following output can be observed:

```bash
$ west flash-payload output.bin /dev/ttyUSB2
power on your device:
....
hss: HSS: decompressing from eNVM to L2 Scratch ... Passed

hss: Press a key to enter CLI, ESC to skip
sent: c


hss: Type HELP for list of commands
sent: mmc


hss: Selecting SDCARD/MMC (fallback) as boot source
sent: usbdmsc


hss: Attempting to select eMMC
Found device:
ID_SERIAL=MSCC_PolarFireSoC_msd_05DA151553375A24CE845F22-0:0,
DEVNAME=/dev/sda

Choose this Device?

Please enter 'y' for yes or 'n' for no: y

Enter your password (required to write to block device):

sent CTR+C

sent: boot

how about that!
```

## Debugging a Zephyr Application using Microchip's SoftConsole IDE

[Follow the instructions here](softconsole-launch-configs/README.md) to start a debug session using Microchips SoftConsole IDE

## Additional Reading

[Zephyr User Manual](https://docs.zephyrproject.org/latest/)

[Zephyr Project - Github](https://github.com/zephyrproject-rtos/zephyr)

[Polarfire SoC Documentation](https://github.com/polarfire-soc/polarfire-soc-documentation)
