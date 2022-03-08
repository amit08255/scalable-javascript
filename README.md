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
