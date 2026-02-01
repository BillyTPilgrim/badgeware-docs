---
title: Files
summary: Load and save application data files and understand the Badgeware filesystem.
icon: save_as
---

### Writing to files from application code

The badge has a writeable LittleFS partition located at `/` which is intended for applications to store state information and cache any data they may need to hold on to across resets.

You can use normal Python style file access from your code:

```python
with open("/storage/myfile.txt", "w") as out:
  out.write("this is some text i want to keep\n")
```
