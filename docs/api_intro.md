# Introduction

Unlike some protocol libraries, SSP21-CPP does not provide I/O by default. The user of the library must bind
the library callbacks to both the application code and to the network I/O.

This allows the user to bind the library to any transport, e.g. :

* TCP/IP
* UDP/IP
* Serial ports

## IStack

The primary interface used to interact with the library is [ssp21::IStack]({{base_doxygen_path}}/{{version}}/classssp21_1_1IStack.html).

![IStack](img/stack.svg)

There are a number of factory methods for both the [initiator]({{base_doxygen_path}}/{{version}}/namespacessp21_1_1initiator_1_1factory.html) 
and the [responder]({{base_doxygen_path}}/{{version}}/namespacessp21_1_1responder_1_1factory.html) available that allow the user to create a
*shared_ptr&lt;ssp21::IStack&gt;* for the available key modes.