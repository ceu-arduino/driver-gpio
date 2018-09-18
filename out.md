# Digital Output

Output digital pins are writable as `high` or `low`.

## API

### Includes

```
#include "out.ceu"
```

### Outputs

#### OUT_XX

An individual digital output pin carries its new state to change, e.g.:

```
output high/low PIN_13;
```

Arguments:

1. `high/low`: new state of the pin

#### OUT

Parameterized digital output carries the pin to change and its new state:

```
output (int, high/low) OUT;
```

Arguments:

1. `int`:      pin to change
2. `high/low`: new state of the pin

The individual pins to use must be declared with [`OUT_XX`](#out_xx) for proper
configuration.

## Examples

### Individual output

Writes `high` to pin 13:

```
#include "out.ceu"

output high/low OUT_13;

emit OUT_13(high);

await FOREVER;
```

### Parameterized output

Writes `high` to pins 10-13:

```
#include "out.ceu"

output high/low OUT_10;
output high/low OUT_11;
output high/low OUT_12;
output high/low OUT_13;

var int i;
loop i in [10 -> 13] do
    emit OUT(i, high);
end

await FOREVER;
```
