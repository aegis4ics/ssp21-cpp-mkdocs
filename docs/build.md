# Building

Clone the repository recursively to checkout most dependencies:

!!! important
    git clone --recursive https://github.com/aegis4ics/ssp21-cpp.git


## libsodium

You'll need to install [libsodium](https://download.libsodium.org/doc/) if you intend to build the proxy application
or run the integration tests.

!!! note "Debian / Ubuntu"
    apt install libsodium

On Windows, download and install the prebuilt [binaries](https://download.libsodium.org/libsodium/releases/). When invoking CMake,
add the following flag so that libsodium can be located:

!!! note "Windows only: Tell CMake where to find libsodium"
    -Dsodium_DIR=C:\libs\libsodium-1.0.10-msvc (path will vary based on version)



## CMake

[CMake](https://cmake.org/) is used to generate a build system for your platform. Create a separate folder for your build artifacts,
and then invoke CMake telling it where to find the root CMakeLists.txt file.



