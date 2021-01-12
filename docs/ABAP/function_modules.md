---
layout: default
title: Function Modules
parent: ABAP
nav_order: 1
---

## Function Modules

Function modules are outdated. If you have to use one cause of implementing a BTE or something else create this function module and call a global class in it.
**Example:**

We have following function module:
```abap
FUNCTION Z_FI_EDI_PAYEXT_2441
  IMPORTING
    IM_MESTYP LIKE EDIDC-MESTYP
    IM_REGUH LIKE REGUH
    IM_REGUD LIKE REGUD
    IM_FLAG_NO_REPLACE TYPE C
  EXPORTING
    EX_FIMSG LIKE FIMSG
  CHANGING
    CH_XAVIS TYPE C
    CH_EDIDC LIKE EDIDC
  TABLES
    T_REGUP LIKE REGUP
    T_EDIDD LIKE EDIDD
  EXCEPTIONS
    DONT_CREATE_IDOC.
    
    " place for some coding or the creation of the object and calling the method
    
ENDFUNCTION.
```
In here we generate a new object from a global class in which our futher coding will be placed.

```abap
DATA(zcl_fi_edi_payext_2441) = NEW zcl_fi_edi_payext_2441( ).
```
**Note:** Use inline declarations!

Now we can call a public method of this class/object and can implement all further logic in here.
We should put all parameters from the function module in the call of this method and made some exception handling here.
```abap
zcl_fi_edi_payext_2441->change_idoc(
    EXPORTING
      im_mestyp          = im_mestyp
      im_reguh           = im_reguh
      im_regud           = im_regud
      im_flag_no_replace = im_flag_no_replace
    IMPORTING
      ex_fimsg           = ex_fimsg
    CHANGING
      ch_xavis           = ch_xavis
      ch_edidc           = ch_edidc
      t_regup            = t_regup[]
      t_edidd            = t_edidd[]
    EXCEPTIONS
      dont_create_idoc   = 1
      OTHERS             = 2
  ).
  IF sy-subrc <> 0.
    RAISE dont_create_idoc.
  ENDIF.
```
The `[]` in `t_regup[]` is because we do not only want to pass the header line to the class rather then entire table.

The global class can look something like this:
```abap
CLASS zcl_fi_edi_payext_2441 DEFINITION
  PUBLIC
  CREATE PUBLIC .
  
  PUBLIC SECTION.
    TYPES: t_regup TYPE TABLE OF regup,
           t_edidd TYPE TABLE OF edidd.
           
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
  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS zcl_fi_edi_payext_2441 IMPLEMENTATION.
  METHOD change_idoc.
    " Here the logic of the program
  ENDMETHOD.
ENDCLASS.
```
**Note:** We have to define `t_regup TYPE TABLE OF regup` and `t_edidd TYPE TABLE OF edidd.` for using them in the class.

To make the method more readable we can use ABAP doc expressions like this:
```abap
"! <p class="shorttext synchronized">Modify payment IDOCs</p>
"! @exception dont_create_idoc | <p class="shorttext synchronized">Don't created a payment IDOC</p>
"! @parameter im_mestyp | <p class="shorttext synchronized">Message type</p>
"! @parameter im_reguh | <p class="shorttext synchronized">Payment data from the payment program</p>
"! @parameter im_regud | <p class="shorttext synchronized">Transfer data form printing</p>
"! @parameter im_flag_no_replace | <p class="shorttext synchronized">SPACE=Special characters conversion required in texts</p>
"! @parameter ex_fimsg | <p class="shorttext synchronized">FI-messages</p>
"! @parameter ch_xavis | <p class="shorttext synchronized">Flag: Advice required</p>
"! @parameter ch_edidc | <p class="shorttext synchronized">Control record (IDoc)</p>
"! @parameter t_regup | <p class="shorttext synchronized">Processed items from the payment program</p>
"! @parameter t_edidd | <p class="shorttext synchronized">Data record (IDoc)</p>
```

The text between those bracets (`<p class="shorttext synchronized">Here some explanation text</p>`) is also shown in SAP Gui or in the ABAP Element Info in Eclipse.

Types can be documented like:

```abap
TYPES:
   "! <p class="shorttext synchronized">Explanation text</p>
   type TYPE TABLE OF spfli.
```
