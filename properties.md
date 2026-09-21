# Property Fields

Property packets are not self-describing strings. Each property ID selects the number of bytes and the decoder used for the following value. The table below lists the common client property slots.

| ID | Name | Wire type | Meaning |
| ---: | --- | --- | --- |
| 1 | `PLPROP_MAXPOWER` | GByte | Maximum power/hearts. |
| 2 | `PLPROP_CURPOWER` | GByte | Current power; presentation commonly scales it by two. |
| 3 | `PLPROP_RUPEESCOUNT` | GInt3 | Currency count. |
| 4 | `PLPROP_ARROWSCOUNT` | GByte | Arrow count. |
| 5 | `PLPROP_BOMBSCOUNT` | GByte | Bomb count. |
| 6 | `PLPROP_GLOVEPOWER` | GByte | Glove power. |
| 7 | `PLPROP_BOMBPOWER` | GByte | Bomb power. |
| 8 | `PLPROP_SWORDPOWER` | Special | Sword power and optional image data. |
| 9 | `PLPROP_SHIELDPOWER` | Special | Shield power and optional image data. |
| 10 | `PLPROP_GANI` | GString | Current animation/gani. |
| 11 | `PLPROP_HEADIMAGE` | Special | Head image selector or image name. |
| 12 | `PLPROP_CURCHAT` | GString | Current chat text. |
| 13 | `PLPROP_COLORS` | 5 GBytes | Skin, coat, sleeves, shoes, and belt. |
| 14 | `PLPROP_ID` | GShort | Player numeric ID. |
| 15 | `PLPROP_X` | GByte | X position multiplied by two. |
| 16 | `PLPROP_Y` | GByte | Y position multiplied by two. |
| 17 | `PLPROP_DIRECTION` | GByte | Direction/sprite value. |
| 18 | `PLPROP_STATUS` | GByte | Status flags. |
| 19 | `PLPROP_CARRYSPRITE` | GByte | Carried sprite. |
| 20 | `PLPROP_CURLEVEL` | GString | Current level name. |
| 21 | `PLPROP_HORSEGIF` | GString | Horse image. |
| 22 | `PLPROP_HORSEBUSHES` | GByte | Horse bushes count. |
| 23 | `PLPROP_EFFECTCOLORS` | GByte | Effect-color value. |
| 24 | `PLPROP_CARRYNPC` | GInt3 | Carried NPC ID. |
| 25 | `PLPROP_APCOUNTER` | GShort | Alignment/AP counter. |
| 26 | `PLPROP_MAGICPOINTS` | GByte | Magic points. |
| 27 | `PLPROP_KILLSCOUNT` | GByte | Kill count in the current compact property decoder. |
| 28 | `PLPROP_DEATHSCOUNT` | GByte | Death count in the current compact property decoder. |
| 29 | `PLPROP_ONLINESECS` | GByte | Online-seconds value in the current compact property decoder. |
| 30 | `PLPROP_IPADDR` | 5 GBytes | Encoded IP value. |
| 31 | `PLPROP_UDPPORT` | GInt3 | UDP port. |
| 32 | `PLPROP_ALIGNMENT` | GByte | Alignment value. |
| 33 | `PLPROP_ADDITFLAGS` | GByte | Additional status flags. |
| 34 | `PLPROP_ACCOUNTNAME` | GString | Account name. |
| 35 | `PLPROP_BODYIMAGE` | GString | Body image. |
| 36 | `PLPROP_RATING` | 3 GBytes | Packed rating and deviation fields. |
| 42 | `PLPROP_ATTACHNPC` | Special | Attachment type plus NPC ID. |
| 43 | `PLPROP_GMAPLEVELX` | GByte | Map-level X coordinate. |
| 44 | `PLPROP_GMAPLEVELY` | GByte | Map-level Y coordinate. |
| 45 | `PLPROP_Z` | GByte | Z position. |
| 50 | `PLPROP_JOINLEAVELVL` | GByte | Level join/leave marker. |
| 53 | `PLPROP_PSTATUSMSG` | GString | Player status message. |
| 76 | `PLPROP_TEXTCODEPAGE` | GInt3 | Text code page. |
| 78 | `PLPROP_X2` | GShort | Signed pixel-precise X. |
| 79 | `PLPROP_Y2` | GShort | Signed pixel-precise Y. |
| 80 | `PLPROP_Z2` | GShort | Signed pixel-precise Z. |
| 81 | `PLPROP_UNKNOWN81` | GByte | Player-list/channel placement flags. |

## Property parser rules

1. Read the property ID.
2. Select its exact wire type.
3. Check that the remaining payload contains the required number of bytes.
4. Decode the value without consuming the next property.
5. Merge the field into the relevant player or NPC cache.

String properties can contain commas and spaces. Numeric properties can be signed or scaled for presentation. Preserve both the raw value and the normalized value when exact round-tripping matters.

## Worked property block

This short property block sets maximum power to `8` and current power to `8`:

```text
21 28 22 28
^^ ^^ ^^ ^^
id value id value
```

`0x21` is property `1` plus `0x20`; `0x28` decodes to `8`. `0x22` is property `2` plus `0x20`.

## Special properties

Sword and shield properties can use a power byte followed by an image field. Head-image properties can use either a built-in selector or a length-bearing image name. These are not interchangeable with ordinary GStrings.

The packed rating field contains two logical values. Decode the bit layout before formatting it for a UI, and retain the packed representation for diagnostics.

Precise coordinate properties use a signed `GShort`: the low bit is the sign marker and the remaining bits represent the absolute pixel value. Do not apply the ordinary half-tile division to these fields.

## Comma-text property example

When a property family stores multiple text lines, the payload is decoded after the property boundary is known. For example:

```text
"line one","line two",""
```

decodes to three lines, including the final empty line. Empty fields must not be dropped.
