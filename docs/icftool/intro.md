# Usage

SSP21-CPP provides and executable that can generate keys and certificates in the format expected by the proxy called `icftool`. ICF stands for
"Industrial Container Format" and is an unstandardized binary file format. 

!!! warning 
    The ICF file format was used for expediancy in the first release because it uses the SSP21 serialization format and rules.
	Therefore, it didn't require manually writing any (un)parsers. The format for the container files might be changed to something more
	conventional like the PEM format frequently used to encode X.509 and keys in the future since only the proxy relies on them.

The options presented with the `-h` (or ---help) argument are fairly self-explanatory.

```
> ./icftool  -h

Options:

    -h, --help
        shows this help message
    -s, --show
        show contents of one or more <icf files ....>
    -r, --shared
        generate a shared secret <key file>
    -x, --x25519
        generate a x25519 key pair <private key file> <public key file>
    -e, --ed25519
        generate a Ed25519 key pair <private key file> <public key file>
    -c, --cert
        interactively generate a <certificate file> for a <public key file> signed by a <private key file>
    -a, --append
        append all entries from one <icf file> and another <icf file> and write them to a destination <icf file>
    -d, --days
        number of days for the certificate to remain valid from now
    -l, --level
        signing level [0, 5] of the certificate. 0 == endpoint. >0  is an authority
```

## Examples

### generate x25519 keypair

```
./icftool.exe -x outstation25519.priv.icf outstation25519.pub.icf
generating x25519 key pair...
wrote: outstation25519.priv.icf
wrote: outstation25519.pub.icf
```

### generate ed25519 keypair

```
.\icftool.exe -d authority.priv.icf authority.pub.icf
generating Ed25519 key pair
wrote: authority.priv.icf
wrote: authority.pub.icf
```

### show file contents

```
./icftool.exe -s authority.priv.icf authority.pub.icf

File: authority.priv.icf
Type: ed25519_private_key
  key-data (length == 64)
    49:b4:d0:64:ca:60:c3:e5:c7:c4:56:8d:d3:82:b9:a0
    6a:a4:9a:7e:18:cf:51:cb:43:85:45:3c:52:c4:5d:e3
    23:28:0d:c2:43:c6:11:c7:1e:36:75:51:90:5f:01:3f
    a4:5f:31:51:60:4c:20:3b:80:b5:d8:35:ae:c4:2c:8f

File: authority.pub.icf
Type: ed25519_public_key
  key-data (length == 32)
    23:28:0d:c2:43:c6:11:c7:1e:36:75:51:90:5f:01:3f
    a4:5f:31:51:60:4c:20:3b:80:b5:d8:35:ae:c4:2c:8f
```

### generate certificates

```
./icftool.exe -c outstation.cert.icf .\outstation25519.pub.icf authority.priv.icf
How many days (from now) should the certificate remain valid?
365
Enter the certificate signing level (0 == endpoint certificate, max == 6):
0

./icftool.exe -s .\outstation.cert.icf

File: .\outstation.cert.icf
Type: certificate_chain

certificate #1
  signature (length == 64)
    ab:b8:8b:85:72:d8:ff:31:6c:e7:55:ac:cd:44:5e:b3
    c7:2e:aa:b5:47:d7:65:5b:dd:4a:0a:fd:f1:c0:ba:99
    3d:40:a5:27:83:aa:93:cc:88:2d:7b:c0:b6:cf:6e:de
    d0:10:74:b3:1a:eb:f0:5d:60:6a:f8:15:51:bd:eb:0d
  certificate_body (length == 52)
    00:00:01:5d:48:26:95:79:00:00:01:5c:f0:75:69:79
    00:01:20:a6:93:66:e0:f5:b0:08:1a:0e:5b:20:80:8b
    fa:0f:48:83:67:6b:c5:92:7c:15:1a:1a:91:88:c4:28
    60:01:15:00
      valid_after: 1500154074489
      valid_before: 1498682845561
      signing_level: 0
      public_key_type: X25519
      public_key (length == 32)
        a6:93:66:e0:f5:b0:08:1a:0e:5b:20:80:8b:fa:0f:48
        83:67:6b:c5:92:7c:15:1a:1a:91:88:c4:28:60:01:15
      extensions (count = 0)
```