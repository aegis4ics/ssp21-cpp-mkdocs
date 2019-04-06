# Building

Clone the repository recursively to checkout most dependencies:

!!! important
    git clone --recursive https://github.com/aegis4ics/ssp21-cpp.git


# libsodium

You'll need to install [libsodium](https://download.libsodium.org/doc/) if you intend to build the proxy application
or run the integration tests.

!!! note "On Debian-based systems:"
    apt install lidsodium
    


# CMake

[CMake](https://cmake.org/) is used to generate a build system for your platform of choice

