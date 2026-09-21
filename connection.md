# Connection Lifecycle

## Configuration phase

Before opening the game socket, the client needs:

- server name, host, and port;
- protocol generation or a field-template description;
- input and output handler tables;
- optional handshake text;
- optional input/output encryption configuration;
- client identity fields used by the login payload.

These values are runtime configuration. A client should not assume that one server, version, or handler table applies to every session.

## Connector-provided behavior

The connector phase can provide more than an endpoint. A complete session description may include:

- platform and version identity fields;
- a protocol name or a field-template string;
- a pre-login handshake;
- input packet-to-handler pairs;
- output handler-to-packet mappings;
- the packet used to install input encryption;
- the packet used to announce raw-payload lengths;
- an optional private-key or parse-key path.

The game transport must apply this description before interpreting ordinary packets. The same semantic action can therefore use different wire IDs or frame layouts in different sessions.

## State transitions

| State | Meaning |
| --- | --- |
| Idle | No socket or receive loop is active. |
| Connecting | A TCP connection is being established. |
| Connected | The socket is usable, but authentication has not completed. |
| Authenticated | The server has emitted its authentication signal. |
| Failed | The connection stopped because framing, crypto, I/O, or protocol validation failed. |
| Closed | The socket and worker loops have been shut down. |

`Connected` and `Authenticated` are separate states. A client may receive transport data before it is allowed to treat the session as logged in.

## Connect and login

1. Resolve the configured endpoint.
2. Open the TCP connection.
3. Apply any configured pre-login handshake.
4. Build the generation-specific login frame.
5. Send the frame through the same write path used for ordinary packets.
6. Decode incoming frames until the authentication signal arrives.
7. Begin normal packet dispatch and state capture.

The exact login fields are configuration- and generation-dependent. The stable rule is to use the active protocol builder and client identity, not to construct a guessed login string in a wrapper.

## Server events

Authentication is normally represented by a signature packet. A separate server-list-connected event may enable list, messaging, or server-text behavior after login. These events are semantic state changes; their handler-table indices are not universal wire IDs.

The server-list-connected event is not the same as TCP connection or authentication. A client can be connected and authenticated while still waiting for the server-list state that enables the next group of script or messaging events.

## Read and write loops

The write path serializes frame construction and sends all bytes before releasing its send lock. The read path:

1. appends received bytes to a persistent buffer;
2. decrypts the stream when the negotiated cipher is active;
3. extracts complete frames without discarding partial input;
4. decodes compression and packet headers;
5. queues packets for the main dispatch step.

The main loop drains queued packets, runs handlers, updates state, and emits callbacks. Keeping socket I/O separate from packet dispatch prevents a callback from corrupting framing state.

The main loop should also drain disconnect events and preserve packet order. A callback that starts a reconnect must not reuse the old receive buffer, sequence counter, or cipher stream.

## Disconnect and reconnect

On disconnect, stop accepting new writes, close the socket, flush any pending resource capture, report the reason, and clear generation-specific stream state before reconnecting. Reconnect must start with fresh frame buffers, sequence counters, and cipher state.
