---
title: "Welcome"
linkTitle: "Site"
type: "docs"
# tags: ["intro"]
weight: 20

cascade:
- _target:
    path: "/blog/**"
  type: "blog"
  # set to false to include a blog section in the section nav along with docs
  toc_root: true
- _target:
    path: "/**"
    kind: "page"
  type: "docs"
- _target:
    path: "/**"
    kind: "section"
  type: "docs"
- _target:
    path: "/**"
    kind: "section"
  type: "home"
---

This is my collection of notes about things that seem to repeatedly surface or
that I find interesting.  Recording them here provides a way for me to refer to
them or jog my memory.  Maybe you'll find them interesting or useful as well.
