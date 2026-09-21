# Template Framing

## Header fields

The newer transport describes its header with marker runs. Each marker occupies one or more bytes; the run length is the field width.

| Marker | Meaning |
| --- | --- |
| `T` | Packet ID. |
| `L` | Total frame length, including the header. |
| `I` | Packet sequence index. |
| `E` | Compression selector. |

Fields are big-endian. A protocol layout may omit `I` or `E`, and the field widths are part of the active configuration.

A common six-byte layout is:

```text
[E][I][L][L][L][T][payload...]
```

Here `L` is a three-byte total length and `T` is one byte. Other layouts can use different widths, so the configured template remains authoritative.

## Frame construction

1. Build the logical payload for the semantic packet.
2. Compress it according to the active `E` policy.
3. Allocate the configured header width.
4. Write the packet ID, total frame length, sequence index, and compression selector.
5. Append the payload.
6. Encrypt the complete frame only when the negotiated outgoing stream is active.

The length includes every header byte and every payload byte. It is not the payload length alone.

### Worked six-byte header

With no compression, packet ID `6`, sequence index `7`, and a five-byte payload, a common header layout is:

```text
00 07 00 00 0b 06 [five payload bytes]
^^ ^^ ^^^^^^^ ^^
 E  I  length  T
```

The total length is `0x0b`: six header bytes plus five payload bytes. This example is illustrative; the active template decides the actual field widths.

Sequence indexes are directional. A sender and receiver maintain their own expected index, and a mismatch can be logged and resynchronized without changing the packet's payload offset.

## Compression selectors

The common selectors are:

| Value | Meaning |
| --- | --- |
| `0` | No compression. |
| `1` | ZLIB. |
| `2` | BZIP2. |

The sender's choice of compression is policy. The receiver must use the selector and reject unknown values rather than guessing.

## Handshake and login

Some sessions send a configured handshake before the first framed login. The login itself is a normal packet under the selected header layout; its packet ID and fields are generation-specific. Keep handshake bytes, login fields, and ordinary packet payloads as separate stages.

## Key exchange and stream encryption

An encrypted session can deliver an encrypted key-control packet before normal stream encryption begins. The client then:

1. decrypts the control payload with the configured private-key path when required;
2. reads the cipher type and encoded key material;
3. initializes the incoming stream cipher;
4. decrypts any bytes already buffered after the control frame;
5. continues decrypting future bytes with the same stream state.

Incoming and outgoing ciphers have independent state. A stream cipher must never be reinitialized at a frame boundary.

Only the negotiated cipher is valid. If the session requests an unsupported cipher, fail the session instead of treating encrypted bytes as plaintext.

Two control IDs are commonly reserved by the modern path: `0xfc` carries encryption setup data and `0xfd` carries a bundle of complete frames. They are transport controls, not ordinary game payloads.

## Bundles

A bundle packet contains complete framed packets as its payload. Decode it recursively:

```text
outer frame
  -> decompress outer payload
  -> identify bundle packet
  -> parse inner headers until payload is exhausted
  -> dispatch each inner packet
```

The inner packet length is authoritative. Do not split bundle payloads on newline bytes.

Bundles may contain another bundle. Recursion must be bounded by the enclosing payload length, and an inner frame that exceeds the remaining bundle bytes is a framing error.
