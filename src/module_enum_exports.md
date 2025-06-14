## Module.enumerateExports()

- Returns an array of all the exported symbols from the specified native module
- Gives informationn about functions, variables, their names, addresses and types
- Use case
    - Discover all exported functions in a native ``.so``
    - Hook custom logic for app hardening like encrpytion, ssl pinning, root detection etc. 

Example:
```js
Java.perform(() => {

    setTimeout(function () {
    var modulesArray = Process.enumerateModules();
    for(var i=0; i< modulesArray.length; i++){
        var modName = modulesArray[i].name
        if(modName == "libcoldstart.so"){
            var exports = modulesArray[i].enumerateExports()
            for(var exp=0; exp<exports.length; exp++){
                console.log(JSON.stringify(exports[exp]))
        }
        }
    }
}, 2000);
    });
```