# Encodings

## Offset bytes

A protocol byte stores a small value as `value + 0x20`. Decode it by subtracting `0x20`.

This convention is used for packet IDs, character-sized values, and the individual components of larger protocol integers. Values outside the supported range must not be silently wrapped.

### Worked example: GByte

The number `5` is transmitted as `5 + 0x20 = 0x25`:

```text
value:  05
wire:   25
decode: 25 - 20 = 05
```

## GShort

`GShort` stores a value in two seven-bit components:

```text
high = (value >> 7) & 0x7f
low  = value & 0x7f
wire = [high + 0x20, low + 0x20]
```

Decode with:

```text
value = ((wire[0] - 0x20) << 7) | (wire[1] - 0x20)
```

### Worked example: GShort

For the value `1000`:

```text
high = 1000 >> 7 = 7
low  = 1000 & 0x7f = 104
wire = [0x27, 0x88]
```

## GInt3 and GInt5

`GInt3` uses three seven-bit components. It is common for compact IDs and 24-bit-style fields.

`GInt5` uses five seven-bit components and is used for timestamps, checksums, sizes, and other larger values.

```text
gint3 = [(value >> 14) & 0x7f, (value >> 7) & 0x7f, value & 0x7f]
gint5 = [(value >> 28) & 0x7f, (value >> 21) & 0x7f,
         (value >> 14) & 0x7f, (value >> 7) & 0x7f, value & 0x7f]
```

Add `0x20` to every component before transmission.

### Worked example: GInt3

For the value `1003`, the three components are `0`, `7`, and `107`, so the wire bytes are:

```text
20 27 8b
```

Decode them as `0 << 14 | 7 << 7 | 107 = 1003`.

## GInt5 variant boundary

The five-byte integer family has a known client-variant boundary. Some paths offset all five components, while another observed path leaves the first component unoffset and subtracts the offset only while decoding the remaining components. Do not move a `GInt5` field between protocol generations without checking the active encoder and decoder as a pair.

This is not a harmless display difference: the first byte changes the decoded value by a high-order component. Preserve the raw five bytes in diagnostics whenever a timestamp, checksum, or file size is disputed.

## Length-prefixed text

A normal string is encoded as one offset length byte followed by that many bytes. Common client send helpers cap a normal string at 223 bytes because `0xff` is a reserved boundary value.

Some protocol generations use `0xff` as a long-value marker. Its continuation rules are not uniform across all client paths, so a decoder must use the active generation's rule instead of assuming that every `0xff` string carries an unlimited remainder.

## Coordinates

Tile coordinates commonly use doubled values in one offset byte:

```text
wire_x = floor(x * 2) + 0x20
wire_y = floor(y * 2) + 0x20
```

Higher-precision position fields use a signed pixel value packed into a `GShort`. The absolute pixel value is shifted left by one bit and the low bit marks a negative value.

### Worked example: half-tile position

For `x = 30.5`, the doubled value is `61`; the wire byte is `61 + 0x20 = 0x5d`. Decode `0x5d - 0x20 = 61`, then divide by two.

## Comma text

Comma text represents lines as comma-separated fields. Quoted fields preserve commas and special characters; a quote inside a quoted field is doubled. Decode it before interpreting newline-separated commands or script text.

An empty line is a real field. Do not discard empty fields while splitting, and do not treat a comma inside a quoted field as a separator.

## Script line breaks

Some script payloads replace line feeds with byte `0xa7`. Convert `0xa7` to a line feed when displaying the script and reverse that conversion when constructing the corresponding upload payload.
