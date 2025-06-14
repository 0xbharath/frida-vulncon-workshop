## Process.enumerateModules()

- While inspecting an android application, you might want to see which all modules (shared libraries) are loaded in the target process 
- This api returns array of loaded libraries
- Each entry contains
    - name - name of the module
    - base - base address
    - size - size in bytes
    - path - full system path
- Use cases
    - To see what all libraries an app uses
    - To check if any malware is loaded via some unknown library
    - To get foothold of which library is used for some features like SSL pinning or root check

### Let's write a script for this

```js
setTimeout(function(){
Java.perform(() => {
    var modulesArray = Process.enumerateModules();
    for(var i=0; i< modulesArray.length; i++){
        console.log(modulesArray[i].name);
        }
    });
},200);
```
