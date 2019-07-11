# Configuration

The proxy accepts a single argument: the path to an [INI-style](https://en.wikipedia.org/wiki/INI_file) configuration file. 
This file may contain an arbitrary number of configuration sections. Each section represents a single SSP21 `initiator` or `responder`
configuration, e.g.:

```
[i-proxy]
proto_type=tcp
mode=<initiator|responder>
log_levels=iwemf
remote_address=1
local_address=10
max_sessions=1
listen_port=20000
listen_endpoint=127.0.0.1
connect_port=20001
connect_endpoint=127.0.0.1
handshake_mode=<shared_secret|qkd|preshared|certificate>
; additional parameters depend on the handshake mode 
```

The name of section (i-proxy above) is used for logging purposes to uniquely identify log messages for a particular session.

## Standard options

| key              | description                                                                                                                  | 
|------------------|------------------------------------------------------------------------------------------------------------------------------|
| proto_type       | Defines the proxy uses `tcp` or `udp` as its transport protocol.                                                             |
| mode             | Defines whether the session is an SSP21 `initiator` or a `responder`.                                                        |
| log_levels       | string of enabled log levels (see [table](#log-levels)).                                                                     |
| remote_address   | 16-bit remote address field to place in SSP21 link frames (only if a link-layer is used).                                    |
| local_address    | 16-bit local address field to place in SSP21 link frames and expect from remote SSP21 peer (only is a link-layer is used).   |
| handshake_mode   | Defines which of the four handshake modes to use. Additional parameters depend on this mode                                  |

### Log levels

| level           | character | description                                                                                  |
|-----------------|-----------|----------------------------------------------------------------------------------------------|
| event           | v         |                                                                                              |
| error           | e         |                                                                                              |
| warning         | w         |                                                                                              |
| info            | i         |                                                                                              |
| debug           | d         |                                                                                              |
| crypto message  | m         | Log the type of SSP21 message received and its length                                        |
| crypto fields   | f         | Log the individual fields in SSP21 messages                                                  |

## Transport-specific parameters

### TCP mode

In TCP, the link-layer is always used because the transport layer is stream based. Therefore, SSP21 link-layer acts
as a framing protocol.

| key              | description                                                                                                                  | 
|------------------|------------------------------------------------------------------------------------------------------------------------------|
| max_sessions     | Number of allowed concurrent connections. When a new connection is received, the oldest is closed.                           |
| listen_port      | For `initiator` mode, port where proxy will listen for the insecure protocol connection.                                     |
|                  | For `responder` mode, port where proxy will listen for SSP21 connection from an `initiator`                                  |
| listen_endpoint  | For `initiator` mode, the IP address of the adapter on which the proxy will listen for the insecure protocol connection.     |
|                  | For `responder` mode, the IP address of the adapter on which the proxy will listen for SSP21 connection from an `initiator`  |
| connect_port     | For `initiator` mode, port where proxy will attempt to connect and send SSP21 traffic.                                       |
|                  | For `responder` mode, port where proxy will attempt to connect and send insecure protcol traffic.                            |
| connect_endpoint | For `initiator` mode, the IP address to connect to and send SSP21 traffic                                                    |
|                  | For `responder` mode, the IP address to connect to and send insecure protcol traffic                                         |

### UDP mode

In UDP, the link-layer is not mandatory, since the transport protocol is datagram based. Therefore, it is possible
to configure the proxy to include or not the link-layer with the `crypto_only` setting. Adding the link-layer permits
to do data integrity if the operating system is not doing it on UDP in IPv4 (the UDP checksum is mandatory in IPv6).

Since UDP is a connectionless transport protocol, it is mandatory to specify all the addresses and ports. The following schema
illustrates the different settings:

![UDP proxy](../img/udp.svg)

!!! warning
    Be careful when setting the ports so that no communication loops back.

| key                          | description                                                                                                                  | 
|------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| crypto_only                  | Specify if the SSP21 stack should include the optional link-layer.                                                           |
| source_receive_endpoint      | For `initiator` mode, IP address where the proxy will listen for the insecure protocol traffic.                              |
|                              | For `responder` mode, IP address where the proxy will listen for SSP21 traffic from an `initiator`.                          |
| source_receive_port          | For `initiator` mode, port where the proxy will listen for the insecure protocol connection.                                 |
|                              | For `responder` mode, port where the proxy will listen for the SSP21 connection from an `initiator`.                         |
| source_send_endpoint         | For `initiator` mode, IP address where the proxy will send insecure protocol traffic.                                        |
|                              | For `responder` mode, IP address where the proxy will send SSP21 traffic.                                                    |
| source_send_port             | For `initiator` mode, port where the proxy will send insecure protocol traffic.                                              |
|                              | For `responder` mode, port where the proxy will send SSP21 traffic.                                                          |
| destination_receive_endpoint | For `initiator` mode, IP address where the proxy will listen for SSP21 traffic from a `responder`.                           |
|                              | For `responder` mode, IP address where the proxy will listen for insecure protocol traffic.                                  |
| destination_receive_port     | For `initiator` mode, port where the proxy will listen for SSP21 traffic from a `responder`.                                 |
|                              | For `responder` mode, port where the proxy will listen for insecure protocol traffic.                                        |
| destination_send_endpoint    | For `initiator` mode, IP address where the proxy will send SSP21 protocol traffic.                                           |
|                              | For `responder` mode, IP address where the proxy will send insecure protocol traffic.                                        |
| destination_send_port        | For `initiator` mode, port where the proxy will send SSP21 protocol traffic.                                                 |
|                              | For `responder` mode, port where the proxy will send insecure protocol traffic.                                              |


## Mode-specific parameters

Different handshake modes require different paramters described in the following sub-sections.

### Shared-secret mode

| key                      | description                                                                  | 
|--------------------------|------------------------------------------------------------------------------|
| shared_secret_key_path   | path to an ICF file containing 256-bit symmetric key                         |

### QKD mode

| key                      | description                                                                         | 
|--------------------------|-------------------------------------------------------------------------------------|
| serial_port              | string ID of the serial port (e.g. COM1 or /dev/ttysO) where keys will be received  |

!!! note 
    The format of the key data transmitted over the serial port is not standarized yet and is only interoperable at the moment with QKD transceivers from 
	[Qubitekk](http://qubitekk.com/).

### Pre-shared public key mode

| key                      | description                                                              | 
|--------------------------|--------------------------------------------------------------------------|
| local_public_key_path    | ICF file containing this side's public DH key                            |
| local_private_key_path   | ICF file containing this side's private DH key                           |
| local_public_key_path    | ICF file containing the remote peers's public DH key                     |

### Certificate mode

| key                      | description                                                                          | 
|--------------------------|--------------------------------------------------------------------------------------|
| local_public_key_path    | ICF file containing this side's public DH key                                        |
| local_private_key_path   | ICF file containing this side's private DH key                                       |
| authority_cert_path      | ICF file contain authority certificate on which to base trust of remote certificates |
| local_cert_path          | ICF file containing certificate chain to present to remote peer                      |
