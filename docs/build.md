## Cloning

Clone the repository **recursively** to checkout all dependencies except libsodium:

!!! important
    > git clone --recursive https://github.com/aegis4ics/ssp21-cpp.git

## Dependencies

All dependencies except for libsodium are provided as gitsubmodules and don't require separate installation.

All dependencies are available under compatible non-copyleft licenses.

### Required

The following dependencies are required to build the core library and includes types in the external API:

| Library                                            |  License  | Required  | Submodule  | Function                                                         |
|----------------------------------------------------|-----------|-----------|------------|------------------------------------------------------------------|
| [ser4cpp](https://github.com/automatak/ser4cpp)    |  BSD-3    | yes       | yes        | low-level serialization routines                                 |
| [exe4cpp](https://github.com/automatak/exe4cpp)    |  BSD-3    | yes       | yes        | event-loop (executor) abstractions                               |
| [log4cpp](https://github.com/automatak/log4cpp)    |  BSD-3    | yes       | yes        | logging library with extensible log levels                       |

### Optional

The following dependencies are only required for the proxy application, integration tests, etc:

| Library                                            |  License  | Required  | Submodule  | Function                                                         |
|----------------------------------------------------|-----------|-----------|------------|------------------------------------------------------------------|
| [libsodium](https://download.libsodium.org/doc/)   |  ISC      | no        | no         | default cryptographic backend                                    |
| [ASIO](https://think-async.com/Asio/)              |  Boost    | no        | yes        | event loop + x-platform I/O                                      |
| [yaml-cpp](https://github.com/jbeder/yaml-cpp)     |  MIT      | no        | yes        | config file parsing in proxy                                     |
| [argagg](https://github.com/vietjtnguyen/argagg)   |  MIT      | no        | yes        | cmd line argument parser                                         |

## Install libsodium

You'll need to install [libsodium](https://download.libsodium.org/doc/) if you intend to build the proxy application
or run the integration tests.

!!! note "Debian / Ubuntu"
    > apt install libsodium

On Windows, download and install the prebuilt [binaries](https://download.libsodium.org/libsodium/releases/). When invoking CMake,
add the following flag so that libsodium can be located:

!!! note "Windows"
    When invoking CMake, tell it where to find the installation, e.g.:

    > -Dsodium_DIR=C:\libs\libsodium-`<version #>`-msvc

	 (path will vary based on version)

## Invoke CMake

[CMake](https://cmake.org/) is used to generate a build system for your platform. Create a separate folder for your build artifacts,
and then invoke CMake telling it where to find the root CMakeLists.txt file.

```
> cd ssp21-cpp
> mkdir build; cd build
> cmake .. 
```

This will create your Makefile, Visual Studio Solution, etc. You can then build and install the library.

## Build and install

The actual build command / proceedure depends on your platform, e.g. on Linux:

```
> sudo make install
```

