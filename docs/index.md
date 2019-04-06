# About

SSP21-CPP is a platform-neutral reference implementation of the [SSP21](TODO) protocol written in modern C++ provided under the [BSD-3 clause license](https://opensource.org/licenses/BSD-3-Clause).

## Platform Support

The core library should compile for any platform that has a compiler that implements C++14 features:

* Ownership using `std::unique_ptr<T>` and `std::shared_ptr<T>` where appropriate
* Time management using `std::chrono::steady_clock`
* Lambda expressions are used throughout the implementation
* Variadic templates are applied specifically to make parsing/formatting more expressive

## Minimum Compiler Versions

* Visual Studio 2017
* GCC 4.9.x
* Clang  3.4

## Dependencies

Most dependencies are provided as gitsubmodules and don't require special installation. The libsodium crypto library is the only exception.

All dependencies are available under liberal (non-copyleft) open source licenses.

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
| [inih](https://github.com/benhoyt/inih)            |  New BSD  | no        | yes        | config file parsing in proxy                                     |
| [argagg](https://github.com/vietjtnguyen/argagg)   |  MIT      | no        | yes        | cmd line argument parser                                         |


