# About

SSP21-CPP is a platform-neutral reference implementation of the [SSP21](TODO) protocol written in modern C++ provided under the [BSDv3 license](https://opensource.org/licenses/BSD-3-Clause).

## Platform support

The core library should compile for any platform that has a compiler that implements C++14 features:

* Ownership using `std::unique_ptr<T>` and `std::shared_ptr<T>` where appropriate
* Time management using `std::chrono::steady_clock`
* Lambda expressions are used throughout the implementation
* Variadic templates are applied specifically to make parsing/formatting more expressive

## Minimum compiler versions

* Visual Studio 2017
* GCC 4.9.x
* Clang  3.4

## Required dependencies

The core library depends on the following header-only libraries (all BSDv3 licensed):

* [ser4cpp](https://github.com/automatak/ser4cpp) is a library for safe low-level serialization routines.
* [exe4cpp](https://github.com/automatak/exe4cpp) is an event-loop (executor) abstraction with implementations based on ASIO.
* [log4cpp](https://github.com/automatak/log4cpp) is a logging library with extensible log levels using ser4cpp sequence types.

## Optional dependencies

* [libsodium](https://download.libsodium.org/doc/) is used for the default cryptographic backend
* 
