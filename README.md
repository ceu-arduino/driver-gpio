# GPIO (General-Purpose Input/Output)

A GPIO can be controlled as either an `input` or `output` digital pin:
- Input pins are readable as `high` or `low`.
- Output pins are writable as `high` or `low`.

In Céu-Arduino, a digital pin must be [declared](declaration) as either `input`
or `output` external event, e.g.:

```
input  high/low PIN_02;     // pin-2  is used as input
output high/low PIN_13;     // pin-13 is used as output
```

A pin can be controlled by an [await](await) or [emit](emit) statement
depending if it is declared as `input` or `output`, respectively.

An `await` halts the running trail until the pin changes its state (i.e., from
`low` to `high` or from `high` to `low`), e.g.:

```
var high/low v = await PIN_02;  // when pin-2 changes, v will hold the new state
```

An `emit` changes the state of the pin, e.g.:

```
emit PIN_13(high);
```

The state of a pin can be read at any time with `_digitalRead`, e.g.:

```
var high/low v = _digitalRead(13);
```

Note that the state of `output` pins can also be read.

[declaration]: http://fsantanna.github.io/ceu/out/manual/v0.30/storage_entities/#external-events
[await]:       http://fsantanna.github.io/ceu/out/manual/v0.30/statements/#event
[emit]:        http://fsantanna.github.io/ceu/out/manual/v0.30/statements/#events_1

## Include

To use GPIO, a program must always include `gpio.ceu`:

```
#include "gpio.ceu"
```

To use a pin as an `input` event, a program must include the corresponding
driver (if available).
For instance, an Arduino UNO uses the `ATmega328p` MCU, which supports
interrupts on `pin 2`:

```
#include "pin_02.ceu"
```

## Events

Céu-Arduino provides declarations to control each pin individually (`PIN_XX`)
or all as a group (`PIN_IN` and `PIN_OUT`).
However, a pin must always be declared individually, even when using as a
group.

### Input

#### PIN_XX

An individual input pin is of type `high/low` and carries the new changed pin
state, e.g.:

```
input high/low PIN_02;
```

#### PIN_IN

A group input is of type `(int, high/low)` and carries the pin that changed and
its new state:

```
input (int, high/low) PIN_IN;
```

### Output

### PIN_XX

An individual output pin is of type `high/low` and carries the new pin state to
change, e.g.:

```
output high/low PIN_13;
```

### PIN_OUT

A group output is of type `(int, high/low)` and carries the pin to change and
its new state:

```
output (int, high/low) PIN_OUT;
```

## Examples

### Individual

Whenever `PIN_02` changes, copy its changed value to `PIN_13`:

```
#include "gpio.ceu"
#include "pin_02.ceu"               // already declares `PIN_02` as input

output high/low PIN_13;             // declares `PIN_13` as output
emit PIN_13(_digitalRead(2));       // copy initial state from `PIN_02` to `PIN_13`

loop do
    var high/low v = await PIN_02;  // waits for changes on `PIN_02`
    emit PIN_13(v);                 // copy new value to `PIN_13`
end
```

### Group

Whenever one of the pins `2`, `3` or `4` changes, copy its changed value to
pin `11`, `12`, or `13`, respectively:

```
#include "gpio.ceu"             // already declares `PIN_IN` and `PIN_OUT`

#include "pin_02.ceu"           // already declares `PIN_XX` as input
#include "pin_03.ceu"
#include "pin_04.ceu"

output high/low PIN_11;         // declares `PIN_XX` as output
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
