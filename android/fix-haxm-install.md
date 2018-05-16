# Fix HAMX driver install on Windows x64

## Problem

You want to use Android Emulators with Android Studio 3. The emulator requires HAMX (Hardware Accelerated Execution Manager) driver to run.

Problem 1: The HAXM install may fail with "unknown error".

Problem 2: you are able to install HAXM driver but AVD Manager says "your HAXM driver is out-of-date".

## Solution

- download the latest HAXM driver (I tried with v7.1.0). 
- extract the downloaded zip file
- extract the installer file (`intelhaxm-android.exe`) using 7-Zip or other archive program
- enter new folder `intelhaxm-android`
- extract `hax64.msi` file using 7-Zip
- enter new folder `hax64`
- rename inf, sys, aad cat files by removing the custom extensions
  - `intelhaxm.inf.win10_64` to `intelhaxm.inf`
  - `intelhaxm.sys.win10_64` to `intelhaxm.sys`
  - `intelhaxm_x64.cat.win10_64` to `intelhaxm_x64.cat`
- open a command line in `hax64` folder
- run this command: 
  - `HaxInst64.exe /i intelhaxm.inf 1`
  - there should be no error reported
- go one folder up (`intelhaxm-android`) and run `setup.exe`
- verify windows service by running these commands
  - `sc query intelhaxm`
  - `sc stop intelhaxm`
  - `sc start intelhaxm`
  - they should return with state of 4 (running, stopped, running)

## Links

- https://software.intel.com/en-us/articles/intel-hardware-accelerated-execution-manager-intel-haxm