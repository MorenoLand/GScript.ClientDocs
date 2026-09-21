# Payload Breakdowns

These layouts describe the decoded packet payload after the outer frame, compression, encryption, raw wrapper, and bundle layers have been removed. A runtime handler table can select a different wire ID for the same semantic operation.

## Reading a packet from bytes

Every worked example follows the same order:

1. decode the outer frame;
2. decode the packet ID;
3. create a cursor at payload byte zero;
4. read fields in the documented order;
5. verify that the cursor ends at the payload boundary.

Never search for a field by text or newline when its preceding length or numeric width already defines the boundary.

## Server-to-client payloads

### Authentication and connection

| Packet | Payload |
| --- | --- |
| `PLO_SIGNATURE (25)` | One signature value, normally decoded as an offset byte. Its arrival is the authentication signal. |
| `PLO_DISCMESSAGE (16)` | Remaining bytes as the disconnect reason. |
| `PLO_SERVERLISTCONNECTED (190)` | Connection-state notification; normally no application payload. |

### Level and world state

| Packet | Payload |
| --- | --- |
| `PLO_LEVELNAME (6)` | Raw level or map name. |
| `PLO_LEVELBOARD (0)` | Tile payload for the active board. The payload remains binary until decoded as tiles. |
| `PLO_LEVELLINK (1)` | Link text or link record for the active level. |
| `PLO_BOARDLAYER (107)` | Tile payload for an additional board layer. |
| `PLO_BOARDPACKET (101)` | Board data using the board-specific payload format. |
| `PLO_LEVELCHEST (4)` | Chest record with the level-specific chest fields. |
| `PLO_NEWWORLDTIME (42)` | Encoded world-time value. |

### Players

`PLO_ADDPLAYER (55)` begins with:

```text
[player id: GShort]
[account: GString]
[initial property records...]
```

For player ID `5` and account `Bob`, the fixed prefix is:

```text
57 20 25 23 42 6f 62
^^ ^^ ^^ ^^ ^^^^^^^^^^
ID id id len  B  o  b
```

`0x57` is `55 + 0x20`; `20 25` is `GShort(5)`; `0x23` is the three-byte account length. Property records follow immediately.

`PLO_DELPLAYER (56)` contains only the player ID:

```text
[player id: GShort]
```

`PLO_OTHERPLPROPS (8)` and `PLO_PLAYERPROPS (9)` carry property records. The former begins with a player ID; the latter is the local-player property block.

`PLO_PLAYERWARP (14)` uses:

```text
[x: GByte, tile * 2]
[y: GByte, tile * 2]
[level: raw text]
```

`PLO_PLAYERWARP2 (49)` extends that with Z and map coordinates:

```text
[x: GByte][y: GByte][z: GByte][map x: GByte][map y: GByte][level: raw text]
```

### Chat and text

`PLO_TOALL (13)` is:

```text
[sender id: GShort]
[message length: GByte]
[message bytes]
```

For sender ID `5` and message `Hi`, the logical bytes are:

```text
2d 20 25 22 48 69 0a
^^ ^^ ^^ ^^ ^^^^^ ^^
ID id id len  H i  LF
```

`0x2d` is `13 + 0x20`; `20 25` is `GShort(5)`; `0x22` is `GByte(2)`.

`PLO_PRIVATEMESSAGE (37)` begins with a sender or target ID and leaves the remaining message fields to the active message subtype. Preserve the remainder before applying comma-text or message-type decoding.

`PLO_RC_CHAT (74)` carries the client-control chat text as raw payload bytes.

`PLO_SERVERTEXT (82)` carries a comma-text response. Decode the fields first, then interpret the command family and response fields.

## Server warp and server text

### Server warp

`PLI_SERVERWARP (41)` sends the requested server destination as the raw remainder of the payload:

```text
[destination server name or address: raw text]
```

`PLO_SERVERWARP (178)` reports the destination selected by the server:

```text
[server name or address: raw text]
```

These are not level warps. A level warp changes the active world inside the current server; a server warp changes the server target and normally begins a new connection sequence.

### Request text

`PLI_REQUESTTEXT (152)` uses three comma-text fields:

```text
[type],[key],[default]
```

Example request:

```text
"server","weather","unknown"
```

The commas separate fields only outside quotes. A value containing a comma must be quoted and an embedded quote must be doubled.

### Send text

`PLI_SENDTEXT (154)` uses three comma-text metadata fields, a newline, and the value:

```text
[type],[key],[metadata]\n[value]
```

Example:

```text
"server","motd","replace"
Welcome to the world
```

`PLO_SERVERTEXT (82)` returns the server's text response. Preserve the complete response before splitting it into fields because command families can append additional lines.

### Files and raw data

`PLO_FILE (102)` is:

```text
[modification time: GInt5]
[filename: GString]
[file bytes...]
```

For a zero timestamp, filename `a.txt`, and content `OK`, the logical body begins:

```text
86 20 20 20 20 20 25 61 2e 74 78 74 4f 4b
^^ ^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^
ID     GInt5(0)       GString("a.txt") + data
```

The example omits any outer frame and compression bytes. `0x86` is `102 + 0x20`.

`PLO_RAWDATA (100)` wraps an inner packet:

```text
[inner length: GInt3]
[0x0a wrapper terminator]
[inner packet bytes]
```

The inner packet can contain `0x0a`; its length, not newline scanning, defines its boundary. Remove only the terminator that belongs to the wrapper or decoded logical packet, never arbitrary trailing file bytes.

Large-transfer control packets are:

| Packet | Payload |
| --- | --- |
| `PLO_LARGEFILESTART (68)` | Transfer name, commonly the basename. |
| `PLO_LARGEFILESIZE (84)` | Total size as `GInt5`. |
| `PLO_LARGEFILEEND (69)` | Transfer name or completion marker. |
| `PLO_FILESENDFAILED (30)` | Failure text or filename. |

### NPC and script data

NPC property families commonly begin with a compact NPC ID and then typed property records. Script-bearing families can carry comma text, `0xa7`-separated script text, or binary bytecode. The packet ID selects the decoder; do not use a text decoder for bytecode.

`PLO_NPCBYTECODE (131)` carries a bytecode-bearing NPC record. Treat its internal header and length fields as part of the bytecode family, not as ordinary GStrings.

## Client-to-server payloads

## Board requests

`PLI_REQUESTUPDATEBOARD (130)` requests a rectangular board region:

```text
[level length: GByte]
[level bytes]
[modification time: GInt5]
[x: GShort][y: GShort]
[width: GShort][height: GShort]
```

The rectangle fields are part of the request, not part of the returned board tile stream. A board response is binary tile data and must be retained separately from its dimensions.

### Chat and text

`PLI_TOALL (6)` uses a length-prefixed message:

```text
[message length: GByte]
[message bytes]
```

For `Hi`, the logical bytes are `26 22 48 69 0a`: packet ID `6 + 0x20`, length `2 + 0x20`, two ASCII bytes, and the logical terminator.

`PLI_PRIVATEMESSAGE (28)` commonly uses:

```text
[recipient count: GShort]
[recipient id: GShort] repeated
[message text]
```

`PLI_REQUESTTEXT (152)` and `PLI_SENDTEXT (154)` carry comma-text command fields. Keep the command namespace, option, and values as separate decoded fields instead of concatenating them before parsing.

### Player and movement

`PLI_PLAYERPROPS (2)` carries a property update for the local client. The property block is a sequence of property ID and type-specific value pairs.

Movement and warp actions can be runtime-mapped. When the active handler selects a base movement packet, encode coordinates using the field type defined by that handler and preserve raw level text after the fixed fields.

`PLI_TRIGGERACTION (38)` carries position, action name, and action parameters. Action parameters can contain commas and must not be split before the packet's text encoding is decoded.

### Files and wrappers

`PLI_RAWDATA (50)` is the client-to-server counterpart of the raw-data wrapper. It carries an inner byte length, wrapper terminator, and inner packet. File update and upload families use this wrapper when arbitrary file bytes would make newline framing unsafe.

`PLI_WANTFILE (23)` requests a resource by name. Update, upload, delete, and rename families use filename or path fields whose exact length encoding belongs to the selected handler table.

The ordinary file-browser operations are:

| Packet | Payload |
| --- | --- |
| `PLI_RC_FILEBROWSER_START (89)` | Empty request to open the browser. |
| `PLI_RC_FILEBROWSER_CD (90)` | Raw directory path. |
| `PLI_RC_FILEBROWSER_END (91)` | Empty request to close the browser. |
| `PLI_RC_FILEBROWSER_DOWN (92)` | Raw file path to download. |
| `PLI_RC_FILEBROWSER_UP (93)` | Length-prefixed filename followed by raw chunk bytes. |
| `PLI_RC_FILEBROWSER_MOVE (96)` | Length-prefixed source pattern followed by raw destination directory. |
| `PLI_RC_FILEBROWSER_DELETE (97)` | File name/path, commonly basename-only for a file deletion. |
| `PLI_RC_FILEBROWSER_RENAME (98)` | Length-prefixed destination basename followed by source basename. |
| `PLI_RC_LARGEFILESTART (155)` | Upload transfer name. |
| `PLI_RC_LARGEFILEEND (156)` | Upload completion name. |
| `PLO_RC_FILEBROWSER_DIRLIST (65)` | Comma-text folder rules. |
| `PLO_RC_FILEBROWSER_DIR (66)` | Folder listing or embedded transfer content. |
| `PLO_FILE (102)` | Complete file or chunk payload. |

The file-browser directory response is context-sensitive. It can contain folder entries, compressed embedded transfer data, or a raw-data wrapper. The active request state determines which decoder is valid.

## Player, account, and administration packets

### Player control

| Packet | Payload |
| --- | --- |
| `PLI_RC_DISCONNECTPLAYER (61)` | Player ID `GShort` followed by a raw reason. |
| `PLI_RC_ADMINMESSAGE (63)` | Raw message bytes. |
| `PLI_RC_PRIVADMINMESSAGE (64)` | Target player ID `GShort` followed by raw message bytes. |
| `PLI_RC_PLAYERPROPSGET2 (73)` | Player ID `GShort`. |
| `PLI_RC_PLAYERPROPSGET3 (74)` | Account `GString` followed by the empty selector byte. |
| `PLI_RC_PLAYERPROPSRESET (75)` | Account name as the remaining raw text. |
| `PLI_RC_PLAYERPROPSSET2 (76)` | Account/world strings, property block, flags, chests, and weapons. |
| `PLO_RC_ADMINMESSAGE (35)` | Raw administrator message. |
| `PLO_RC_PLAYERPROPSGET (72)` | Player ID, account/world strings, property block, flags, chests, and weapons. |

### Account and profile control

| Packet | Payload |
| --- | --- |
| `PLI_RC_ACCOUNTADD (70)` | Account, password, email, banned flag, guest/load-only flag, and administrative fields. |
| `PLI_RC_ACCOUNTDEL (71)` | Account name. |
| `PLI_RC_ACCOUNTLISTGET (72)` | Name pattern and conditions as length-prefixed strings. |
| `PLI_RC_ACCOUNTGET (77)` | Account name. |
| `PLI_RC_ACCOUNTSET (78)` | Account fields, flags, world, and ban reason. |
| `PLI_PROFILEGET (80)` | Account name. |
| `PLI_PROFILESET (81)` | Account `GString` followed by ordered profile fields. |
| `PLO_RC_ACCOUNTDEL (53)` | Deleted account name. |
| `PLO_RC_ACCOUNTLISTGET (70)` | Repeated length-prefixed account names until payload exhaustion. |
| `PLO_RC_ACCOUNTGET (73)` | Account fields and ban fields. |
| `PLO_PROFILE (75)` | Ordered profile strings. |

### Rights, comments, and bans

| Packet | Payload |
| --- | --- |
| `PLI_RC_PLAYERRIGHTSGET (83)` | Account name. |
| `PLI_RC_PLAYERRIGHTSSET (84)` | Account `GString`, rights `GInt5`, IP range `GString`, folder-access length, folder-access comma text. |
| `PLI_RC_PLAYERCOMMENTSGET (85)` | Account name. |
| `PLI_RC_PLAYERCOMMENTSSET (86)` | Account `GString` and comment comma text. |
| `PLI_RC_PLAYERBANGET (87)` | Account name. |
| `PLI_RC_PLAYERBANSET (88)` | Account `GString`, banned byte, and raw reason remainder. |
| `PLO_RC_PLAYERRIGHTSGET (62)` | Account, rights `GInt5`, IP range, folder-access length, folder rules. |
| `PLO_RC_PLAYERCOMMENTSGET (63)` | Account `GString` followed by the comment remainder. |
| `PLO_RC_PLAYERBANGET (64)` | Account `GString`, banned byte, and raw reason remainder. |

## Server settings

| Packet | Payload |
| --- | --- |
| `PLI_RC_SERVEROPTIONSGET (51)` | Empty request. |
| `PLI_RC_SERVEROPTIONSSET (52)` | Options comma text. |
| `PLI_RC_FOLDERCONFIGGET (53)` | Empty request. |
| `PLI_RC_FOLDERCONFIGSET (54)` | Folder configuration comma text. |
| `PLI_RC_SERVERFLAGSGET (68)` | Empty request. |
| `PLI_RC_SERVERFLAGSSET (69)` | Flag count and flag strings. |
| `PLO_RC_SERVEROPTIONSGET (76)` | Options comma text. |
| `PLO_RC_FOLDERCONFIGGET (77)` | Folder configuration comma text. |
| `PLO_RC_SERVERFLAGSGET (61)` | Flag count and flag strings. |

## NPC-server discovery

`PLI_NPCSERVERQUERY (94)` addresses the special NPC-server player:

```text
[NPC-server player ID: GShort]
location
```

`PLO_NPCSERVERADDR (79)` returns:

```text
[server identifier: GShort]
[host,port: raw text]
```

The address is connection metadata, not an NC payload. Parse the host and port only after the surrounding packet has been decoded.

### Property records

The common property record shape is:

```text
[property id: GByte][typed value]
```

Representative property types are:

| Property family | Type |
| --- | --- |
| Hearts, counters, direction, status | Offset byte. |
| Player ID, high-precision coordinates | `GShort`. |
| NPC IDs and compact object IDs | `GInt3`. |
| Timestamps, checksums, file sizes | `GInt5`. |
| Names, levels, text fields | `GString` or raw remainder. |
| Player colors | Five consecutive offset bytes. |

The property ID must be decoded before the value width is selected. A malformed property should stop that property block, not consume the next packet.

## Text encoding checklist

1. Remove the transport wrapper.
2. Identify the packet family.
3. Read fixed-width numeric fields in order.
4. Decode length-prefixed strings with the active generation's long-value rule.
5. Decode comma text only for fields that use it.
6. Preserve binary remainder fields without character conversion.
