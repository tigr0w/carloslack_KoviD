# Manual testing of KoviD features

This document describes the process of testing the features of Kovid LKM.
Please see `docs/QEMUSetupForTesting.md` that contains info for qemu setup. 

## Fetch LFS and submodules

```
$ git fetch --recurse-submodules
```
or:

```
$ git submodule update --remote --recursive
```

LFS should be fetched:

```
$ git lfs fetch --all
```

## Build KoviD

Old way (using pre-existing GNU Makefile):
```
$ cd KoviD
$ make clean && make CC=gcc-12
```

New way by using CMake:

```
$ mkdir build && cd build
$ cmake ../ -DCMAKE_C_COMPILER=gcc-12 && make CC=gcc-12
```

or

```
$ cmake ../ && make
```

NOTE: You can customize it:

```
$ cmake -DPROCNAME=myproc -DMODNAME=mymodule ../
```

### Building for Linux version other than native

```
$ cmake ../ -DKOVID_LINUX_VERSION=5.10 -DCMAKE_C_COMPILER=gcc-12 && make CC=gcc-12
-- The C compiler identification is GNU 12.3.0
-- The CXX compiler identification is GNU 11.4.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/gcc-12 - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Error at CMakeLists.txt:14 (message):
  Kernel headers for version 5.10 not found in /lib/modules/5.10/build


-- Configuring incomplete, errors occurred!
See also "build/CMakeFiles/CMakeOutput.log".
```

But lets say we built `linux` in `projects/private/kovid/linux`, we can set up manually the variables:

```
$ cmake ../ -DKOVID_LINUX_VERSION=5.10 -DKERNEL_DIR=projects/private/kovid/linux -DKOVID_LINUX_VERSION=5.10 -DCMAKE_C_COMPILER=gcc
-- The C compiler identification is GNU 11.4.0
-- The CXX compiler identification is GNU 11.4.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/gcc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Linux Target: 5.10
-- Linux Headers: projects/private/kovid/linux
-- Extra CFLAGS: -Iprojects/private/kovid/linux/include -Iprojects/private/kovid/KoviD/src -Iprojects/private/kovid/KoviD/fs -I$(KERNEL_DIR)/include/generated -Wno-error -DPROCNAME="changeme" -DMODNAME="kovid" -DKSOCKET_EMBEDDED -DDEBUG_RING_BUFFER -DCPUHACK -DPRCTIMEOUT=1200 -DUUIDGEN="5a803031-366c-4070-8656-1f940a2467b8" -DJOURNALCTL="/usr/bin/journalctl"
-- Configuring done
-- Generating done
-- Build files have been written to: projects/private/kovid/build
$ make PROCNAME="mykovidproc"
-- Selected PROCNAME is mykovidproc
```

If you miss the `PROCNAME`, it will emit an error during build time:

```
$ make
...
*** ERROR: PROCNAME is not defined. Please invoke make with PROCNAME="your_process_name".  Stop.
```

### Run tests

Please make sure to install llvm-tools, since we will be using some of the tools for testing infrastructure:

```
sudo apt-get install llvm-18-dev
sudo apt-get install llvm-18-tools
sudo apt-get install libslirp-dev
sudo apt-get install qemu-system-x86
```

Run tests:

```
$ cd KoviD && mkdir build && cd build
$ cmake ../ -DKOVID_LINUX_VERSION=5.10 -DKERNEL_DIR=projects/private/kovid/linux -DKOVID_LINUX_VERSION=5.10 -DCMAKE_C_COMPILER=gcc
$ make PROCNAME="myprocname"
$ make check-kovid
```

## Linux Kernel 5.10

1. Hide itself

TODO