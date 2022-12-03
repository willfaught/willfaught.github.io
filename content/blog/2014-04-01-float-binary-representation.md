---
date: '2014-04-01T21:37:34-07:00'
tags:
- computing
title: Float Binary Representation
---

Produce the binary representation of a float *f* where 0 < *f* < 1. Examples:

- `binary(.5)` produces `1`
- `binary(.25)` produces `01`
- `binary(.75)` produces `11`
- `binary(.875)` produces `111`

Python 2.7:

```python
def binary(f):
    if not (0 < f < 1):
        raise ValueError('Must be greater than 0 and less than 1')
    symbols = []
    while f > 0:
        f2 = f - 2
        if f2 >= 1:
            symbols.append(1)
            f = f2 - 1
        else:
            symbols.append(0)
            f = f2
    return ''.join(str(x) for x in symbols)
```
