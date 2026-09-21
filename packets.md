# Packet Reference

Packet IDs below are the common semantic IDs used by the client protocol. Runtime handler tables may map semantic actions to different wire IDs for a particular session.

For the exhaustive numeric vocabulary, see [Complete Packet IDs](packet-reference.md). For field-by-field payload layouts, see [Payload Breakdowns](packet-breakdowns.md).

## Common server-to-client packets

| ID | Meaning | Typical payload |
| ---: | --- | --- |
| `0` | Level board | Tile data. |
| `1` | Level link | Link text. |
| `6` | Level name | Raw level name. |
| `8` | Other-player properties | Player ID plus property block. |
| `9` | Local-player properties | Property block. |
| `13` | To-all message | Player ID, text length, message. |
| `14` | Player warp | X, Y, and level. |
| `16` | Disconnect message | Reason text. |
| `25` | Authentication signature | Signature payload. |
| `30` | File transfer failure | Failure text or filename. |
| `37` | Private message | Sender ID and message data. |
| `42` | World time | Encoded time value. |
| `55` | Player joined | Player ID, account, and initial properties. |
| `56` | Player left | Player ID. |
| `68` | Large transfer start | Transfer name. |
| `69` | Large transfer end | Transfer name. |
| `74` | Client chat | Chat text. |
| `79` | Auxiliary server address | Address data. |
| `82` | Server text | Encoded text response. |
| `100` | Raw-data wrapper | Encoded inner length and packet bytes. |
| `102` | File data | Timestamp, filename, and content. |
| `131` | NPC bytecode | NPC ID and bytecode. |
| `140` | NPC weapon script | Script payload. |
| `190` | Server-list connected | No ordinary payload. |

## Common client-to-server packets

| ID | Meaning | Typical payload |
| ---: | --- | --- |
| `0` | Level warp | X, Y, and destination level. |
| `2` | Player properties | Property update. |
| `6` | To-all message | Length-prefixed message. |
| `23` | Want file | Resource name. |
| `28` | Private message | Recipient IDs and message. |
| `34` | Update file | File update payload. |
| `38` | Trigger action | Coordinates, action, and parameters. |
| `47` | Verify file | Checksum and filename. |
| `50` | Raw-data wrapper | Inner length and packet bytes. |
| `79` | Chat | Chat payload. |
| `80` | Profile request | Account or profile selector. |
| `82` | Player warp | Player ID, coordinates, and level. |
| `152` | Request text | Encoded text command. |
| `154` | Send text | Encoded text update. |
| `155` | Large transfer start | Transfer name. |
| `156` | Large transfer end | Transfer name. |
| `158` | Request script | Script selector. |
| `161` | Request class update | Modification time and class name. |

## Payload rules

- The outer frame determines where a packet ends.
- The packet payload begins after the decoded packet ID.
- G-type fields must be read in the order defined by the packet family.
- A raw wrapper's inner terminator belongs to the wrapper and must not be delivered as file content.
- Bundle members are framed packets, not newline-delimited text.

## Unknown and versioned packets

Keep an enum name, numeric ID, raw payload, and active protocol generation together in diagnostics. A packet table without the active handler configuration is not enough to reconstruct semantic behavior.
