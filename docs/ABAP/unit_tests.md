---
layout: default
title: Unit Tests
parent: ABAP
nav_order: 3
---

## Unit Tests

### General

For ABAP Unit Tests you have to declare a testing class (`CLASS test DEFINITION`) with the additive `FOR TESTING`.

Here we have an example of a normal class with two methods `set_text_to_x` and `minus_ten_percent` for wich we want to define some unit tests.
```abap
CLASS class DEFINITION.
  PUBLIC SECTION.
    CLASS-DATA text TYPE string.
    CLASS-METHODS set_text_to_x.
    METHODS minus_ten_percent CHANGING price TYPE p.
ENDCLASS.

CLASS class IMPLEMENTATION.
  METHOD set_text_to_x.
    text = 'U'. " should be 'X'
  ENDMETHOD.
  METHOD minus_ten_percent.
    price = price * '0.9'.
  ENDMETHOD.
ENDCLASS.
```

Now we can declare a test class and tests for the two methodes above.

```abap
CLASS test DEFINITION FOR TESTING RISK LEVEL HARMLESS DURATION SHORT.
  PRIVATE SECTION.
    METHODS test_for_x FOR TESTING.
    METHODS test_minus_ten_percent FOR TESTING.
ENDCLASS.

CLASS test IMPLEMENTATION.
  METHOD test_for_x.
    class=>set_text_to_x( ).
    cl_aunit_assert=>assert_equals( act = class=>text
                                    exp = 'X'
                                    msg = 'Text "' && class=>text && '" is not equals "X".').
  ENDMETHOD.
  METHOD test_minus_ten_percent.

    DATA: price TYPE p VALUE 200.
    DATA(class) = NEW class( ).

    class->minus_ten_percent(
      CHANGING
        price = price
    ).

    cl_aunit_assert=>assert_equals( act = price
                                    exp = 180
                                    msg = 'Ninty percent not calculated correctly').

  ENDMETHOD.
ENDCLASS.
```

| **Risk Level** | Description |
|----|----|
| CRITICAL | The test could change system settings or the Customizing, for example. |
| DANGEROUS | The test could change persistent application data, for example. |
| HARMLESS | The test has no effect on persistent data or system settings. |

`... RISK LEVEL {CRITICAL|DANGEROUS|HARMLESS}`

| **Execution Duration** | Description |
|---|---|
| SHORT | an imperceptibly short execution duration is expected. This is the default value. |
| MEDIUM | a noticeable execution duration is expected. |
| LONG | a very noticeable execution duration is expected. |

`... DURATION {SHORT|MEDIUM|LONG}`

### When do unit tests make sense in ABAP

For test driven development I wonder when it makes sense to implement ABAP Unit Tests for Methods in a class.

For example I have a class with to methods in it.

get the x last characters of a string
modify a whole IDOC with case statements in it differentiate between company codes.
This example might be a little bit extreme but shows my problem really well.

I wonder if it makes sense to implement unit tests for the first method think that is a little bit overload for a method looking like this with only one changing parameter:
```abap
METHOD get_x_last_chars.
    IF strlen( string ) < num_last_chars.
      result = string.
    ELSE.
      result = substring( val = string
                          off = strlen( string ) - num_last_chars
                          len = num_last_chars ).
    ENDIF.
  ENDMETHOD.
```
But on the other side I have a method with more than 400 lines of code with very different outcomes for a defined input. You can maybe can see the complexity of the method at the parameters:

```abap
METHODS change_idoc
      IMPORTING
        im_mestyp          TYPE edidc-mestyp
        im_reguh           TYPE reguh
        im_regud           TYPE regud
        im_flag_no_replace TYPE c
      EXPORTING
        ex_fimsg           TYPE fimsg
      CHANGING
        ch_xavis           TYPE c
        ch_edidc           TYPE edidc
        t_regup            TYPE t_regup
        t_edidd            TYPE t_edidd
      EXCEPTIONS
        dont_create_idoc.
```
May you can help me to specify when unit tests are useful and when they creating to much work implementing them?

**Answer:**

You should know best the complexity of a certain piece of logic, the degree of reusability, and how often new changes/fixes/extensions will be expected at that place. Also it is important to think how many other developers will have to review that code/maintain it, and how long it is expected to 'live' - customer support duration.

Maybe you will like to spare the effort if the code is small, simple, not frequently changed, and therefore not significant to many other developers than the author.

In all other cases, it will be great to have tests.
