---
layout: default
title: ATC
parent: ABAP
nav_order: 1
---

## ATC - ABAP Test Cockpit

### Reminder

> Old Move type conflict (Type DZLSHB_F15, Note: 0002610650) STRUCTURE -> C

```abap
DATA:  gt_item        TYPE rfkord_t_item, "fail with type DZLSHB_F15
       lr_data        TYPE REF TO data,
       ls_ads_dataref TYPE /softw/sads_dataref, " refname -> string | data -> ref data
       lt_ads_dataref TYPE /softw/pads_dataref. " table of /softw/sads_dataref

GET REFERENCE OF gt_item INTO lr_data.
ls_ads_dataref-data ?= lr_data.
APPEND ls_ads_dataref TO lt_ads_dataref.
```

ToDo: Run test in test-system and if it works apply for exemption.
