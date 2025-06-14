## Java enumerate loaded classes

- This API lists all Java classes currently loaded in memory in the target Android app's Java VM (ART/Dalvik).
- Use case
    - List all app-defined Java classes at runtime
    - Identify real class names after dynamic loading
    - Filter classes to hook based on name/prefix
    - See which classes are loaded during specific actions

Example:
```js
setTimeout(function() {

    Java.perform(function () {
        Java.enumerateLoadedClasses({
            onMatch: function(className) {
                    if (className.startsWith("com.BugBazaar")) {
                    console.log(className);
                      }
                },
                onComplete: function() {}
            });
        });
        
    },2000);

```