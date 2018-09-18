# Digital Input with Pin-Change Interrupts

Input digital pins are readable as `high` or `low`.

Most pins generate pin-change interrupts in blocks.

A block is a set of pins associated with the same interrupt:
- `PCINT0` is a block of pins in the range `PCINT[0-7]`,
- `PCINT1` is a block of pins in the range `PCINT[8-15]`,
- and so on.

*NOTE: The name "PCINT" is used for both the block interrupt number and the
individual pins (e.g., the pin `PCINT1` is in the range of the interrupt
`PCINT0`.)*

Pin mapping in popular Arduinos:

```
        pcint0      pcint1
UNO     D8-D12       A0-A5
MEGA   D53-D50       D0
       D10-D13     D15,D14
```

- In the `UNO`, the pin `PCINT2` is associated with interrupt `PCINT0` and
  corresponds to pin `D10`.
- In the `MEGA`, the pin `PCINT8` is associated with interrupt `PCINT1` and
  corresponds to pin `D0`.

## API

### Includes

```
#include "pcintX.ceu"   // `X` is a digit, e.g., `pcint0.ceu`
```

### Output

#### PCINTX_ENABLE

Enables or disables pin-change interrupts for the provided pin in the block.

```
output (on/off, int) PCINTX_ENABLE; // `X` is a digit, e.g., `PCINT0_ENABLE`
```

Parameters:

1. `on/off`: enable or disable interrupts
2. `int`:    individual pin to configure

#### PCINTX_GET

Gets the current state of the provided pin in the block.

```
output (int, &high/low) PCINTX_GET; // `X` is a digit, e.g., `PCINT0_GET`
```

Parameters:

1. `int`:       individual pin to query (e.g., `_PCINT4`)
2. `&high/low`: reference to write the value

### Inputs

#### PCINTX

```
input none PCINTX;      // `X` is a digit, e.g., `PCINT0`
```

- Occurrences:
    - whenever the state of the any of the pins in the block changes
- Payload:
    - `none`

## Examples

### Input / Output

State of output pin 13 follows the state of pin associated with *PCINT4*:

```
#include "out.ceu"
#include "pcint0.ceu"

output high/low OUT_13;

emit PCINT0_ENABLE(_PCINT4);    // UNO=D12, MEGA=D10

var high/low v = PCINT0_GET(_PCINT4);
emit OUT_13(v);
loop do
    await PCINT0;
    v = PCINT0_GET(_PCINT4);
    emit OUT_13(v);
end
```

### Enable / Disable

Similar to previous example, but disables interrupts after `5` seconds:

```
#include "out.ceu"
#include "wclock.ceu"
#include "pcint0.ceu"

output high/low OUT_13;

spawn do
    emit PCINT0(on, _PCINT4);   // UNO=D12, MEGA=D10
    await 5s;
    emit PCINT0(off, _PCINT4);  // UNO=D12, MEGA=D10
end

var high/low v = PCINT0_GET(_PCINT4);
emit OUT(13, v);
loop do
    await PCINT0;
    v = PCINT0_GET(_PCINT4);
    emit OUT_13(v);
end
```
