## Java APIs 

### Java.perform()

- The classes needed to load the android apps are loaded after the ART for the app is setup
- This causes slight delays in loading the classes
- Java.perform api waits for the classes to be loaded
- Ensures that your code runs after the Java virtual machine is ready

Syntax:
```js
Java.perform(()=>{
    //your script here
});
```

### Java.performNow()
- This is the synchronous version of the Java.perform api
- Useful when the app is already running
- Useful for time critical tasks

Syntax:
```js
Java.performNow(()=>{
    //your script here
});
```

### Java.use()
- Returns a javascript wrapper of a given class
- Can be used to 
    - Access or override methods and fields of any loaded Java class
    - Call constructors, static or instance methods
    - Modify return values or arguments
    - Intercept behavior
- Uses ART's reflection apis

Syntax
```js
Java.perform(()=>{
    var class_ = Java.use("Class_Name");
});
```

### Java.choose()

- Scans the Java heap to find all live instances of a class and lets you interact with them.
- Can be used to:
    - Find real-time object instances
    - Read or modify fields at runtime
    - Call instance methods

Syntax:
```js
Java.perform(()=>{
    var class_ = Java.choose("Class_Name", callbacks);

    //callback are used to perform the action when a particular event occurs 
});
```


