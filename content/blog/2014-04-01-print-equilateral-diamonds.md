---
date: '2014-04-01T21:25:52+00:00'
draft: false
tags:
- computing
title: Print equilateral diamonds
---

Examples: X X X X X X X X X X X X X X X X X X X X X X X X X Python 2.7: def diamond(height): if height % 2 == 0: raise ValueError('Height must be odd') half_height = height / 2 for row in xrange(height): if row <= half_height: indent = half_height - row else: indent = row - half_height if row <= half_height: fill = 0 if row == 0 else row * 2 - 1 else: fill = 0 if row == height - 1 else (height - 1 - row) * 2 - 1 left = '%sX' % (' ' * indent) right = '%sX' % (' ' * fill) if fill else '' print left + right
