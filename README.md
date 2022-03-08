# Scalable JavaScript design pattern

To create a truly modular design for our application, we need to break it down into smaller functional pieces, in such a way that each piece will specialize in and be responsible for very specific tasks. This enables us to achieve the principle of separation of concerns and responsibilities.

At the same time, each main piece may consist of other smaller pieces which are packaged together to create the main piece.

In this modular architecture, we capitalize on the same concept and create spaces for our components to play in and to be isolated from other pieces of the application.

## Features of sandbox architecture

* Eliminate tight coupling among our application components and the core module.

* Sandbox module is designed to be an interface and to provide communication among our components and the rest of the application.

* Sandbox is considered a contract and, as such, it should never change. It means that we cannot change what is already there and that our components have come to rely on.

* When our components are loaded in the application, either at application start-up time or dynamically any time after, they are all given an instance of the sandbox module.

* The sandbox module provides the following for our components:

  * A consistent interface
  * Security
  * Communication
  * Filtering

* When a component needs a particular functionality, it does not necessarily need to implement it itself. Components can simply ask the sandbox module, which in turn, asks the core module to provide the functionality.

* **Note that any changes to the sandbox module should still honor the previous contract between this module and the application's components.**

* The components in our application only know about the sandbox module and are not allowed (or able) to directly communicate with any other pieces of the application.

* Sandbox makes sure that protected areas of the framework are not accessible by the components through its interface. This enables us to control the type of operations that the components are permitted to perform, within the context of the core and other modules of the application.

* The components in our application only know about the sandbox module and are not allowed (or able) to directly communicate with any other pieces of the application.

* Sandbox module is the only route of communication between the components and the rest of the application.

* It is also through the sandbox module that components can subscribe to and publish custom events in the application.

* **It is important for the components to only have one route of communication with the rest of the application so we can preserve the integrity of our modular design.**

* Sandbox should only provide the functionality of the core module that we want to expose to the components.

* **It is always a good idea to do some error checking at the sandbox level before getting the core module involved.**

* When a sandbox module is created, it sets a context object for its component. Components can use this context object to easily refer to the correct execution context when needed.

* We can use each instance of the sandbox module to preserve a reference to the component that the sandbox instance belongs to. 

## Things to avoid in a sandbox

* It doesn’t contain business logic.
* It doesn’t contain presentation logic, like routing etc.
* It doesn’t do HTTP calls directly, it delegates to http services.

## Why using multiple sandbox instance

* It a better idea to use multiple instances of the sandbox module (one for each component) as opposed to having all the components use the same sandbox object as a singleton. The reason for this is: better isolation and performance.

* Module provides its exposed functionality to the outside world through a single common interface. Create multiple instances of the sandbox module and, to be more precise, one instance per component.

* This would mean that, if one of our components does something undesirable which could cause issues in its sandbox instance, such a mess would be contained within that sandbox module instance. The adverse effects will only impact the functionality of that component but no other sandbox instances, or any other components for that matter.

## Sandbox constructor

* You create objects using this constructor, and you also pass a callback function, which becomes the isolated sandboxed environment for your code.

  ```js
  new Sandbox(function (box) {
   // your code here...
  });
  ```

  The object box will have all the library functionality you need to make your code work.

* The Sandbox() constructor can accept an additional configuration argument (or arguments) specifying names of modules required for this object instance. We want the code to be modular, so most of the functionality Sandbox() provides will be contained in modules.

  ```js
    Sandbox(['ajax', 'event'], function (box) {
     // console.log(box);
   });
  ```

  Another example of that is:
  
  ```js
  Sandbox('ajax', 'dom', function (box) {
   // console.log(box);
  });
  ```

* Using a wildcard `*` argument to mean use all available modules. When no modules are passed, the sandbox will assume `*`. So two ways to use all available modules will be like:

  ```js
  Sandbox('*', function (box) {
   // console.log(box);
  });
  ```
  
  ```js
  Sandbox(function (box) {
   // console.log(box);
  });
  ```

## Adding sandbox module

* The Sandbox() constructor function is also an object, so you can add a static property called `modules` to it.

* This property will be another object containing key-value pairs where the keys are the names of the modules and the values are the functions that implement each module:

  ```js
  Sandbox.modules = {};

  Sandbox.modules.dom = function (box) {
   box.getElement = function () {};
   box.getStyle = function () {};
   box.foo = "bar";
  };

  Sandbox.modules.event = function (box) {
   // access to the Sandbox prototype if needed:
   // box.constructor.prototype.m = "mmm";
   box.attachEvent = function () {};
   box.dettachEvent = function () {};
  };

  Sandbox.modules.ajax = function (box) {
   box.makeRequest = function () {};
   box.getResponse = function () {};
  };
  ```

* The functions that implement each module accept the current instance box as a parameter and may add additional properties and methods to that instance.

## Implementing sandbox constructor

* There should check whether this is an instance of Sandbox and if not (meaning Sandbox() was called without new), we call the function again as a constructor.

* You can add properties to this inside the constructor. You can also add properties to the prototype of the constructor.

* The required modules can be passed as an array of module names, or as individual arguments, or with the * wildcard (or omitted), which means we should load all available modules.

* When we know the required modules, we initialize them, which means we call the function that implements each module.

* The last argument to the constructor is the callback. The callback will be invoked at the end using the newly created instance. This callback is actually the user’s sandbox, and it gets a box object populated with all the requested functionality.


```js
function Sandbox() {
 // turning arguments into an array
 var args = Array.prototype.slice.call(arguments),
 // the last argument is the callback
 callback = args.pop(),
 // modules can be passed as an array or as individual parameters
 modules = (args[0] && typeof args[0] === "string") ? args : args[0],
 i;
 // make sure the function is called
 // as a constructor
 if (!(this instanceof Sandbox)) {
 return new Sandbox(modules, callback);
 }
 // add properties to `this` as needed:
 this.a = 1;
 this.b = 2;
 // now add modules to the core `this` object
 // no modules or "*" both mean "use all modules"
 if (!modules || modules === '*') {
 modules = [];
 for (i in Sandbox.modules) {
 if (Sandbox.modules.hasOwnProperty(i)) {
 modules.push(i);
 }
 }
 }
 // initialize the required modules
 for (i = 0; i < modules.length; i += 1) {
 Sandbox.modules[modules[i]](this);
 }
 // call the callback
 callback(this);
}
// any prototype properties as needed
Sandbox.prototype = {
 name: "My Application",
 version: "1.0",
 getName: function () {
 return this.name;
 }
};
```

# Implementing sandbox static members

* Static properties and methods are those that don’t change from one instance to another.

* Public static member, which can be used without having to create an instance of the class.

* Private static members are not visible to the consumer of the class but still shared among all the instances of the class.

### Public static members

```js
// constructor
var Gadget = function () {};

// a static method
Gadget.isShiny = function () {
 return "you bet";
};

// a normal method added to the prototype
Gadget.prototype.setPrice = function (price) {
 this.price = price;
};

// calling a static method
Gadget.isShiny(); // "you bet"

// creating an instance and calling a method
var iphone = new Gadget();
iphone.setPrice(500);
```

* Sometimes it could be convenient to have the static methods working with an instance too. This is easy to achieve by simply adding a new method to the prototype, which serves as a façade pointing to the original static method:

  ```js
  Gadget.prototype.isShiny = Gadget.isShiny;
  iphone.isShiny(); // "you bet"
  ```

* In such cases you need to be careful if you use this inside the static method. When you do Gadget.isShiny() then this inside isShiny() will refer to the Gadget constructor function. If you do iphone.isShiny() then this will point to iphone.

* One last example shows how you can have the same method being called statically and nonstatically and behave slightly different, depending on the invocation pattern. Here instanceof helps determine how the method was called:

  ```js
  // constructor
  var Gadget = function (price) {
    this.price = price;
  };

  // a static method
  Gadget.isShiny = function () {
   // this always works
   var msg = "you bet";
   if (this instanceof Gadget) {
     // this only works if called non-statically
     msg += ", it costs $" + this.price + '!';
   }
   return msg;
  };

  // a normal method added to the prototype
  Gadget.prototype.isShiny = function () {
   return Gadget.isShiny.call(this);
  };
  
  Gadget.isShiny(); // "you bet"
  
  var a = new Gadget('499.99');
  a.isShiny(); // "you bet, it costs $499.99!"
  ```

### Private static members

* Shared by all the objects created with the same constructor function.

* Not accessible outside the constructor.

```js
var Gadget = (function () {
 // static variable/property
 var counter = 0;
 // returning the new implementation
 // of the constructor
 return function () {
 console.log(counter += 1);
 };
}()); // execute immediately
```

* The new Gadget constructor simply increments and logs the private counter.

```js
var g1 = new Gadget(); // logs 1
var g2 = new Gadget(); // logs 2
var g3 = new Gadget(); // logs 3
```

```js
// constructor
var Gadget = (function () {
 // static variable/property
 var counter = 0,
 NewGadget;
 // this will become the
 // new constructor implementation
 NewGadget = function () {
 counter += 1;
 };
 // a privileged method
 NewGadget.prototype.getLastId = function () {
 return counter;
 };
 // overwrite the constructor
 return NewGadget;
}()); // execute immediately
```

```js
var iphone = new Gadget();
iphone.getLastId(); // 1
var ipod = new Gadget();
ipod.getLastId(); // 2
var ipad = new Gadget();
ipad.getLastId(); // 3
```
