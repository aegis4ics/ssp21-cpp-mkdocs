# Configuration

The proxy accepts a single argument: the path to a [YAML](https://yaml.org/) configuration file. This file contains a hierarchical
configuration model that specifies the configuration for an arbitrary number of SSP21 `initiator` or `responder` sessions.

``` yaml

qkd_sources: []                  # sequence of QKD sources (e.g. QIX)
sessions:                        # sequence of SSP21 proxy sessions
  - id: "session1"               # an identifer for the session for logging purposes
    levels: "iwemf"              # enabled log levels for this session
    transport:                   # configuration of the transport
      type: "tcp"                # type of transport { tcp, udp }
#     ...                          OMITTED: transport type specific parameters omitted
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
#         ...                      OMITTED: initiator or responder specific session parameters
      handshake:                 # configuration of the handshake
#       ...                        OMITTED: initiator or responder specific handshake parameters
        type: "shared_secret"    # type of handshake { shared_secret, preshared_public_key, qkd }
#       ...                        OMITTED: handshake type specific parameters
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

## Link-layer

The SSP21 link-layer provides framing for stream oriented transports like TCP. Additionally, it provides 16-bit source and destination
addresses which can be used for multiplexing multiple sessions on a single transport.

TCP must always enable the link-layer, or an exception is thrown during startup.

```
link_layer:
  enabled: true
  address:
    local: 10
    remote: 1
```

UDP may be used with or without the link-layer. If the link-layer is not used, the address information may be omitted:

```
link_layer:
    enabled: false
```

## Transport modes

The proxy supports TCP and UDP transport. Both modes take specific parameters. The following subsections provide configuration
snippets for each mode.

### TCP

TCP transport requires the following parameters.

```
transport:
  type: "tcp"
  max_sessions: 1                # The maximum number of concurrent sessions on the listening side
    listen:           
      address: "127.0.0.1"       # IP address of adapter on which to listen
      port: 20000                # Port on which to listen
    connect:
      address: "127.0.0.1"       # IP to connect
      port: 20001                # Port to connect
```

When using TCP as an initiator, the proxy will not attempt a SSP21/TCP connection until the application (raw) side
of the connection occurs.

!!! note
    With regard to listening, certain special addresses have the typical meanings:

	* *"0.0.0.0"* - listen on all adapters

	* *"127.0.0.1"* - only accept connections from the loopback adapter

### UDP

UDP is a connectionless transport protocol, so you must specify addresses and ports
for both receiving and transmitting separately.

```
transport:
  type: "udp"      
  raw_rx:
    address: "127.0.0.1"
    port: 20000
  raw_tx:
    address: "127.0.0.1"
    port: 20001
  secure_rx:
    address: "127.0.0.1"
    port: 20002
  secure_tx:
    address: "127.0.0.1"
    port: 20003
```

The following diagram illustrates how the parameters above are applied:

![UDP proxy](../img/udp.svg)

!!! warning
    Be careful when setting the ports so that no communication loops back creating a cycle.

## Handshake types

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
