# FRida on Android

## Attaching to the app process
- Injects a compiled ``.so`` file, frida agent, into the target app process
- The agent sets up JS runtime and provides the environment for JS execution

## How does it hooks
- It uses Android Runtime's Java Bridge via libart.so
- It uses Java reflection APIs to fetch classes and then overrides the method pointers to call JS handlers. 

## Components in the setup
### Frida Server
- A native binary that runs as root on the device.
- Acts as a backend agent that injects the Frida instrumentation engine (frida-agent.so) into target app processes.
- Listens on a TCP port (default: 27042) for incoming connections from the host.

### Frida CLI
- Used to communicate with frida server via USB or remote connection



