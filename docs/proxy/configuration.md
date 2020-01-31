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
#       ...                        OMITTED: initiator or responder specific session parameters
      handshake:                 # configuration of the handshake
#       ...                        OMITTED: initiator or responder specific handshake parameters
        type: "shared_secret"    # type of handshake { shared_secret, preshared_public_key, qkd }
#       ...                        OMITTED: handshake type specific parameters
```

The YAML skeleton above describes all of the configuration values that must be present irrespective of the various modes that can be configured.
The sections where configuration information have been omitted are dependent on the values of other parameters.

## Log levels

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

## Session parameters

The initiator requires a handful of additional session parameters not required when configuring a responder.

```
security:
  mode: "initiator"	  
  session:
    max_payload_size: 4096
    ttl_pad:
      value: 10
      unit: seconds
# ----- parameters below here are only required for an initiator -----
    max_nonce_value: 32768                      # maximum value for a session nonce (maximum 65535)
    nonce_renegotiation_trigger_value: 32700    # nonce value that triggers renegotiation
    max_session_duration:                       # maximum validity time for an established session
      value: 1
      unit: hours
    session_time_renegotiation_trigger:         # session time that triggers renegotiation
      value: 59
      unit: minutes
```

## Handhake Parameters

The initiator specifies the cryptographic primitives to use when establishing a session. There is no negotiation in 
SSP21, therefore these parameters are only required when configuring an initiator.

``` yaml
...
handshake:
  algorithms:
    session_crypto_mode: hmac_sha256_16              # { hmac_sha256_16, aes_256_gcm }
...
```

You can currently choose between an HMAC auth-only mode or authenticated encryption using AES-GCM.


!!! note
    In the future, the initiator will also allow you to specify additional cryptographic primitives
    for the the KDF, handshake hash, and ECC key types in public key modes. At the moment, there
    is only one choice for these primitives.

### Key modes

Different handshake modes require different paramters described in the following sub-sections.

!!! important
    Initiator's require some additional configuration in the handshake section to specify some timeouts:

```yaml
handshake:
  response_timeout:
    value: 2
    unit: seconds
  retry_timeout:
    value: 5
    unit: seconds
  type: # ....
```

These timeout parameters are not required when configuring a responder.


#### Shared-secret

A shared secret handshake only requires a single parameter specifiying where the shared key resides.

```yaml
handshake:
  type: "shared_secret"
  shared_secret_key_path: "./shared_secret.icf"  # path to the file containing the shared secret
```

#### Pre-shared public key

Pre-shared key modes requires the paths to 3 keys be provideded:

```
 handshake:  
  type: "preshared_public_key"
  local_private_key_path: "./local_private_key.icf"     # private part of the local static DH key
  local_public_key_path: "./local_public_key.icf"       # public part of the local static DH key
  remote_public_key_path: "./remote_public_key.icf      # public key of the remote party
```

#### QKD

QKD mode allows for a source of keys to be shared across multiple proxy sessions. The first item
in a configuration file is a list of these sources

``` yaml
# a sequence of QKD sources
qkd_sources:
  - qkd_source_id: "source1"        # a unique identifier for the source
    type: "qix"                     # the only supported type ATM is qix
    num_subscribers: 1              # the number of subscribers
    serial:                  
      device: "/dev/pts/3"          # the serial device, e.g. /dev/ttyS0 or COM1
      baud: 9600                    # baud rate, defaults to 9600 if not specified
      data_bits: 8                  # data bits, defaults to 8 if not specified
      flow_control: none            # flow control, defaults to 'none' if not specified
      stop_bits: one                # stop bits, defaults to 'one' if not specified
```

In a session's handshake configuration section, the following parameters are required:

```
  handshake:
    type: "qkd"               
    key_source_id: "source1"        # source id matching the id in the list above
    subscriber_id: 0                # unique subscriber id in the range [0 .. (num_subscribers - 1)]
    max_key_cache_size: 100         # maximum number of cached keys for this particular session.
```

!!! note 
    The format of the key data transmitted over the serial port is not standardized yet and is only interoperable at the moment with QKD transceivers from 
	[Qubitekk](http://qubitekk.com/).

QIX keys contain `uint64_t` key identifier field which increments. This allows the QKD source (serial port) to send the keys to each subscriber in a round-robin fashion.
Each subscribing session maintains a key off of configurable length (`max_key_cache_size`). 

Each key is sent to a subscribing session using the key identifier modulo the number of subscribers:

```
subscriber_id = key_identifier % num_subscribers
```

!!! warning
    Sessions at paired ends of a QKD link must agree on both the `num_subscribers` parameter, and each paired session
    must agree on `subscriber_id`. If either of these two parameters don't match, initiators and subscribers will not
    have keys with the same key identifiers in their corresponding key caches, and they will not be able to perform a
    handshake. If a responder returns the error code `KEY_NOT_FOUND` this is a good indicator of a misconfiguration. 

![QKD subscribers](../img/qkd.svg)

The diagram above shows a configuration with a single QKD source receiving QIX frames and sending the keys
to subscribers #0, #1, and #2. In this case the `num_subscribers` parameter is set to 3.  If it was set to a number greater
than 3, keys where `key_id % num_subscribers > 2` would be silently discarded . If it were set to a number less than 3,
the proxy would fail to start with a configuration error since one or more of the subscriber would never receive keys.

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