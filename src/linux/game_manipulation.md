# Game manipulation

# Game manipulation in Frida

## Install nSnake

```
cd ~/frida-playground/nSnake
make 
make install
```

```
Process.enumerateModules()[0].enumerateSymbols().filter(s => /delay|Delay/i.test(s.name))
```

```
Interceptor.attach(ptr("0xADDRESS_HERE"), { onEnter(args) { console.log("setSpeed called with:", args[1].toInt32()); } });
```

```
Interceptor.attach(ptr("0x5eff0900f340"), {
  onEnter(args) {
    // Print the int argument
    console.log("Game::getDelay called with arg:", args[1].toInt32());
  },
  onLeave(retval) {
    // Print the return value
    console.log("Game::getDelay returned:", retval.toInt32());
  }
});
```

```
Interceptor.replace(ptr("0x5e52e6107340"), new NativeCallback(function(self, arg) {
    const level = arg.toInt32();
    console.log(level)
    if (level < 5) return 100; // fast for low levels
    else return 300;           // slower for high levels
}, 'int', ['pointer', 'int']));
```