# Introduction

Unlike some protocol libraries, SSP21-CPP does not provide I/O by default. The user of the library must bind
the library callbacks to both the application code and to the network I/O.

This allows the user to bind the library to any transport, e.g. :

* TCP/IP
* UDP/IP
* Serial ports

## IStack

The primary interface used to interact with the library is [ssp21::IStack]({{base_doxygen_path}}/classssp21_1_1IStack.html).

There are a number of factory methods for both the [initiator]({{base_doxygen_path}}/namespacessp21_1_1initiator_1_1factory.html) 
and the [responder]({{base_doxygen_path}}/namespacessp21_1_1responder_1_1factory.html) available that allow the user to create a
`shared_ptr<ssp21::IStack>` for the available key modes.

![IStack](../img/stack.svg)

The factory methods take a number of arguments that will be described in the following sections.

## Integration

User applications must provide their own implementations of both [IUpperLayer]({{base_doxygen_path}}/classssp21_1_1IUpperLayer.html) and
[ILowerLayer]({{base_doxygen_path}}/classssp21_1_1ILowerLayer.html) to integrate with `IStack`.

The user provided `ILowerLayer` transmits fully formatted SSP21 messages on behalf of the stack, and notifies the stack's `IUpperLayer` when raw
data is available for reading.

The user provided `IUpperLayer` receives authenticated (and possibly decrypted) payload data from the stack. It uses the `ILowerLayer` provided by the
stack to transmit user data.

!!! info
    The [integration test suite](https://github.com/ssp21/ssp21-cpp/blob/master/cpp/tests/integration/IntegrationTestSuite.cpp) provides the
	simplest example of implementing these interfaces and integrating with the library's API. Alternatively, the 
	[proxy application](https://github.com/ssp21/ssp21-cpp/tree/master/cpp/exe/proxy) is a real world integration with TCP/IP as the transport
	layer, but is more complex.