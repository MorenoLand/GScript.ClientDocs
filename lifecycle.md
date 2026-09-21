# Packet Lifecycle

## Outgoing path

```text
semantic action
  -> handler-table lookup
  -> payload encoding
  -> generation-specific frame construction
  -> compression
  -> optional stream encryption
  -> complete socket write
```

The semantic action may be a movement update, chat message, text request, resource request, or script event. The handler table supplies the wire packet ID for runtime-configured actions.

## Incoming path

```text
socket bytes
  -> persistent receive buffer
  -> optional stream decryption
  -> complete-frame extraction
  -> header validation
  -> decompression
  -> raw/bundle unwrapping
  -> packet ID and payload
  -> semantic dispatch
  -> cache/state update
  -> callbacks and script events
```

Partial headers and partial payloads remain in the receive buffer. A read boundary is never a protocol boundary.

## Handler tables

Runtime tables commonly have two directions:

- input: wire packet ID to handler index;
- output: semantic handler index to wire packet ID.

This allows the same client logic to work with different server/version layouts. A handler index is not automatically a packet ID.

Some input slots have control meanings rather than ordinary packet handlers:

| Slot value | Meaning |
| --- | --- |
| `0xfc` | Install or process input encryption. |
| `0xfd` | Announce or process a raw-payload length. |
| `0xfe` | Forward data to a script-level raw-data event. |
| `0xff` | Disabled or unavailable handler. |

These values are table conventions. Interpret them only in the active handler configuration.

## Dispatch rules

Dispatch should expose both raw and decoded forms when possible:

- raw callbacks preserve unknown or future payloads;
- typed callbacks expose stable semantic fields;
- state caches merge player, level, and resource updates;
- packet events identify known IDs that do not yet have a dedicated callback.

Malformed packets should be rejected or quarantined with their frame context. They should not advance an unrelated cipher or sequence state.

## Error boundaries

Keep these failures separate:

| Failure | Correct response |
| --- | --- |
| Incomplete frame | Keep bytes buffered and wait for more input. |
| Invalid length | Resynchronize only if the active generation defines a safe recovery rule; otherwise fail the session. |
| Unknown compression | Fail or quarantine the frame without advancing crypto state. |
| Unknown packet ID | Emit raw/unhandled data if framing is valid. |
| Bad payload field | Reject the packet payload, not the preceding frame. |
| Callback error | Report the callback failure without rewriting the receive buffer. |

## Authentication and disconnects

The authentication callback is emitted once when the session first receives its authentication signal. A disconnect packet flushes pending captures, records the reason, stops normal dispatch, and closes the transport.

Authentication must not be inferred merely because a socket is readable. Likewise, a signature packet does not authorize packet families that the active handler configuration has disabled.
