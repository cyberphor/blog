---
layout: post
title: 'Microsoft Excel Formulas'
category: examples
permalink: 'examples/microsoft-excel-formulas'
---

### Table of Contents
* [Count Where Two Columns Meet Specific Criteria](#count-where-two-columns-meet-specific-criteria)

### Count Where Two Columns Meet Specific Criteria
```bash
# Column A is "Pay Period," Column B is "Paid"
=COUNTIFS(A:A,"1st",B:B,"Yes")
=COUNTIFS(A:A,"15th",B:B,"Yes")
```
