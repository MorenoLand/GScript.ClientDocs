# State and Resources

## Player state

The client commonly maintains one record per player ID. Incoming property packets merge fields into that record instead of replacing unrelated fields. Typical fields include:

- account and display names;
- current level;
- position, direction, and status;
- appearance and carried objects;
- chat and presence information.

Player deletion marks the record disconnected or removes it according to the wrapper's cache contract. A callback should use a snapshot so later network activity cannot mutate the data while it is being consumed.

## Level state

Level state is assembled from a sequence of packets rather than a single message. A capture may include:

- level name;
- board or layer tile data;
- links;
- NPC properties and bytecode;
- file or resource packets associated with the active level.

Start a new capture when a concrete level becomes active. Flush the previous capture at a level transition, a disconnect, or another configured boundary.

Map or world-level names that are only intermediate routing values should not replace the concrete active level. Keep the capture context stable until a real level-name transition is observed.

## Files and resources

Resource callbacks should expose the decoded bytes, logical name, resource type, and packet that delivered them. Binary resources must use raw-length framing when their contents can contain newline bytes.

Common resource categories include:

- files and level data;
- NPC bytecode;
- weapon or class script payloads;
- gani or animation data;
- board snapshots.

Dumping resources is a side effect of dispatch. It must not change packet parsing or consume bytes that a typed callback still needs.

Resource names should be normalized only for the output path. The protocol name used for matching, transfer tracking, and callbacks must remain available so two files with the same basename in different directories do not collide.

## Text and scripts

Server-text requests and replies are packet payloads, not transport control. Decode their text encoding only after the frame and packet boundaries are known.

Script payloads may use comma text or `0xa7` line-break substitution. The transport should preserve the encoded bytes; presentation or script installation can decode them later.

## Unknown data

Unknown packet IDs and partially decoded payloads should remain observable through a raw event. An unknown packet is not proof that the frame is invalid; only the frame structure, compression selector, sequence rules, and negotiated cryptography determine transport validity.

When adding a decoder for an unknown family, first record the raw payload and cursor offsets, then add typed fields one at a time. A partial decoder that silently consumes the wrong number of bytes is worse than a raw event.
