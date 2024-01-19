---
title: "Data Oriented Design"
linkTitle: "data oriented design"
weight: 30
description: >
  Notes on data oriented design.
---

Relational database and schema design:

- Split out infrequently access data into a separate table.
- Avoid tables with huge number of columns where only a few are ever used.
- Denormalization can be used to enhance performance for common queries.


Key points ([Reference](https://youtu.be/yy8jQgmhbAU?t=2035)
- Keep data flat
- Existence based prediction
    - reduce branching
    - apply the same operation on the whole table
- Id based handles
    - no pointers
    - Allow us to rearrange internal memory
- Table based output
    - no external dependencies
    - easy to reason about flow

