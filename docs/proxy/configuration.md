# Configuration

The proxy accepts a single argument: the path to a [YAML](https://yaml.org/) configuration file. This file contains a hierarchical
configuration model that specifies the configuration for an arbitrary number of SSP21 `initiator` or `responder` sessions.

``` yaml

qkd_sources: []                  # sequence of QKD sources (e.g. QIX)
sessions:                        # sequence of SSP21 proxy sessions
  - id: "session1"               # an identifer for the session for logging purposes
    levels: "iwemf"              # enabled log levels for this session
    link_layer:                  # configuration of the link layer
      enabled: true              # if enabled, wrap SSP21 with the link-layer
      address:                   # addressing configuration required if 'enabled' == true
        local: 10                # the address of the local node
        remote: 1                # the address of the local node
    security:                    # configuration of the SSP21 crypto layer
      mode: "initiator"	         # SSP21 mode: { initiator, responder }
      session:                   # configuration of the cryptographic session
        max_payload_size: 4096   # maximum number of payload bytes that can be sent or received
        ttl_pad:                 # how much of a time pad to apply to session messages
          value: 10              # the value associated with the 'unit'
          unit: seconds          # unit of the time {milliseconds, seconds, minutes, hours}
#         ...                      omitted: initiator or responder specific session parameters
      handshake:                 # configuration of the handshake
#       ...                        omitted: initiator or responder specific handshake parameters
        type: "shared_secret"    # type of handshake { shared_secret, preshared_public_key, qkd }
#       ...                        omitted: handshake type specific parameters
    transport:                   # configuration of the transport for 
      type: "tcp"                # type of transport { tcp, udp }
#     ...                          omitted: transport type specific parameters omitted
```

The YAML skeleton above describes all of the configuration values that must be present irrespective of the various modes that can be configured.
The sections where configuration information have been omitted are dependent on the values of other parameters.

### Log levels

The `levels` parameter in a session configuration contains a string of characters that specify which logs messages to enable.

| level           | character | description                                                                                  |
|-----------------|-----------|----------------------------------------------------------------------------------------------|
| event           | v         | High priority messages such as startup and shutdown                                          |
| error           | e         | Errors that don't occur during normal operation                                              |
| warning         | w         | Less severe than error, events like discconects                                              |
| info            | i         | Informational (non-error or warning) messages                                                |
| debug           | d         | Detailed, but normally overhelming messages                                                  |
| crypto message  | m         | High level decoding of SSP21 messages (type) and length                                      |
| crypto fields   | f         | Detailed decoding of the individual fields in SSP21 messages                                 |

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
| raw.rx.ip                    | The IP address where the proxy listens for insecure traffic                                                                  |      
| raw.rx.port                  | The port where the proxy listens for insecure traffic                                                                        |
| raw.tx.ip                    | The IP address to which the proxy writes insecure traffic                                                                    |      
| raw.tx.port                  | The port to which the proxy writes insecure traffic                                                                          |      
| secure.rx.ip                 | The IP address where the proxy listens for SSP21 traffic                                                                     |      
| secure.rx.port               | The port where the proxy listens for SSP21 traffic                                                                           |
| secure.tx.ip                 | The IP address to which the proxy writes SSP21 traffic                                                                       |      
| secure.tx.port               | The port to which the proxy writes SSP21 traffic                                                                             |      



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
