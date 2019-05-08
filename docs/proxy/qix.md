# Quantum Information Exchange (QIX)

In QKD mode, the proxy receives quantum keys via the serial port in a binary format called QIX.

The frame consists of the following fields:

`[sync][key id][key data][status][crc]`

* **`sync`** is the 2-byte constant {0x5A, 0xA5}
* **`key id`** is an unsigned 64-bit key identifier that increments
* **`key data`** is the 256-bit random key
* **`status`** is a one byte value where:
    * `0x01` - `OK`: key is safe to use
	* `0xFF` - `Compromised`: key should not be used
	* All other values are undefined and should be consider compromised
* **`crc`** is the 32-bit Castagnoli CRC used in the SSP21 standard, calculated over the key id, key data, and status fields

## QIX Emulator

The project includes a command line application called `qix-emulator` that can read and write frames via a serial port.

```
> qix-emulator --help

    -h, --help
        shows this help message
    -r, --read
        read QIX frames
    -w, --write
        read QIX frames
    -p, --port
        serial port
    -t, --rate
        number of keys per second (write only) - defaults to 1
    -c, --count
        number of frames to transmit (write only) - defaults to 2^64 -1
```

For testing purposes, you can use `socat` to create a virtual null-modem serial port pair:

```
> socat -d -d pty,raw,echo=0 pty,raw,echo=0
2019/05/08 13:02:31 socat[50869] N PTY is /dev/pts/1
2019/05/08 13:02:31 socat[50869] N PTY is /dev/pts/2
2019/05/08 13:02:31 socat[50869] N starting data transfer loop with FDs [5,5] and [7,7]
```

You can then start writing and reading on each end of the null-modem pair:

```
> ./cpp/exe/qix-emulator/qix-emulator -w -p /dev/pts/1
wrote: 0 - ok - 7F 24 C9 E1 91 50 1B 27 24 74 BD 0E ED F9 2D 2F 2B 7A 70 8D 7F DB CB 1D ED 5E 72 51 00 05 EA 0C
wrote: 1 - ok - FF 0E 81 E5 C1 A3 52 CD 43 9A E9 DF 4B 50 CC BB 98 70 7C DD A5 41 24 BB 38 B6 F6 6A 2B B2 45 E3
wrote: 2 - ok - E2 A4 53 E5 9E 88 59 BB 33 25 E8 35 71 16 9A 0D E4 C9 BE EA B1 11 52 A1 15 1A 09 67 D9 C5 DD 6D
wrote: 3 - ok - 96 D8 D8 C0 14 29 9D 00 3E 45 7B 73 0A 84 75 50 DF 94 3A DF A9 8E 24 24 98 87 9F 41 98 E8 93 48
wrote: 4 - ok - 70 0A D4 86 E0 29 93 CC 04 4D 68 A0 89 84 0C F5 EA BB 97 BD 84 0C 9F BC 68 DD 75 C2 76 B9 ED F4
```

```
> ./cpp/exe/qix-emulator/qix-emulator -r -p /dev/pts/2
read: 0 - ok - 7F 24 C9 E1 91 50 1B 27 24 74 BD 0E ED F9 2D 2F 2B 7A 70 8D 7F DB CB 1D ED 5E 72 51 00 05 EA 0C
read: 1 - ok - FF 0E 81 E5 C1 A3 52 CD 43 9A E9 DF 4B 50 CC BB 98 70 7C DD A5 41 24 BB 38 B6 F6 6A 2B B2 45 E3
read: 2 - ok - E2 A4 53 E5 9E 88 59 BB 33 25 E8 35 71 16 9A 0D E4 C9 BE EA B1 11 52 A1 15 1A 09 67 D9 C5 DD 6D
read: 3 - ok - 96 D8 D8 C0 14 29 9D 00 3E 45 7B 73 0A 84 75 50 DF 94 3A DF A9 8E 24 24 98 87 9F 41 98 E8 93 48
read: 4 - ok - 70 0A D4 86 E0 29 93 CC 04 4D 68 A0 89 84 0C F5 EA BB 97 BD 84 0C 9F BC 68 DD 75 C2 76 B9 ED F4
```

If you want to write the same key data to two ports to test a proxy link without QKD hardware, just supply two port arguments:

```
> ./qix-emulator -w -p /dev/pts/1 -p /dev/pts/2
```