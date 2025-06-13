## Frida Java APIs

- Frida has a JAVA API that allows instrumentation of processes having Java VM loaded
- Like Dalvik/ART
- This API does not support non Dalvik/ART processes

### Checking if Java API is available
- Attach to any process
- Get into Frida REPL - Read-Eval-Print Loop
- Type ``Java.available``
