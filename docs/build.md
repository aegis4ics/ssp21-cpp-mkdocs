# Building

Clone the repository **recursively** to checkout all dependencies except libsodium:

!!! important
    > git clone --recursive https://github.com/aegis4ics/ssp21-cpp.git


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

