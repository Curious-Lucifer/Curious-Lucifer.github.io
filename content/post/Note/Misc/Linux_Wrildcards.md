---
author: Curious
title: "Note : Linux Wildcards"
date: 2023-09-24
math: true
slug: note-linux_wildcards
categories:
    - Note
tags:
    - Misc
---

| Options | Description |
| --- | --- |
| `*` | Matches any number (include 0) of any characters |
| `?` | Matches any single character |
| `[abcdef]` | Matches a single character enclosed in square brackets |
| `[a-b]` | Matches a single character in a range defined in square brackets |
| `[^abcd]` | Matches a single character not enclosed in square brackets |
| `[[:upper:]]` | Matches a single uppercase character |
| `[[:lower:]]` | Matches a single lowercase character |
| `[[:digit:]]` | Matches a single digit |
| `[[:upper:][:lower:]]` | Matches a single letter |
| `[^[:upper:]]` | Matches a single character that is not uppercase |