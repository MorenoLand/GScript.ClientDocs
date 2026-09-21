# Client Protocol Guide

This guide explains the client protocol from the wire outward: how a session is configured, how frames are built and decoded, how packet payloads are encoded, and how decoded packets become client state.

## Documentation Files

### [Connection Lifecycle](#/connection)

Explains configuration, TCP connection, handshake, login, authentication, reconnect, and disconnect behavior.

### [Encodings](#/encoding)

Explains offset bytes, GShort, GInt3, GInt5, GStrings, coordinates, comma text, and script line-break encoding with worked examples.

### [Legacy Framing](#/legacy)

Explains the two-byte and extended length headers, login exception, packet terminators, compression modes, rolling scrambling, and raw-data wrappers.

### [Template Framing](#/modern)

Explains configurable header fields, sequence indexes, compression selectors, key exchange, stream encryption, and nested bundles.

### [Payload Breakdowns](#/packet-breakdowns)

Explains server warp, request/send text, player and movement packets, files, raw transfers, accounts, administration, server settings, NPC-server discovery, and script/resource payloads.

### [Complete Packet IDs](#/packet-reference)

Lists the full numeric PLI, PLO, SVO, and SVI packet vocabulary.

### [Property Fields](#/properties)

Lists typed player/NPC property fields and shows how to decode a property block without losing cursor alignment.

### [Packet Lifecycle](#/lifecycle) and [State and Resources](#/state)

Explain handler tables, raw/typed dispatch, player and level caches, resource capture, and error boundaries.

## Session model

A client session has two layers:

- a configuration layer that supplies the endpoint, protocol layout, handler tables, and optional cryptography;
- a transport layer that connects, writes frames, reads frames, decodes packets, and reports state changes.

The wire protocol has two framing families. They share packet payload conventions, but their outer headers, compression selectors, sequence handling, and encryption setup differ.

## How to Use This Documentation

Start with **Connection Lifecycle**, then read **Encodings** and the framing page that matches the active session. Use **Complete Packet IDs** to find a numeric packet and **Payload Breakdowns** to understand its fields. Use **State and Resources** when implementing caches or callbacks.

Every worked example separates the outer frame from the decoded logical packet. The examples show the cursor order explicitly so the next field is never guessed from a string search.

## Common Concepts

- **Frame first:** Read the active outer framing before interpreting packet IDs.
- **Packet second:** Decode the packet ID and isolate its payload range.
- **Typed fields:** Select each field decoder from the packet/property ID before consuming bytes.
- **Stateful crypto:** Keep incoming and outgoing stream state separate and continuous.
- **Runtime mappings:** Handler slots and wire packet IDs are different namespaces.
- **Raw preservation:** Keep raw payloads for unknown or partially understood packets.

## Reading Rules

- Treat a packet ID as a wire value only after the active protocol has decoded its framing.
- Keep input and output cipher state separate and advance each stream continuously.
- Treat handler slots as semantic mappings, not as universal packet IDs.
- Preserve raw payloads when a packet family is not fully understood.
- Do not infer login fields, server-list records, or encrypted-session parameters when the active configuration or a capture does not establish them.

## Scope

The guide covers transport and client-observable packet behavior. UI behavior, script language semantics, server implementation details, and package-container internals are outside its scope unless they are required to interpret a wire packet.

## Troubleshooting

Start with [Troubleshooting](troubleshooting.md) when a frame length drifts, decompression fails, encrypted bytes look like packet IDs, a sequence index jumps, or a packet is unknown. Preserve the raw frame and active configuration before changing a decoder.
