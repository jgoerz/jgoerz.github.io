---
title: "Data-Oriented Design and C++"
date: 2023-12-26T10:10:00-05:00
weight: 20
---

[Presentation by Mike Acton](https://youtu.be/rX0ItVEVjHc)

Presentation index:

- [Avoid ghost read and writes](https://youtu.be/rX0ItVEVjHc?t=2862)
    - Don't re-read values or re-call functions when you already have the data.
    - Hoist all loop-invariant reads and branches.  Even super obvious ones that
      should already be in the registers.
- [Low information density](https://youtu.be/rX0ItVEVjHc?t=2914)
    - pack the decision together
    - combine with other reads/transforms
- [Reviewing open source code](https://youtu.be/rX0ItVEVjHc)
- [Truths](https://youtu.be/rX0ItVEVjHc?t=4172)


Figure out the most common transform and start there.




