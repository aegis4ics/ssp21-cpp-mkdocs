# About

SSP21-CPP is a platform-neutral reference implementation of the [SSP21](TODO) protocol written in modern C++ provided under the [BSD-3 clause license](https://opensource.org/licenses/BSD-3-Clause).

## Features

* Scalable asynchronous design backed by arbitrary event loop (examples based on ASIO)
* Pluggable cryptographic backend with libsodium implementation provided
* Secure zero-copy (un)parsers generated from a model of the message grammars
* Implements all of the key modes defined in SSP21
    * shared secrets
	* quantum key distribution (QKD)
	* pre-shared public keys
	* industrial certificates
	

## C++11/14

The core library should compile for any platform that has a compiler that implements C++11/14 features:

* Ownership using `std::unique_ptr<T>` and `std::shared_ptr<T>` where appropriate
* Time management using `std::chrono::steady_clock`
* Lambda expressions are used throughout the implementation
* Variadic templates are applied specifically to make parsing/formatting more expressive

### Minimum Compiler Versions

* Visual Studio 2017
* GCC 4.9.x
* Clang  3.4


