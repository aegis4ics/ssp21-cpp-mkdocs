# Introduction

The library provides a bump-in-the-wire (BitW) application that can intercept arbitrary TCP traffic,
wrap it in SSP21, and redirect to a different host and port. This functionality is almost identical to what
[stunnel](https://www.stunnel.org/) provides for transport layer security (TLS).

![IStack](../img/proxy.svg)

Of course it's also possible to interface a single proxy instance with something that supports SSP21 natively. The proxy
is meant to be a turn-key solution for adding security to legacy applications and a convienent way to perform interoperability
testing against the SSP21-CPP reference implementation.