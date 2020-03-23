# NextJTAG

NextJTAG is a standalone command line utility used for accessing Xilinx FPGAs over USB.  It supports basic operations, such as checking the temperature and loading bitstreams.  Platform and FPGA support are fairly limited, but more are coming soon.  To gain access to all features, a [license](#Licensing) must be purchased.  Check the [releases](../../releases) to download the latest binaries.

## Feature Highlights

* Query Device DNA of Xilinx FPGAs (free)
* Load bitstreams in parallel into configuration memory
* Clear the currently loaded bitstream
* Reload bitstreams from flash
* Reading the FPGA min/max/current temperature and voltage
* Reading/writing XADC/SYSMON registers
* Reading/writing to AXI over JTAG
* Changing voltage controller settings (not supported on all boards)
* Querying sensors from the BMC (not supported on all boards)
* REST API for remote control

## Supported Xilinx FPGAs and Boards

<table>
  <tr>
    <th>FPGA</th>
    <th>Board</th>
    <th>JTAG</th>
    <th>FPGA Program</th>
    <th>FPGA Sensors</th>
    <th>Voltage Control</th>
    <th>BMC Sensors</th>
  </tr>
  <tr>
    <td rowspan="1">XCU200</td>
    <td>Xilinx Alveo U200</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td rowspan="5">XCVU9P</td>
    <td>Xilinx VCU1525</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td>SQRL BCU1525</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>Yes<sup>1</sup></td>
    <td>Yes<sup>1</sup></td>
  </tr>
  <tr>
    <td>SQRL JCM9</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td>TUL BTU9P</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td>TUL BTU9P PRO</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td rowspan="1">XCVU13P</td>
    <td>Bittware CVP-13</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>Yes<sup>2</sup></td>
    <td>Yes<sup>2</sup></td>
  </tr>
  <tr>
    <td rowspan="2">XCVU33P</td>
    <td>SQRL FK33</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>Yes<sup>1</sup></td>
    <td>No</td>
  </tr>
  <tr>
    <td>SQRL JCM33</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td rowspan="1">XCVU35P</td>
    <td>SQRL JCM35</td>
    <td>USB</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td rowspan="1">Artix-7</td>
    <td>SQRL Acorn</td>
    <td>Custom</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
  <tr>
    <td>Kintex-7</td>
    <td>Trustfarm TM-FM2L</td>
    <td>Custom</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
    <td>No</td>
  </tr>
</table>

<p>
<sup>1</sup> Bitstream support required for BMC access<br>
<sup>2</sup> Bittware cards require Bittworks II Toolkit Lite for BMC access (see <a href="#bittware-board-setup">Bittware Board Setup</a>)
</p>

## Limitations

* General
  * Requires USB to be connected to an onboard FTDI chip or an external FTDI/JTAG cable
  * Regular temperature and voltage readings use XADC/SYSMON, which may not give the same value as sensors elsewhere on the board.  In addition, some bitstreams seem to break the min/max functionality on the temperature and voltage sensors.
  * There is not a way to run different operations on different devices in the same CLI command.  NextJTAG will need to be called multiple times.
  * Many advanced operations (such as changing the voltage) requires BMC access, which is different for every board.  We currently only support BMC operations on the BCU1525 and any Bittware board (see [Bittware Board Setup](#bittware-board-setup)), but may add more in the future depending on demand and vendor cooperation.
* BCU1525
  * BMC access requires loading a special bitstream, which takes a few seconds and causes the previous bitstream to be overwritten.  This means that setting the voltage or reading BMC sensors can't be done while mining.
  * NextJTAG will refuse to set voltage on an out of date BMC (this is shown as error 0x6d)

## Supported Platforms

* Linux (x86-64, aarch64)
* Windows (x64)

## Licensing

Licensing is done per FPGA and is tied to the FPGA's Device DNA (a unique identifier fused into the chip by the manufacturer).  The majority of features require a license to use.  The only feature that does not require a license is to list the Device DNAs of all FPGAs connected to the system.

Licenses are stored in a text file(s), with each line specifying the Device DNA and associated license separated by whitespace.  Blank lines and lines starting with `#` are ignored. There are two ways to use license files:

1. If the `NEXTJTAG_LICENSE` environment variable is set, NextJTAG will use the value as the (relative or absolute) path to the license file.  Multiple files can be set using a colon (Linux) or semicolon (Windows) separated list.
2. If the environment variable is not set, then NextJTAG will automatically use any txt file that starts with `nextjtag_license` that is in the current directory or in the directory where the NextJTAG tool is located in.  For example, `nextjtag_license_foo.txt` would be matched.

Note: When using NextJTAG server, licenses can be uploaded/downlaoded from the server through the REST API. The NextJTAG CLI toolwill use this automatically to get missing licenses.

Licenses must be purchased from NextDesign Solutions directly or through an authorized third party vendor ([see purchasing options](#purchasing-options)).  Licenses are valid indefinitely, but will only work with the same major version of NextJTAG.  For example, a 1.x license will work with versions 1.0 and 1.1, but will not with version 2.0.  In general, bug fixes and basic new features will not result in a major version bump.


### Purchasing options

* [fpga.guide](https://shop.fpga.guide/collections/all/products/nextjtag-license) (Paypal, Credit Card, or various crypto currencies)
* Contact `turekaj#2845` on discord (Ethereum only)

## Permissions (Linux only)

NextJTAG needs access to the FTDI chip on the FPGA over USB in order to work, which requires special permissions on Linux platforms.  There are two options:
1. Run as root user (not ideal, but convenient)
2. Install udev rules to allow non-root users access to read and write to USB FTDI devices (best practice)

To do option 2, simply copy the `.rules` files from the release (located in `<release>/lib/udev/rules.d`) into the `/etc/udev/rules.d` directory on your system.  However, the rule will only apply to new USB connections.  If you already have a FPGA connected over USB, you can manually trigger the rule by running `udevadm trigger --action=add` as root.

## Driver Install (Windows only)

NextJTAG supports two drivers: a proprietary FTDI driver from the manufacturer and an open source driver.  Only one driver needs to be used.

### Proprietary FTDI driver (default for Windows)

Most other tools and miners, including Vivado, use this driver, so using this with NextJTAG ensures maximum interoperability.  If the driver isn't already installed, it can be found [here](https://www.ftdichip.com/Drivers/D2XX.htm) (there is a Windows installer if you search for "setup executable" on that page).

Once the driver is installed, you should be able to run NextJTAG without any arguments, and see a list of your FPGAs.

### Open Source FTDI driver (advanced users only)

The open source driver is around for legacy reasons, and not recommended for most users.  The open source driver will need to be configured for the FTDI interfaces that NextJTAG uses (Interface 0 for JTAG, Interface 2 for BMC control).

1. (optional) Installed the Install the [proprietary driver](#proprietary-ftdi-driver-default-for-windows).  This will create a clean slate, and will use the proprietary driver as default the for all interfaces for the FTDI chip.  If you are already using Vivado, this is probably not necessary
2. Download the [zadig](https://zadig.akeo.ie/) tool and run it as administrator.  This is a convenient tool to for installing drivers compatible with `libusb`
3. In Zadig, select `Options > Show all devices`, and then use the dropdown to select Interface 0 for one of your FPGA cards.  On the line below the dropdown, select `libusbK` and then hit the `Replace Driver` button.  Repeat for Interface 0 for all of your FPGA cards.  Do not replace the driver for the other interfaces, since some miners require the proprietary driver for those to work.
4. (optional) If you require BMC control on the BCU1525, repeat step 3 for Interface 2 for all FPGA cards.  This may prevent USB based miners from working, so you might need to switch it back after the BMC is configured.

![alt text](zadig.png "Example to replace driver for VCU1525")

The changes made by Zadig will be persistent, even after reboots.  To remove the open source driver, just install the proprietary driver again from step 1.  **You will need to do this in order to use Vivado again after running these steps**

In order to tell NextJTAG to use the open source driver, the `--prefer_libftdi` must be passed on the command line.

## Bittware Board Setup

To unlock full functionality for Bittware FPGA boards (such as voltage control and extended sensors), the Bittworks II Toolkit Lite (version 2018.6 or newer) must be installed and in the default path. The toolkit software can be obtained from your board vendor. Next, the boards must be configured using the `bwconfig` or `bwconfig-gui` utility (part of the toolkit), by scanning for USB devices and mapping in the boards. Finally, a map file must be generated, which associates the Bittware serial numbers with your FPGA's device DNA. The map file is provided to NextJTAG using the `--bw-map=` argument.  Here is an example map file:

```
# Nextjtag DNA to Bittworks Serial Map
#
400200000129076225200585        205965
```

A helper utility called `nextjtag_bwutil` is provided with the NextJTAG release to automate the map file generation. Simply execute `nextjtag_bwutil map` to start the mapping proces. Please note that the utility will power cycle all configured Bittware boards at least once, so it is recommended to ensure all FPGAs are idle before starting. Also ensure that the `bwmonitor-gui` is not running and connected to a board, since that will prevent that board from being scanned.

## CLI Usage

The CLI `nextjtag` tool has two modes. The default mode is direct mode, in which it will communicate directly with FPGAs locally connected the system. The second mode is client mode, in which it will connect to a NextJtag server instance and act as a remote control. To enable client mode, use the `--remote=<server>` flag.

To list all FGPAs detected on the system, run `nextjtag` without any arguments.  This does not require a license, so it is a good way to test the permissions and drivers are setup correctly on your system before buying a license.

All other usages of `nextjtag` require a license.  It is required that you specify at least one argument that is a "Device Selector" (to specify which devices to use) and at least one argument that is a "Command" (to specify what you want to do).  If multiple commands are given, the commands will be executed in the order given.  If multiple device selectors are given, then all commands will be executed on the selected devices.

Here is the list of all the options (you can also run `nextjtag -h` to see the same output):

```
Usage: nextjtag [OPTIONS]

Options:
  -h,--help                   Print this help message and exit
  --version                   Display the version of this tool and exit
  -m,--multithread            Enables multi threaded mode, with one thread per FGPA
  --max-threads COUNT         Limits how many threads will be run concurrently in multithreaded mode


Device Selectors:
  -a,--all                    Selects all detected devices
  -f,--fpga FPGA              Select all devices using the given FPGA
  -d,--device INDEX[.FPGA]    Select device to use by numerical index. An optional numerical suffix
                              can be used to select the FPGA on boards multiple FPGAs.
  -D,--description DESC       Select device to use by FTDI description
  -j,--serial SERIAL[.FPGA]   Select device to use by FTDI serial number. An optional numerical
                              suffix can be used to select the FPGA on boards multiple FPGAs.
  -k,--dna DNA                Select device to use by FPGA DNA


Commands:
  -t,--temperature            Performs a read of temperature sysmon/xadc registers
  -v,--voltage,--vccint       Performs a read of vccint sysmon/xadc registers
  --vccaux                    Performs a read of vccaux sysmon/xadc registers
  --vccbram                   Performs a read of vccbram sysmon/xadc registers
  -s,--sysmon OP              Performs a read or write (if value is given) operation to a
                              sysmon/xadc register.
                              OP: <reg>[:<value>]
  -x,--axi32 OP               [experimental] Performs a 32-bit AXI read or write (if value_list is
                              given) operation over JTAG. Requires bitstream support.
                              OP: <index>:<address>[<mode>]:[<cache>]:<count>[:<value_list>]
                              <index> - 0 based index for which AXI bus to use
                              <address> - axi bus address
                              <mode> - burst type, defaults to FIXED, specify a plus (+) for INCR
                              <cache> - bufferable(b), modifiable(m), read-alloc(r), write-alloc(w)
                              <count> - num of words, >256 will be split into multiple txns
                              <value_list> - comma sep list of words to write, omit for read
  -e,--extended LIST          Reads a comma separated list of extended sensors from the BMC. List of
                              supported sensors is board specific; use "all" to read all sensors. On
                              some boards, such as the BCU1525, this operation will clear the
                              currently loaded bitstream.
  -c,--clear                  Performs a clear operation, which clears the existing bitstream from
                              CFG mem (not persistent)
  -r,--reload                 Performs a reload operation, which reloads the bitstream from flash
  -b,--bitstream PATH:FILE    Performs a program operation, which loads a complete bitstream (.bit
                              or .bin file) info CFG mem(not peristent). If a partial bitstream
                              (.bit file only) is provided, a partial program operation (--partial)
                              will be performed automatically.
  --partial PATH:FILE         Performs a partial program operation, which loads a partial bitstream
                              (.bit or .bin file) into CFG mem (not persistent) without clearing the
                              previous bitstream. It is recommended to prefer the -b/--bitstream
                              option for loading partial .bit bitstreams, and only use this for
                              loading partial .bin bitstreams.
  -w,--wait SECONDS           Pauses for the given seconds after the previous operation


Client Options:
  --remote URL                Enables client mode. This will cause all commands to be executed
                              remotely using the given URL to a nextjtag_server instance. Protocol
                              defaults to HTTP unless specified. Port defaults to 19080 unless a
                              protocol or port is specified. To connect to a server running on the
                              same machine, the value "localhost" can be used.
  --rescan                    Issues a rescan on the server before retrieving devices


Expert Options:
  --set-voltage VOLTAGE       Changes the FPGA core voltage through the BMC (not persistent).
                              WARNING: INCORRECT VALUES CAN CAUSE PERMANENT DAMAGE TO YOUR FPGA!
                              This option will also clear the current bitstream, so make sure to
                              reprogram it afterwards. Voltage is programmatically limited to 0.64V
                              to 0.95V, but each FPGA is unique and may not function across the
                              entire range. Only supported board is BCU1525; this option is ignored
                              for all other boards.
  --disable-voltage-limit     Disables artificial voltage limits of the --set-voltages option. Not
                              recommended, only for the brave or the stupid.
  --disable-known-bmc-check   Disables version checks that ensure that known good BMC versions are
                              used. Some BMC features may not work correctly on boards with
                              unsupported BMC versions. Requires standalone mode.
  --disable-bs-check          Disables the check that ensures .bit bitstreams can only be programmed
                              on the correct FPGA.
  --enable-allmine-vcu-bmc    Enables BMC access for VCU1525 boards that have the allmine BMC
                              flashed. Requires standalone mode.
  --set-jtag-freq FREQ        Override the default JTAG frequency with the specified frequency. Most
                              cards default to 10000000, 15000000 or 30000000 (max), depending on
                              FTDI chip. Requires standalone mode.
  --ignore-sel-errors         Downgrades errors from unmatched selectors to warnings.
  --prefer-libftdi            Uses open source FTDI driver for JTAG communication (default for
                              Linux). Requires standalone mode.
  --prefer-ftd2xx             Uses proprietary FTDI driver for JTAG communication (default for
                              Windows). Requires standalone mode.
  --force-ftd2xx-reload       Forcibly unloads and reloads the proprietary FTDI driver in Windows
                              and exits. This an experimental method to recover USB devices that are
                              stuck and no longer visible to the driver. For best results, close any
                              other programs that use USB to communicate with the FPGA before
                              running this.  Must be run as Administrator. Requires standalone mode.
  --bw-map FILE               Load a Bittware map file, which contains maps the device DNA to
                              Bittware serial numbers. This will enable the Bittware libraries,
                              which unlocks advanced features for Bittware boards, such as voltage
                              control. The Bittworks II Toolkit Lite (2018.6 or later) must be
                              installed and in the default path. Additionally, the Bittware boards
                              must have already been configured using the bwconfig utility. The map
                              file format consists of lines with the dna followed by the serial,
                              separated by one or more spaces. Lines starting with # are ignored.
                              Requires standalone mode.
```

## Server Usage

The `nextjtag_server` a long running version of nextjtag, which accepts commands through a REST API. It is able to accept commands from multiple clients concurrently, and run commands targeting different FPGA boards in parallel. When conflicting commands are received that target the same board at the same time, the server will execute the first request and reject the others until that board is no longer busy. Similar to the CLI tool, a license is required for full functionality.

When the server first starts up, it will scan the system for FPGA and build a list, but will not open them by default. Clients are responsible for opening and initializing devices. When new devices are connected or disconnected from the system while the server is running, they will not be detected until the server runs a "rescan" operation. The rescan will add new devices to the internal list and prune devices that are no longer detected. The rescan operation can be triggered through the REST API or using the `--rescan`` flag in `nextjtag` client mode.

For performance reasons, the server will cache any bitstreams it receives for programming in RAM. This can cause the server to use a large amount of memory if many bitstreams are uploaded over the lifetime of the server. It may be necessary to restart the server from time to time, in order to clear the cache.

API documentation is packaged with each release.

Here is the list of all the options (you can also run `nextjtag_server -h` to see the same output):

```
Usage: nextjtag_server [OPTIONS]

Options:
  -h,--help                   Print this help message and exit
  --version                   Display the version of this tool and exit
  -b,--bind ADDR              Selects which IP address to bind the server to. This determines where
                              it will accept incoming requests from. Use 0.0.0.0 to accept requests
                              from anywhere. Defaults to 127.0.0.1.
  -p,--port PORT              Selects port to listen to listen on. Defaults to 19080.
  -t,--threads COUNT          Sets how many worker threads the server users. This determines how
                              many simultaneous requests the server can handle. Defaults to 32.
  -r,--auto-rescan SECONDS    Rescans for new USB devices every N seconds. Setting to zero disables
                              this feature. Defaults to disabled.
  -a,--auto-init              Automatically attempts to open and initialize devices discovered
                              during rescan.
  -l,--license-upload         Enables uploading and deleting licenses through the REST API.


Expert Options:
  --disable-known-bmc-check   Disables version checks that ensure that known good BMC versions are
                              used. Some BMC features may not work correctly on boards with
                              unsupported BMC versions.
  --set-jtag-freq FREQ        Override the default JTAG frequency with the specified frequency. Most
                              cards default to 10000000, 15000000 or 30000000 (max), depending on
                              FTDI chip.
  --prefer-libftdi            Uses open source FTDI driver for JTAG communication (default for
                              Linux)
  --prefer-ftd2xx             Uses proprietary FTDI driver for JTAG communication (default for
                              Windows)
  --bw-map FILE               Load a Bittware map file, which contains maps the device DNA to
                              Bittware serial numbers. This will enable the Bittware libraries,
                              which unlocks advanced features for Bittware boards, such as voltage
                              control. The Bittworks II Toolkit Lite (2018.6 or later) must be
                              installed and in the default path. Additionally, the Bittware boards
                              must have already been configured using the bwconfig utility. The map
                              file format consists of lines with the dna followed by the serial,
                              separated by one or more spaces. Lines starting with # are ignored.
```

## Examples

Query Device DNAs from all attached devices (no license required):

```
$ nextjtag
List of available devices:
  Board 0: SQRL JCC4P (serial: 160400100098)
  ├── Device 0.0: XCVU33P (type: FPGA, DNA: 4000000001289ee104f0a445)
  ├── Device 0.1: XCVU33P (type: FPGA, DNA: 40000000012922a63c1102c5)
  ├── Device 0.2: XCVU33P (type: FPGA, DNA: 4000000001295d813c504345)
  └── Device 0.3: XCVU33P (type: FPGA, DNA: 4000000001295d813c40c205)
  Board 1: CB-U1-AEBE (serial: 21440289L034)
  └── Device 1.0: XCVU9P (type: FPGA, DNA: 400200000128a7072d60e145)
  Board 2: CB-U1-AEBE (serial: 21430289400C)
  └── Device 2.0: XCVU9P (type: FPGA, DNA: 400200000117ab290d3102c5)
  Board 3: CB-U1-AEBE (serial: 21440289K059)
  └── Device 3.0: XCVU9P (type: FPGA, DNA: 40020000012922a7151121c5)
```

Read the temperature, wait 5 seconds, read the temperature again, and finally read voltage from device 0.1 (board 0, fpga 1):
```
$ nextjtag -d0.1 -t -w5 -t -v
[2019-12-11 00:24:04] Device 0.1: Temperature: 19.6883°C (min), 23.6673°C (current), 24.6621°C (max)
[2019-12-11 00:24:04] Device 0.1: Sleeping: 5 seconds
[2019-12-11 00:24:09] Device 0.1: Temperature: 19.6883°C (min), 23.6673°C (current), 24.6621°C (max)
[2019-12-11 00:24:09] Device 0.1: Vccint: 0.8467V (min), 0.8467V (current), 0.8496V (max)
```

Program bitstream for all XCVU9P FPGAs in parallel:
```
$ nextjtag -m -f xcvu9p -b vcu1525_keccak_21_600.bit
[2019-03-24 06:53:59] Device 1.0: Programming bitstream: STARTING...
[2019-03-24 06:53:59] Device 2.0: Programming bitstream: STARTING...
[2019-03-24 06:53:59] Device 3.0: Programming bitstream: STARTING...
[2019-03-24 06:54:39] Device 3.0: Programming bitstream: SUCCESS
[2019-03-24 06:54:39] Device 1.0: Programming bitstream: SUCCESS
[2019-03-24 06:54:39] Device 2.0: Programming bitstream: SUCCESS
```

Set voltage for device with Device DNA of 400200000117ab290d3102c5
```
$ nextjtag -k 400200000117ab290d3102c5 --set-voltage=0.72
[2019-11-22 02:04:47] Device 0.0: BMC Setup: STARTING...
[2019-11-22 02:04:53] Device 0.0: BMC Setup: SUCCESS
[2019-11-22 02:04:53] Device 0.0: Changing voltage to 0.72V: SUCCESS
```

Read FGPA core current and board temperature from CVP-13 (uses Bittworks library)
```
$ nextjtag --bw-map=bw.txt -f xcvu13p -e core_current,board_temperature
Using Bittware map file: bw.txt
Checking for Bittware Toolkit Installaiton
Found version: 2019.1.14.54399
[2019-11-22 02:18:39] Device 1.0: BMC Setup: STARTING...
[2019-11-22 02:18:39] Device 1.0: BMC Setup: SUCCESS
[2019-11-22 02:18:39] Device 1.0: Extended Sensors: core_current = 10.0000, board_temperature = 32.0000
```

Write 9 words to AXI address 0x1004 and read 13 words from AXI address 0x1000 (needs compatible bitstream loaded, requires v2.2+):
```
$ nextjtag -a -x 0:0x1004::9:0x5c,0x02,0x0c,0x00,0x00,0x0c,0x00,0x5c,0x03 -x 0:0x1000::13
[2019-06-11 02:21:20] Device 0.0: AXI[0] Write 9 word(s) to 0x1004: [OKAY]
[2019-06-11 02:21:20] Device 0.0: AXI[0] Read 13 word(s) from 0x1000: [OKAY]
    0000:  0000005c  00000002  0000008c  00000000
    0010:  00000004  00000003  00000000  00000001
    0020:  00000000  00000094  00000000  0000005c
    0030:  00000003
```

Start a server instance that accepts connections from any interface
```
$ nextjtag_server -b 0.0.0.0
Starting Server on 0.0.0.0:19080
```

Trigger a rescan on a remote server and program a bitstream:
```
$ nextjtag --remote=10.176.4.57 --rescan -d0 -b 0xToken/VCU1525_0xToken_13GHS.bit
Connected to NextJtag Server 2.4.0 (1da765824d319c2a173ec4c60bfd8944ae432365)
[2019-09-23 04:01:20] Preload: Uploading VCU1525_0xToken_13GHS.bit: STARTING...
[2019-09-23 04:01:43] Preload: Uploading VCU1525_0xToken_13GHS.bit: SUCCESS
[2019-09-23 04:01:43] Device 0.0: Programming VCU1525_0xToken_13GHS.bit: STARTING...
[2019-09-23 04:02:07] Device 0.0: Programming VCU1525_0xToken_13GHS.bit: SUCCESS
```

## Troubleshooting

How to fix some common problems.

#### No devices available or missing devices

```
$ nextjtag
List of available devices:
  (No devices found)
```

Check that your FPGAs are plugged in.  On Linux, you can do `lsusb` and search for FT4232H or "Future Technology Devices International" (FTDI).  On Windows, check device manager, and make sure you have gone through the [driver install](#driver-install-windows-only).

If it was previously working on Windows, but devices are not showing up now, then sometimes the FTDI driver gets stuck and prevents NextJTAG from opening deivces.  You can try running NextJTAG with the experimental `--force_ftd2xx_reload` option to attempt to get it unstuck.  If that doesn't work, you can try unplugging and plugging the device back in, or rebooting.

#### Open Error (no device name)

```
$ nextjtag
List of available devices:
  Board 0:  (serial: , Init Failed (LibFTDI Open Error - 0x10))
  Board 1:  (serial: , Init Failed (LibFTDI Open Error - 0x10))
  Board 2:  (serial: , Init Failed (LibFTDI Open Error - 0x10))
```

On Linux, this is probably a [permission](#permissions-linux-only) problem.  Try running as root or installing udev rules.

On Windows, this might be a [driver](#driver-install-windows-only) issue.  Sometimes a reboot will help.

#### Open Error (with device name)

```
$ nextjtag
List of available devices:
  Board 0: CB-U1-AEBE (serial: 21440289L034)
  └── Device 1.0: XCVU9P (type: FPGA, DNA: 400200000128a7072d60e145)
  Board 1: CB-U1-AEBE (serial: 21430289400C, Init Failed (LibFTDI Open Error - 0x10))
  Board 2: CB-U1-AEBE (serial: 21440289K059)
  └── Device 3.0: XCVU9P (type: FPGA, DNA: 40020000012922a7151121c5)
 ```

The device is probably already open by another process.  Check for other instances of NextJTAG that are running.  Also check if Vivado is running, and if so, make sure it isn't connected to the FPGA.  If that doesn't work, you can try rebooting.

#### No FPGAs found

```
$ nextjtag
List of available devices:
  Board 0: Digilent USB Device (serial: 210241433626, Init Failed (No FPGAs Found - 0x54))
```

This means that it was able to open the device, but it received something unexpected from the FTDI chip.  For example, this can happen if you use NextJTAG with a non-supported FPGA.  This can also indicate a hardware issue, when the FPGA is not responding to JTAG scan requests.  In some cases, power cycling may fix the issue.

#### Bogus min/max temperatures

```
$ nextjtag -a -t
[2019-03-24 07:14:37] Device 0.0: Temperature: 228.5866°C (min), 25.1594°C (current), -280.2300°C (max)
```

This is a known issue with certain hardware/bitstream configurations, and something we can hopefully provide a workaround for in the future.

#### Weird characters being printed out for temperature command

This is probably an issue with your shell not rendering the degree symbol, which requires UTF-8 encoding.

On Windows, run `chcp 65001` in the command prompt or `[Console]::OutputEncoding = [System.Text.Encoding]::UTF8` in powershell.

On Linux, it depends on your terminal program.

#### Error 0x6d when setting the voltage

```
$ nextjtag -a --set-voltage 0.8
[2019-11-22 02:21:53] Device 0.0: BMC Setup: STARTING...
[2019-11-22 02:21:58] Device 0.0: BMC Setup: ERROR (BCU BMC is too old - 0x6d)
```

This means the BMC version is out of date. The instructions to update the BMC firmware for BCU1525 cards is [here](https://miner.all-mine.co/instructions/guides/updating-bcu1525-bmc-firmware).
