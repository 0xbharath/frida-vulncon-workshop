# Basic Instrumentation with Frida

# FRida 101

1. Copy the following code into a file `counter.c`

```c
#include <stdio.h>
#include <unistd.h>     // for sleep()
#include <stdbool.h>    // for true

static void f(int n) {
    printf("Number: %d\n", n);
}

int main(void) {
    int n = 1;
    printf("f() is at address: %p\n", f);
    while (true) {
        f(n++);
        sleep(1);
    } 
}
```

2. Compile it with the following `gcc` command

```bash
gcc -g -O0 -o counter counter.c
```

2. There will be binary with the name `counter`
3. Inspect the file using 

***What is this Binary?**

## Inspect the binary file

```bash
file counter
```

- Execute the binary and understand the output

```bash
./counter
```

**Question 1: How does this binary print the counter?**

**Question 2: Can we manipulate the binary to print different output?**
  
## Instrumenting the binary using Frida 

_ Run the following command to spawn the binary using Frida_

```bash
$ frida ./counter --pause
... SNIPPED ...
   . . . .   Connected to Local System (id=local)
Spawned `./counter.o`. Use %resume to let the main thread start executing!
[Local::counter.o ]->
```

## Dig deeper into binary

_Run the following command to enumerate the modules/functions invoked by the binary_


You can run the following JS code in the Frida REPL after hooking into the process

```js
Process.enumerateModules().forEach(m => console.log(m.name));
```

You can save this into a file and run this after attaching/spawning a binary

```js
var modules = Process.enumerateModules()

Process.enumerateModules()[0]
  for (var i = 0; i < modules.length; i++) {
  console.log("module name: " + modules[i].name); 
}
```

## Exploring the binary

```bash
frida ./counter -l enumerate.js
```


- Lets print all the imports and exports which are present in out binary

```js
'use strict';

// Replace this with your module name if needed (e.g., 'libc.so.6' or 'yourbinary')
const moduleName = Process.enumerateModules()[0].name;
const module = Process.getModuleByName(moduleName);

console.log(`\n[+] Listing Imports of ${module.name}`);
const imports = module.enumerateImports();
imports.forEach(imp => {
    console.log(`Import: ${imp.name} from ${imp.module} at ${imp.address}`);
});

console.log(`\n[+] Listing Exports of ${module.name}`);
const exports = module.enumerateExports();
exports.forEach(exp => {
    console.log(`Export: ${exp.name} - Type: ${exp.type} at ${exp.address}`);
});

```

```js
'use strict';

const sym = DebugSymbol.fromName('f');
const f_addr = sym.address;

console.log('[*] Found f() at:', f_addr);

Interceptor.attach(f_addr, {
    onEnter(args) {
        console.log('[+] f() called with number n =', args[0].toInt32());
    }
```

## Let's hook into the counter

We can intercept the counter binary with the following:

```js
'use strict';

const sym = DebugSymbol.fromName('f');
const f_addr = sym.address;

console.log('[*] Found f() at:', f_addr);

Interceptor.attach(f_addr, {
    onEnter(args) {
        console.log('[+] f() called with number n =', args[0].toInt32());
    }
});
```

We can intercept the counter number with the following:

```js
'use strict';

const sym = DebugSymbol.fromName('f');
const f_addr = sym.address;

console.log('[*] Found f() at:', f_addr);

Interceptor.attach(f_addr, {
    onEnter(args) {
        //console.log('[+] f() called with number n =', args[0].toInt32());
    
        console.log('[+] Original n =', args[0].toInt32());

        // Overwrite the value of n
        const new_value = Math.floor(Math.random() * 10); ;
        args[0] = ptr(new_value);

        console.log('[+] Overwritten n =', new_value);
}
});
```

We can intercept the counter sleep function with the following:


```js
'use strict';

// Get the module object first
const libc = Process.getModuleByName('libc.so.6');

// Use the module's method to get the address
const sleep_addr = libc.getExportByName('sleep');

console.log('[*] Hooking sleep() at:', sleep_addr);

Interceptor.attach(sleep_addr, {
    onEnter(args) {
        const original = args[0].toInt32();
        console.log('[*] sleep() called with:', original, 'second(s)');

        // Replace sleep duration with 5 seconds
        args[0] = ptr(5);
        console.log('[*] sleep() modified to: 5 seconds');
    }
});
```

We can call the `f()` function from Frida using:

```js
'use strict';

// Find address of function f()
const sym = DebugSymbol.fromName('f');
const f_ptr = sym.address;
console.log('[*] Calling f() at:', f_ptr);

// Create NativeFunction: f(int) -> void
const f = new NativeFunction(f_ptr, 'void', ['int']);

// Call it with your own value
f(777);

console.log('[+] Called f(777)');
```

Let's use `frida-trace` handlers

```js
defineHandler({
  onEnter(log, args, state) {
    const format = args[0].readUtf8String();
    if (format === 'Number: %d\n') {
      const n = this.context.rsi.toInt32();
      log(`printf(format="Number: %d", value=${n})`);
    } else {
      log(`printf(format="${format}", ...)`);
    }
  },

  onLeave(log, retval, state) {}
});
```