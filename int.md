# Digital Input with External Interrupts

Input digital pins are readable as `high` or `low`.

Some specific pins generate external interrupts on pin changes:

```
        int0    int1
UNO       D2      D3
MEGA     D21     D20
```

## API

### Includes

```
#include "intX.ceu"     // `X` is a digit, e.g., `int0.ceu`
```

### Macros

#### INTX_GET

Gets the current state of the pin.

```
INTX_GET()              // `X` is a digit, e.g., `INT0_GET()`
```

### Input

```
input none INTX;        // `X` is a digit, e.g., `INT0`
```

- Occurrences:
    - whenever the state of the pin changes
- Payload:
    - `none`

## Examples

### Input / Output

State of output pin 13 follows the state of pin associated with *int0*:

```
#include "out.ceu"
#include "int0.ceu"     // UNO=D2, MEGA=D21

output high/low OUT_13;

var high/low v = INT0_GET();
emit OUT_13(v);

loop do
    await INT0;
    v = INT0_GET();
    emit OUT_13(v);
end
```