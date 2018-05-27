# GPIO (General-Purpose Input/Output)

## Summary

- Includes

```
#include "gpio.h"                   // must always be included
#include "pin_XX.h"                 // include for input pins (e.g., `#include "pin_02.ceu"`)
```

- Events

```
input  high/low PIN_XX;             // digital input  (e.g., `input  high/low PIN_02`)
output high/low PIN_XX;             // digital output (e.g., `output high/low PIN_13`)

input  (int, high/low) PIN_IN;      // group digital input
output (int, high/low) PIN_OUT;     // group digital output

output u8 PWM_XX;                   // PWM output (e.g., `output u8 PWM_06`)
```

## Introduction

A GPIO pin can be controlled as either an `input` or `output` pin:
- Input digital pins are readable as `high` or `low`.
- Output digital pins are writable as `high` or `low`.
- Some pins also support `PWM` output.

In Céu-Arduino, a digital pin must be [declared][declaration] as either `input`
or `output` external event, e.g.:

```
input  high/low PIN_02;     // pin-2  is used as digital input
output high/low PIN_13;     // pin-13 is used as digital output
output u8       PWM_06;     // pin-6  is used as PWM output
```

A pin can be controlled by an [await][await] or [emit][emit] statement
depending if it is declared as `input` or `output`, respectively.

An `await` halts the running trail until the pin changes its state (i.e., from
`low` to `high` or from `high` to `low`), e.g.:

```
var high/low v = await PIN_02;  // when pin-2 changes, v will hold the new state
```

An `emit` changes the state of the pin, e.g.:

```
emit PIN_13(high);
emit PWM_06(127);
```

The state of a pin can be read at any time with `_digitalRead`, e.g.:

```
var high/low v = _digitalRead(13);
```

Note that the state of `output` pins can also be read.

[declaration]: http://fsantanna.github.io/ceu/out/manual/v0.30/storage_entities/#external-events
[await]:       http://fsantanna.github.io/ceu/out/manual/v0.30/statements/#event
[emit]:        http://fsantanna.github.io/ceu/out/manual/v0.30/statements/#events_1

## Includes

To use GPIO pins, a program must always include `gpio.ceu`:

```
#include "gpio.ceu"
```

To use a pin as a digital `input` event, a program must include the
corresponding driver (if available).
For instance, an Arduino UNO uses the `ATmega328p` MCU, which supports
interrupts on pin 2:

```
#include "pin_02.ceu"
```

## Events

Céu-Arduino provides declarations to control each pin individually (`PIN_XX`)
or all pins as a group (`PIN_IN` and `PIN_OUT`).
However, a pin must always be declared individually, even when using as a
group.

### Input

#### PIN_XX

An individual digital input pin carries its new changed state, e.g.:

```
input high/low PIN_02;
```

Arguments:

1. `high/low`: new state of the pin

#### PIN_IN

A group digital input carries the pin that changed and its new state:

```
input (int, high/low) PIN_IN;
```

Arguments:

1. `int`:      pin that changed
2. `high/low`: its new state

### Output

### PIN_XX

An individual digital output pin carries its new state to change, e.g.:

```
output high/low PIN_13;
```

Arguments:

1. `high/low`: new state of the pin

### PIN_OUT

A group digital output and carries the pin to change and its new state:

```
output (int, high/low) PIN_OUT;
```

Arguments:

1. `int`:      pin to change
2. `high/low`: its new state

### PWM_XX

An individual PWM output pin carries its new value to change, e.g.:

```
output u8 PWM_06;
```

Arguments:

1. `u8`: new value of the pin

## Examples

### Individual

Whenever `PIN_02` changes, copy its changed value to `PIN_13`:

```
#include "gpio.ceu"
#include "pin_02.ceu"               // already declares `PIN_02` as digital input

output high/low PIN_13;             // declares `PIN_13` as digital output
emit PIN_13(_digitalRead(2));       // copy initial state from `PIN_02` to `PIN_13`

loop do
    var high/low v = await PIN_02;  // waits for changes on `PIN_02`
    emit PIN_13(v);                 // copy new value to `PIN_13`
end
```

### Group

Whenever one of the pins `2`, `3`, or `4` changes, copy its changed value to
pin `11`, `12`, or `13`, respectively:

```
#include "gpio.ceu"             // already declares `PIN_IN` and `PIN_OUT`

#include "pin_02.ceu"           // already declares `PIN_XX` as digital input
#include "pin_03.ceu"
#include "pin_04.ceu"

output high/low PIN_11;         // declares `PIN_XX` as digital output
output high/low PIN_12;
output high/low PIN_13;

emit PIN_11(_digitalRead(2));   // copy initial states
emit PIN_12(_digitalRead(3));
emit PIN_13(_digitalRead(4));

loop do
    var int pin;
    var high/low v;
    (pin,v) = await PIN_IN;     // waits for changes on any of the input pins
    emit PIN_OUT(pin+9, v);     // copy new input value to associated output pin
end
```

## PWM

Fades `PIN_06` slowly from `0` to `255` and back to `0` continuously:

```
#include "gpio.ceu"
#include "wclock.ceu"

output u8 PWM_06;

loop do
    var int i;
    loop i in [0->255] do
        emit PWM_06(i);
        await 5ms;
    end
    loop i in [0<-255] do
        emit PWM_06(i);
        await 5ms;
    end
end
```
