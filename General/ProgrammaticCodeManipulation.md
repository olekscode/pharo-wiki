# Programmatic Code Manipulation (Pharo 11)

In this article, we will present some code snippets that demonstrate how we can manipulate the source code (packages, classes, methods, variables, traits, etc.) programmatically, without using the SystemBrowser. Those techniques can be very useful for reflective operations, code generation, testing, etc.

This article explains only those operations that **modify** the code. We will write a separate article on **querrying code** (looking for method senders, class references, pragma usages, etc.).

## Packages

### Creating a new package

```st
package := self packageOrganizer ensurePackage: 'PackageName'.
```

In Playground, you can use any object or class instead of `self`:

```st
package := Object packageOrganizer ensurePackage: 'PackageName'.
```

### Removing a package

```st
package removeFromSystem.
```

**Be careful!** In Pharo 11 and earlier, this will not properly remove classes from the system. So make sure to remove all classes before you remove the package.

### Creating a package without installing it

_(how to create an instance of a package without installing it into the image? - e.g., for testing)_

## Classes

### Creating a new class

To create a new class, you can use the same code that appears in SystemBrowser. Here is an example with **Fluid** class definition.

```st
builder := Object << #MyClass
```

The first statement will return an instance of `FluidClassBuilder`. The second statement will build the class, install it into the image, and assign it to the variable. If `MyPackage` does not exist, it will be created.

### Creating a new class without installing it

Here is an example of how you can create a new class without installing it into the system. You can do that by sending `build` message instead of `install` as in the previous example.

```st
builder := OrderedCollection << #MyCollection.
```

This way, we can create two classes that have a same name but do different things. In the example below, we create two classes named `Dog`. One class defines method `bark` to return the string `'Woof, woof!'` (English barking), the other one - `'Ouaf, ouaf !'` (French barking). As you can see below, the instances of those classes do different things. The classes are different but the class names are the same.

```st
dogClass1 := (Object << #Dog) build.

dog1 class = dog2 class. "false"
```

**Be careful!** In Pharo 11, if the class named `Dog` already exists in the system, your new classes will also have the methods that the system class defines. However, the new methods will override the system ones in your new class.

For example, if there already was a class Dog in your image defined as follows:

```st
Object << #Dog
    
Dog >> printOn: aStream

Dog >> bark
```

You can still define the local class as in the example above. In this case, your new class will understand the method `printOn:` from the system class and will define a new method `bark`:

```st
dog1. "I am a dog"
dog1 bark. "Woof, woof!"
```

### Creating an anonymous class

### Removing a class

This will also remove all methods of the class.

```st
class removeFromSystem.
```

## Methods

### Adding a method to a class

To add a new method to a class, you can simply write its source code (including method name and arguments) in a string and send it as argument of a `compile:` method of your class. Here is an example of adding the method `printOn:` to `MyClass` (this is a silly implementation that will print a class name followed by a random number).

```st
sourceCode := 'printOn: aStream

MyClass compile: sourceCode.
```

You can also specify a protocol:

```st
MyClass compile: sourceCode classified: 'printing'.
```

Finally, to avoid the error when generating a code in a fresh image, you should specify the author name. It can be anything, so in this case, we use `'Dummy Name'`

```st
Author 
    useAuthor: 'Dummy Name'
    during: [ MyClass compile: sourceCode ].
```

### Removing a method from a class

```st
method removeFromSystem.
```

## Variables

## Traits

### Adding trait to a class

### Removing trait from a class

### Creating a new trait

### Adding methods to a trait

## Useful links

- A toolkit to generate Pharo code: <https://github.com/juliendelplanque/PharoCodeGenerator>