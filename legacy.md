# Legacy Framing

## Outer frame

The normal outer frame is:

```text
[2-byte big-endian length][frame body]
```

If the two-byte length is `0xffff`, the frame uses an extended header:

```text
[0xffff][4-byte big-endian length][frame body]
```

The length counts the frame body, not the length prefix.

## Login exception

The first login frame is a zlib-compressed payload with a two-byte length prefix. It does not carry the later per-frame compression selector. Login fields are generated from the selected version configuration and client identity.

Do not parse the login frame as an ordinary packet with a packet ID and newline terminator.

## Ordinary packet body

After login, the logical body is normally:

```text
[packet ID + 0x20][optional first-packet key + 0x20][payload][0x0a]
```

The optional key byte is part of the first outgoing packet for the classic path. It is not repeated in every packet.

### Worked logical packet

`PLI_TOALL (6)` carrying `Hi` is represented before compression as:

```text
26 22 48 69 0a
^^ ^^ ^^^^^^^ ^^
ID len  H i   LF
```

`0x26` is `6 + 0x20`; `0x22` is the two-byte message length encoded as a GByte.

## Compression modes

The frame body carries a compression selector after the outer length:

| Type | Meaning |
| --- | --- |
| `0x02` | Uncompressed logical bytes. |
| `0x04` | ZLIB-compressed logical bytes. |
| `0x06` | BZIP2-compressed logical bytes. |

Selection thresholds are implementation policy, not a wire invariant. Observed clients use different boundaries for switching to ZLIB and BZIP2. A decoder must follow the selector; it must not infer the selector from payload size.

Two common sender policies are a smaller policy that switches near 48 bytes and 4096 bytes, and a larger policy that keeps raw payloads through 55 bytes and switches to BZIP2 above 8192 bytes. These are sender choices, not additional compression types.

For the short illustrative packet above, an unencrypted raw frame body is:

```text
00 06 02 26 22 48 69 0a
^^^^^^ ^^ ^^^^^^^^^^^^^^
length mode logical body
```

The length is six because it counts the mode byte plus the five logical bytes. A real first outgoing packet may also contain the protocol's one-time key byte, and encrypted/compressed frames replace the visible logical bytes with the transformed bytes.

## Rolling scrambling

Scrambling is applied to the compressed bytes before they are placed in the frame. Each four-byte block advances a rolling state:

```text
state = state * 0x08088405 + seed
```

The state is maintained independently for incoming and outgoing data. Uncompressed data uses up to twelve blocks; compressed data uses up to four blocks. The final partial block is scrambled only for its available bytes.

The initial state and seed are session parameters. Reset them when a connection is replaced, not when an individual frame is received.

If a frame fails to decompress after scrambling, restore the stream state from before that frame before attempting resynchronization. Advancing the LCG on a failed parse will corrupt every subsequent frame.

## Raw packets

Packets that may contain arbitrary binary bytes use a raw-length mechanism. The wrapper carries a length in a compact integer, an end marker, and the complete inner packet. The inner packet's terminator belongs to the wrapper and must be removed exactly once before dispatch.

Raw packets can arrive inside an ordinary decoded stream or as the pending body of a raw-data control packet. The parser must retain the expected raw length until the complete body has arrived; newline scanning is invalid while that body is pending.

## Receive algorithm

1. Read the outer length prefix.
2. Read the complete frame body.
3. Identify the compression selector or the first-login exception.
4. Undo scrambling with the incoming stream state.
5. Decompress if required.
6. Split ordinary newline-terminated packets or unwrap raw packets.
7. Convert the encoded packet ID back to its numeric value.
8. Dispatch the payload without reinterpreting its bytes as a second frame.
