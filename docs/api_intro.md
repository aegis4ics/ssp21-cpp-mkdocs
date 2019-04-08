# Introduction

Unlike some protocol libraries, SSP21-CPP does not provide I/O by default. The user of the library must bind
the library callbacks to both the application code and to the network I/O.

This allows the user to bind the library to any transport, e.g. :

* TCP/IP
* UDP/IP
* Serial ports

## IStack

The primary interface used to interact with the library is `ssp21::IStack`.

![IStack](img/stack.svg)

There are a number of [factory methods]({{base_doc_path}}/{{version}}/TODO) available that allow the user to create a
`shared_ptr<ssp21::IStack>`.