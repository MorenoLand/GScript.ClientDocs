# Troubleshooting

## The first frame cannot be decoded

Check whether the session is in the login exception or ordinary legacy framing. The first legacy login payload is zlib-compressed without the later mode byte. A template-framed session must use its configured header layout from the first frame.

## Packet lengths drift

Verify all of the following:

- the length includes the correct header bytes;
- extended legacy lengths are handled after `0xffff`;
- a read loop preserves incomplete frames;
- a raw wrapper's inner length is not confused with the outer length;
- a bundle is parsed as a sequence of complete headers.

For a legacy stream, also check whether the parser accidentally consumed the first packet's embedded key byte as application data. For a template stream, check the configured header width before reading packet ID or compression.

## Decompression fails

Use the frame's compression selector. Do not guess ZLIB from a payload that may be BZIP2, and do not decompress the first login frame as if it carried an ordinary selector.

If only large packets fail, compare the sender's compression threshold and BZIP2/ZLIB choice. Thresholds are policy; the selector is the authority.

## Encrypted bytes look like packet IDs

The likely causes are a stale cipher state, the wrong direction's stream, a missing key-control step, or decryption applied after packet parsing. Reset crypto only when the session resets.

## Sequence warnings appear

Use the configured index width and initial value. A sequence mismatch can be recoverable, but silently accepting an index with the wrong width will shift every later header interpretation.

Verify whether the session starts outgoing indexes at one and incoming indexes at zero, or uses another configured initial value. Never reset the index after a bundle member.

## Login or server-list fields are unclear

Do not invent a universal field order. Use the active connector configuration or a captured frame. Keep the guide's transport rules separate from server-specific login and list records.

## A packet is unknown

Retain the numeric ID and raw payload, emit an unhandled event, and continue if the frame is structurally valid. Unknown payload semantics are different from invalid transport.

Compare the numeric ID against the active input table before comparing it with a static enum. Runtime tables can route the same semantic handler through different IDs.

## Disconnects occur after a successful connect

Connection only proves that TCP is open. Check handshake order, login framing, identity fields, negotiated protocol layout, authentication state, and whether a required encryption setup packet was handled before ordinary traffic.
