# Payload Layouts

The frame decoder produces a packet ID and a payload byte range. Payload parsing begins only after that boundary is established.

## Player properties

Property packets are sequences of:

```text
[property id][property value]
```

The value type belongs to the property ID. Common value forms include:

- offset bytes for counters, flags, directions, and appearance slots;
- `GShort` for player IDs and high-precision values;
- `GInt3` for compact NPC IDs;
- `GInt5` for timestamps, sizes, and checksums;
- length-prefixed strings for names, levels, and text;
- fixed groups such as the five player color bytes.

The parser must switch on the property ID before consuming the value. Reading every property as a string will shift the cursor and make all later properties appear corrupt.

## Player joins and leaves

A player-join payload commonly contains:

```text
[player id: GShort]
[account: length-prefixed text]
[initial property block]
```

Merge the initial fields into the player cache. A leave payload contains the player ID and should mark that cache entry disconnected before invoking the callback.

## Movement and warps

A basic player warp carries two half-tile coordinates followed by a level name. An extended warp adds Z and map coordinates. The coordinate bytes are decoded as doubled tile values, so `wire - 0x20`, divided by two, is the ordinary tile position.

Movement packets and warp packets are related but not interchangeable: movement updates may include direction and high-precision fields, while a warp changes the active level context.

## Chat and text

The common chat families have different payload shapes:

| Family | Payload shape |
| --- | --- |
| To-all | Sender ID, message length, message bytes. |
| Private message | Sender or target ID followed by message metadata and text. |
| Client chat | Raw or length-prefixed text according to the active handler. |
| Request text | Encoded command fields. |
| Send text | Encoded update fields. |

Text commands often use comma text internally. Decode the field structure before interpreting command names or values.

## File packets

A complete file payload commonly has this shape:

```text
[modification time: GInt5]
[filename: length-prefixed text]
[file bytes...]
```

The file bytes are not text. Preserve them exactly, including zero bytes and bytes equal to the legacy newline terminator.

Large transfers use start, size/raw-data, chunk, and end stages. Track the transfer by the requested logical path, not only by the basename in a start/end marker. A raw-data wrapper's inner terminator is framing and must not become part of the file.

## Board and level packets

Level capture is assembled from related packets:

1. the level name establishes the capture context;
2. board and layer packets provide tile data;
3. link packets provide exits and connections;
4. NPC property and bytecode packets add entities;
5. a transition or disconnect flushes the assembled snapshot.

Board payloads can be binary tile arrays. Decode them into tiles for semantic callbacks while retaining the original bytes for exact resource capture.

## NPC and script payloads

NPC property payloads begin with a compact NPC ID and then property records. Script-bearing packets may carry:

- comma text;
- `0xa7`-separated script text;
- raw bytecode with a separate length or wrapper.

The packet ID selects the script family. Do not apply weapon line-break decoding to comma text, and do not treat bytecode as display text.

## Raw and bundled payloads

Raw and bundled payloads are transport containers. Decode the container first, then pass the resulting packet bytes back through the ordinary packet dispatcher. Never dispatch the container itself as if it were the contained game packet.
