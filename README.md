# GPIO (General-Purpose Input/Output)

## Summary

- Includes

```
#include "gpio.ceu"             // must always be included
```

- Events

```
// individual digital output
output high/low PIN_XX;         // substitute `XX` by the pin number (e.g., `PIN_13`)

// parameterized digital output
output (int, high/low) PIN;

// individual PWM output
output u8 PWM_XX;               // substitute `XX` by the pin number (e.g., `PWM_06`)

// parameterized PWM output
output (int, high/low) PWM;
```

- Abstractions

```
// individual digital input
code/await Pin (var int pin) -> high/low;
```

## Introduction

In Arduino, a GPIO pin can be controlled as either an input or output pin:

- Input digital pins are readable as `high` or `low`.
- Output digital pins are writable as `high` or `low`.
- Some pins also support `PWM` output with values between `0` and `255`.

In Céu-Arduino, output pins must be [declared][declaration] as `output`
external events.

A pin can be declared individually, e.g.:

```
output high/low PIN_13;     // pin-13 is used as digital output
output u8       PWM_06;     // pin-6  is used as PWM output
```

Alternatively, pins can be controlled as an argument in a single declaration:

```
output (int, high/low) PIN;     // the first argument is the pin number
output (int, high/low) PWM;
```

An `emit` sets the state of the pin, e.g.:

```
emit PIN_13(high);
emit PIN(13, high);
emit PWM_06(127);
emit PWM(6, 127);
```

Input pins require an abstraction since they use interrupt handlers and are
more complex internally:

```
code/await Pin (var int pin) -> high/low;
```

An `await` halts the running trail until the pin changes its state (i.e., from
`low` to `high` or from `high` to `low`), e.g.:

```
var high/low v = await Pin(2);  // when pin-2 changes, v will hold the new state
```

The state of a pin can also be read at any time with `_digitalRead`, e.g.:

```
var high/low v = _digitalRead(2);
```

*Note that the state of `output` pins can also be read.*

[declaration]: http://ceu-lang.github.io/ceu/out/manual/v0.30/storage_entities/#external-events
[await]:       http://ceu-lang.github.io/ceu/out/manual/v0.30/statements/#event
[emit]:        http://ceu-lang.github.io/ceu/out/manual/v0.30/statements/#events_1

## API

### Includes

To use GPIO pins, a program must always include `gpio.ceu`:

```
#include "gpio.ceu"
```

### Inputs

None

### Outputs

#### PIN_XX

An individual digital output pin carries its new state to change, e.g.:

```
output high/low PIN_13;
```

Arguments:

1. `high/low`: new state of the pin

#### PIN

A parameterized digital output and carries the pin to change and its new state:

```
output (int, high/low) PIN
```

Arguments:

1. `int`:      pin to change
2. `high/low`: its new state

#### PWM_XX

An individual PWM output pin carries its new value to change, e.g.:

```
output u8 PWM_06;
```

Arguments:

1. `u8`: new value of the pin

#### PWM

A parameterized PWM output and carries the pin to change and its new state:

```
output (int, u8) PWM
```

Arguments:

1. `int`: pin to change
2. `u8`:  new value of the pin

### Abstractions

#### Pin

Receives an input pin and returns when the pin changes:

```
code/await Pin (var int pin) -> high/low;
```

Arguments:

1. `int`: pin to await for a change

Return:

- `high/low`: new value of the pin

## Examples

### Digital input/output

Whenever *pin 2* changes, copy its changed value to *pin 13*:

```
#include "gpio.ceu"

emit PIN(13, _digitalRead(2));      // copy initial state from pin 2 to pin 13
loop do
    var high/low v = await Pin(2);  // waits for changes on pin 2
    emit PIN(13, v);                // copy new value to pin 13
end
```

### PWM output

Fades *pin 6* slowly from `0` to `255` and back to `0` continuously:

```
#include "gpio.ceu"
#include "wclock.ceu"           // allows for `await 5ms` below

output u8 PWM_06;               // declares `PWM_06` as PWM output

loop do                         // fades continuously
    var int i;
    loop i in [0->255] do       //   from 0->255
        emit PWM_06(i);         //     set new value
        await 5ms;              //     small delay
    end
    loop i in [0<-255] do       //   from 0<-255
        emit PWM_06(i);         //     set new value
        await 5ms               //     small delay;
    end
end
```
