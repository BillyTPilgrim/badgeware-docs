---
title: Time
summary: Process the passing of time and scale changes your application state based on elapsed frame time.
icon: timer
---

## Timing

`io.ticks`\
The number of ticks (milliseconds) since the badge was powered on when `update()` was called.

`io.ticks_delta`\
The number of ticks (milliseconds) since the previous time `update()` was called. Useful for timing animations where the framerate isn't completely stable.
