## Easy Protection Bypass

Let's write some custom script and bypass the runtime protection in the BugBazaar app.

First let's try to figure out which all functions are called

```js
setTimeout(function() {

    const ignore_classes = [
        "com.google","android","java","io",
        "kotlin", "com.bumptech","[", "com.android", "sun",
        "jdk","gov.nist","okio","org.apache", "org.xml","libcore",
        "org.ccil", "com.sun","dalvik","org.w3c","org.json"
    ];

    function isExcluded(className) {
        return ignore_classes.some(prefix => className.startsWith(prefix));
    }


    Java.perform(function () {
        Java.enumerateLoadedClasses({
            onMatch: function(className) {
                    if (!isExcluded(className) && className.startsWith("com")) {
                
                    const clazz = Java.use(className);
                    try {
                    const methods = clazz.class.getDeclaredMethods();
                    methods.forEach(method => {
                        const methodName = method.getName();

                            const overloads = clazz[methodName].overloads;
                            overloads.forEach(overload => {
                                overload.implementation = function () {
                                    console.log(`${className}.${methodName}(${overload.argumentTypes.map(t => t.className).join(", ")})`);
                                    return overload.apply(this, arguments);
                                };
                            });
                        });
                        } catch (hookErr) {
                        }
                      }
                },
                onComplete: function() {}
            });
        });
        
    },2000);
```

Let's override the method responsible for the check:

```js
Java.perform(() => {

    let RootBeer = Java.use("com.scottyab.rootbeer.RootBeer");
RootBeer["isRooted"].implementation = function () {
    console.log(`RootBeer.isRooted is called`);
    let result = this["isRooted"]();
    console.log(`RootBeer.isRooted result=${result}`);
    return false;
};

let Native = Java.use("com.darvin.security.Native");
Native["isMagiskPresentNative"].implementation = function () {
    console.log(`Native.isMagiskPresentNative is called`);
    let result = this["isMagiskPresentNative"]();
    console.log(`Native.isMagiskPresentNative result=${result}`);
    return false;
};
});
```