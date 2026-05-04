# mchroot
MacaroniOS multi-architecture chroot tool using QEMU user emulation

# mchroot

## Introduction

mchroot is a fork of the original project https://pypi.org/project/fchroot/, adapted and maintained for the MacaroniOS ecosystem.

mchroot is a utility that allows you to leverage QEMU user-mode emulation together with Linux binfmt_misc to enter and operate inside non-native chroot environments. For example, on an x86_64 system, it is possible to chroot into an ARM32 or ARM64 environment and have it function transparently.

Unlike a traditional chroot, mchroot automatically prepares a hybrid execution environment where binaries from a different architecture are executed via QEMU, requiring minimal manual configuration. You can use mchroot just like the standard chroot command.

## Example
```
my64bitpc # mkdir /tmp/stage3 ; cd /tmp/stage3 ; tar xpvf stage3-arm64bit.tar.xz
my64bitpc # mchroot /tmp/stage3
>>> Entering arm-64bit (native) fchroot...
fchroot #
```
#

At this point, you are inside an ARM64 environment, and all binaries executed will run through QEMU emulation.

## What mchroot Does

mchroot automates several steps required to make cross-architecture chroot environments work seamlessly:

- Detects the target architecture inside the specified root directory (currently supports arm32 and arm64)
- Verifies that QEMU is available for the required architecture
- Compiles and installs a wrapper used for execution inside the chroot
- Registers binfmt_misc handlers to automatically invoke QEMU for non-native binaries
- Copies the required QEMU binary and wrapper into the chroot environment
- Mounts /proc, /dev and /sys if not already mounted
- Copies /etc/resolv.conf to ensure proper DNS resolution
- Finally executes chroot with the provided arguments

## Prerequisites

### Host and Target Support

mchroot has been tested on x86_64 hosts and supports ARM32 and ARM64 target environments.

### QEMU

QEMU must be installed with user-mode emulation enabled and built as static binaries.

Example configuration:
```
QEMU_USER_TARGETS="aarch64 arm"
```
Recommended USE flags:
```
app-emulation/qemu static-user
dev-libs/glib static-libs
sys-apps/attr static-libs
sys-libs/zlib static-libs
dev-libs/libpcre static-libs
```
### binfmt_misc

Ensure that binfmt_misc is enabled in the kernel or available as a module.

## Setup

Clone the repository:

git clone https://github.com/coffnix/mchroot.git

Run directly:

./bin/mchroot /path/to/chroot

## Acknowledgements

This project is based on fchroot and automates techniques originally documented in:

https://wiki.gentoo.org/wiki/Embedded_Handbook/General/Compiling_with_qemu_user_chroot
https://github.com/sakaki-/gentoo-on-rpi3-64bit/wiki/Build-aarch64-Packages-on-your-PC%2C-with-User-Mode-QEMU-and-binfmt_misc

## License

This project is a fork of fchroot and retains its original licensing.

Copyright 2020 Daniel Robbins

Licensed under the Apache License, Version 2.0
http://www.apache.org/licenses/LICENSE-2.0

Modifications for MacaroniOS have been applied.
