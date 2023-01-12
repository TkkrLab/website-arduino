---
title: "Tips"
date: 2023-01-12
weight: 39
---

## Arduino IDE 2

### macOS specific issues

* Error message while verifying/compiling:

```
rpc error: code = FailedPrecondition desc = reading dir /Users/<username>/Documents/Arduino/libraries: open /Users/<username>/Documents/Arduino/libraries: operation not permitted

Compilation error: rpc error: code = FailedPrecondition desc = reading dir /Users/<username>/Documents/Arduino/libraries: open /Users/<username>/Documents/Arduino/libraries: operation not permitted
```

**Solution:**

The Arduino IDE needs Full Disk Access:

```
System Settings - Privacy & Security - Full Disk Access - + - Select /Applications/Arduino IDE.app
```

