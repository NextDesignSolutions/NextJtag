# NextJTAG

NextJTAG is a standalone command line utility used for accessing Xilinx FPGAs over USB.  It supports basic operations, such as checking the temperature and loading bitstreams.  Platform and FPGA support are fairly limited, but more are coming soon.  To gain access to all features, a [license](#Licensing) must be purchased.

## Supported Features

* Querying Device DNAs of attached FGPAs
* Loading bitstreams in parallel (not persistent across power cycling)
* Reading the min, max, and current temperature
* Reading/writing SYSMON registers

## Supported Xilinx FPGAs

* XCVU9P (VCU1525 or BCU1525)

## Supported Platforms

* Linux (x86-64)

## Licensing

Licensing is done per FPGA, and is tied to the FPGA's Device DNA (a unique identifier fused into the chip by the manufacturer).  The majority of features require a license to use.  The only feature that does not require a license is to list the Device DNAs of all FPGAs connected to the system.

Licenses are stored in a text file, with each line specifying the Device DNA and associated license separated by whitespace.  Blank lines and lines starting with `#` are ignored.  The `NEXT_JTAG_LICENSE` environment variable must contain the path to the license file when running the NextJTAG utility.

Licenses must be purchased from Next Design Solutions directly or through a third party vendor.  Licenses are valid indefinitely, but will only work with the same major version of NextJTAG.  For example, a 1.x license will work with versions 1.0 and 1.1, but will not not with version 2.0.  In general, bug fixes and basic new features will not result in a major version bump.


### Purchasing options

* Coming soon
