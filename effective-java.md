# Effective Java

## Creating and Destroying Objects

### Item 1 - Use static factory method to create instances
* Can give a useful name (not only using the class name)
* Can express what creation does, i.e. when there are multiple parameters. Constructor with different lists of parameters is very hard to remember for Clients.
* Can return the same instance if needed ("Flightweight pattern" to save space)
* Can return different subtypes of an abstract class/interface with static factory method (constructor only ever returns Objects of the same type), e.g. useful to encapsulate API
* Objects to be created must have at least a protected (or public) constructor, otherwise they cannot be subclassed
* No obvious difference to other static methods that "show" that they are used for object creation (Constructor is obviously used for construction)

### Item 2 - Use builder for classes with many constructor parameters
* Avoids telescoping parameters => readability for clients is impaired
* Avoids JavaBeans construction with empty constructor followed by a sequence of setters => checking of constraints is hard, objects might be in inconsistent states
* Builder emulates optional named constructor parameters, i.e. Word w = Word.Builder(mandatory-params).someOptionalParam(val).someOtherOptionalParam(val).build();

### Item 3 - Use Enum to implement Singletons
* Old approach #1: Constant and private constructor
* Old approach #2: Private constant, private constructor and static factory method (Singleton'ness can be unmade)
* Both approaches lack direct support of implementing Serializable
* Preferred approach: Use an enum with a single value INSTANCE

### Item 4 - Uninstaniable classes should use private constructors
* Utility classes like java.lang.Math or java.util.Arrays cannot be instantiated
* Use private constructor and throw an exception in it
* Comment the constructor
* The class cannot be instantiated inside the class
* The class cannot be subclassed due to a lack of a (at least protected) constructor

### Item 5 - Avoid creating unnecessary objects
* If class is immutable there is no reason not to reuse. Classical example: Use string literals, not new String("foo")
* Use constants and a static initializer instead of creating new instances (e.g. Calendar is expensive)
* Beware of autoboxing, e.g. adding and int to a sum of Integer creates a new object
* Use flightweight pattern / own object pool only where creation is REALLY expensive because of additional complexity

### Item 6 - Avoid "Memory Leaks"
* There are no classical memory leaks; gc takes care of object that are not referenced any more
* Java memory leaks => obsolete object references
* Sources of obsolete object references
  - Maintaining an own memory management, e.g. resizeable stack, dynamic array sizes
  - Caches that are never cleaned up
  - Listener registration without de-registration can accumulate
* Consider using WeakHashMap
* Memory leaks can be inspected with a Heap Profiler

### Item 7 - Avoid finalizers
* Is called when garbage collector cleans up an object
* Problem: Its hard to tell if and when the garbage collector is called (depends on JVM implementation)
* Problem: If uncaught exception is thrown in finalize() it can leave the object in a corrupt state
* Use explicit clean-up method to clean up resources once object is not in use any more, e.g. close(), ...
* Objects with clean-up methods should hold internal state and throw an exception if clients want to reuse it, e.g. read()
* Clients should use try {} finally {} pattern to enforce that clean-up call is always made
* Only use as safety net if clients forget to call the clean-up method or for native peers w/o critical resources

### Item 8 - Obey the equals() contract
* Reflexive: x.equals(x) == true
* Symmetric: x.equals(y) == y.equals(x)
* Transitive: x.equals(y) && y.equals(z) => x.equals(z)
* Consistent: If data relevant for logical equality is unchanged so is the value of subsequent equals() calls (immutable classes: result is always the same)
* Its easy to break equals(), e.g.
  - Break symmetry: Implementations c1::equals(Object o with c2) and c2::equals(Object o with c1) must always return the same value
  - Break transitivity: Let c, sc be class and subclass. If sc's equals() takes into account sc's additional fields if both object are of type sc and otherwise only c's fields then comparing sc1 to c and c to sc2 returns true, but comparing sc1 to sc2 returns false
* Recipe for implementing equals()
  - Introduce == check: If compared to self return true (fast-success)
  - instanceof check: If class fails return false (fast-fail, also handles null case). Remark: Subclasses are allowed!
  - Cast to correct type
  - Equality check for each "significant" field
    * Primitives w/o long or float: Use ==
    * long or float: Use Long.compare, Float.compare
    * Objects: Return false if one is null and the other not, otherwise use equals() recursively

### Item 9 - If you override equals() then override hashCode()
* hashCode() is used in HashMaps, HashList, ... to distribute objects to buckets
* By default hashCode() is related to the memory address (because default equals() is ==)
* One must override hashCode() if equals() changes because HashMaps otherwise search for equal objects in unequals buckets and cannot find previously added objects (!)
* Recipe for implementing hashCode():
  - Take initial value (random) as result
  - For every significant field used in the equals() contract
    * For primitive fields generate some integer
    * For Objects take hashCode()
    * For arrays take Arrays.hashCode()
    * Multiply the previous result by an odd prime and add the value for the significant field. This makes the order of the values relevent (!)

### Item 10 - Always override toString()
* The class is much more useful with a self-explanatory description of its instances
* Optional: Implement string to object conversion

### Item 11 - Be cautious when implementing clone()
* native Object.clone() creates a clone of the run-time type of the object using reflection. Remark: It is impossible using constructors since this would bind to a compile-time type
* Object.clone() must be called when overriding clone() AND the whole class hierarchy must not break that clone() calls super.clone()
* Object.clone() copies primitive fields and makes shallow copies of objects and arrays
* Cloneable is a marker interface that prevents Object.clone() from throwing CloneNotSupported exceptions
* To implement clone
  * Implement Cloneable
  * Add a public clone method (counts as override because public contains proteced)
  * Cast to the concrete type and return it (counts as override because of covariant return types)
  * Optional: Fix problems like deep object copies, ...
* Better solution: Provide copy or conversion constructors  

### Item 12 - Consider implementig Comparable
* Used to express a natural ordering of objects
* Used by the sorted versions of collections (set, map)
* Comparable<T>: compareTo(T other) returns -1 if other is >, returns 1 if other is <, returns 0 if other is =
* Must be reflexive, anti-symmetric, transitive and should be in sync with equals (not a hard requirement)
* Similar to equals(): Comparing objects of different type might break anti-symmetry or transitivity or checking for exact type breaks liskov

### Item 13 & 14 - Information Hiding
* Class level visibility: package-private (= only in package), public (= from everywhere)
* Method level visibility: private (= only in class), package-private (= only in package, not even subpackages), protected (= package OR subclass), public (= from everywhere)
* Always choose the most restrictive possible access level; public classes and public/protected members are part of the API -> once exposed then code might rely on it
* Reason for this is decoupling of subsystems which allows independent development, testing, optimization, ...
* Instance members must never be public otherwise the class has no more control over the
  * Non-final / mutable: Client can exchange reference or change underlying object
  * Final / mutable: Client cannot exchange reference but change underlying object
  * Non-final / immutable: Client can exchange reference
  * Final / immutable: Client cannot change anything but it's code might depend on it, the field cannot be savely removed without breaking clients
* Non-empty arrays are treated as objects
* It's OK to expose constants if they are part of the API
* Use setter: Class can enforce invariants on the data and avoid leaking references into the class
* Use getter: Class can avoid leaking references out of the class by returning copies

### Item 15 - Minimize mutability
* Invariants of the class are established at creation and hold for the rest of the life of the object
* Objects can be shared, also among threads
* It is easier to reason about immutable objects than about mutable objects
* Recipe:
  * No mutator methods
  * No extension (final class or static factories with private constructors)
  * Only private and final fields
  * Exclusive access to any mutable components

### Item 16 - Favor composition over inheritance
* Implementation inheritance establishes a dependency between subclass and superclass. This can be critical because subclass must evolve together with superclasses.
* Only use inheritance if subtyping exists
* For classes that cross package border: Only use inheritance if classes are designed for inheritance
* Use wrappers instead of inheritance

### Item 17 - Design classes for inheritance or prohibit it
* Document concrete implementation, document every self-use of overridable methods (non-final public or non-final protected) in superclass
* Documentation is needed such that subclasses are aware of possible side-effects of overrides
* Overridable methods in constructors or other overrideable methods can break subclasses

### Item 18 - Prefer interfaces to abstract classes
* Abstract classes depend on class hierarchies -> If two classes have a new type a common ancestor must be found -> has side effects on other classes
* Interfaces do not depend on class hierarchies -> New types can be "mixed-in" withou side effects
* Interfaces are contracts and should be stable, but do not provide implementation
* Abstract classes can accompany an interface and are useable by clients ("skeletal implementation")
* Clients that choose to implement interfaces can do this manually or use the abstract classes internally (inner class, anonymous class) -> simulated multiple inheritance
* Evolving interfaces is hard because clients rely on them - evolving abstract classes is easy, because clients should not rely on them

### Item 19 - Use interfaces only to define types
* Don't create interfaces that hold constants & have their clients implement it -> introduces dependency, use of constant is not part of the API but something internal
* Use an non-instantiable utility class for constants and static import (avoid name qualification) in clients that make use of the constants

### Item 20 - Prefer class hierarchies to tagged classes
* Tagged class = class with instance variable that expresses the type and switch statements to test for the type
* Tagged classes are
  * Hard to read and maintain: Changes are cluttered all over the possibly huge class
  * Error-prone: Missing switch can cause runtime damage
  * Inefficient: Every instance hold data for every other type

### Item 21 - Use function objects to represent strategies
* Function objects or also lambdas are to be used for the strategy pattern, e.g. List<T>::sort(SortingStrategy<T> strategy)

### Item 22 - Favor static inner classes over non-static
* Non-static inner classes have a reference to the enclosing instance: More memory footprint, more complexity
* Typical use of non-static inner classes
  * iterator() that implements Iterator<T> for some iterable data structure
  * Clients will create new iterators that access the same underlying instance
  * It is sufficient to declare the class as inner class (not in the package) because this expresses the dependency

### Item 23 - Don't use raw types in new code
* Raw types introduce casts in the code e.g. when reading from a list of some type, which can result in ClassCastException if the content was wrong. This error is only detected at runtime
* Parametrized collection types enforce a type when inserting and add automatic casts when retrieving information from the collection. The underlying type is still the raw type because of backwards compatibility
* Backward compatibility requirement: List and List<E> had to interoperate in both ways. Therefore the following subtyping rules were introduced
  * List<E> is a subtype of List (raw type)
  * List (raw type) is a subtype of List<?>
* Exceptions:
  * Access generic type information at runtime
    * Class name: One must use raw types when referring to the class of a generic type because List<String>.class does not exist any more; it was erased by the compiler
    * instanceof: One must use raw types in instanceof and then cast to the unbounded wildcard type, e.g. Set<?>
  
### Item 24 - Eliminate unchecked warnings
* Eliminate unchecked warnings that can be eliminated. In exceptional cases always use the annotation @SuppressWarnings("unchecked") to silence the compiler
* Use the smallest scope possible for the annotation, typically parameter declaration or method level

### Item 25 - Prefer lists to arrays
* Generic types
  * Invariant
  * Non-reifiable ("nicht-konkretisierbar") because of type erasure; compiler erases to the upper bound and adds casts, so at runtime the type is gone
  * No runtime-checks
  * Wildcards and (bounded) type parameters are used to write code for multiple generic types, invariance is strict
* Arrays
  * Covariant
  * Reading from an array is always fine because one can be sure that the content is of the supertype
  * Writing to an array might throw an ArrayStoreException because the runtime type of the array might contradict to what is written to it
  * Reifiable ("konkretisierbar"), so class, instanceof, ... are working
  * Runtime checks when writing to arrays are introduced => "ArrayStoreException", e.g. Object[] arr = new Long[1]; arr[0] = "boom"; // Throws ArrayStoreException
* Mixing non-reifiable with arrays is prohibited at compile-time because they are not safe

### Item 26 - Favor generic types
* Sometimes code is made generic and mixes with arrays, e.g. Stack implementation
* It's ok to suppress compiler warnings if type safety can be proven, typically the following options exist
  * Use array of generic type in constructor, i.e. stack = (E[]) new Object[STACK_CAPACITY]
  * Use array of objects and add a cast in pop(), i.e. (E) stack[--position]
* Type parameters appear at class level or method level
* (Bounded) wildcard types appear
  
### Item 27 - Favor generic methods
* Bounded type parameters
  * Upper bounds are used to constrain parameters, e.g. T extends Foobar
* Recursive type bound
  * Type parameters that have bounds that are generic types, e.g. T extends Comparable<T>
* Safer & easier for clients, no casts needed

### Item 28 - Increase API flexibilty by using wildcards
* Generic types are invariant, sometimes we want methods that can handle both List<X> and List<Y>
* Bounded wildcard types use "? extends|super someType" to allow types or methods for multiple generic types
* PECS = Producer-Extends, Consumer-Super means
  * Assignment (writing): T t = ? => ? extends T <= Producer
  * Assignment (reading): ? = t => ? super T     <= Consumer
  * Method calls: ?. => ? extends T              <= Producer
  * Method parameters / wildcard is argument: o.bar(?) => ? extends T           <= Producer
  * Method parameters / other is argument: ?.foo(o) => ? super T                <= Consumer

### Item 29 - Consider typesafe heterogenous containers
* If an unbounded number of type parameters is needed then use generic methods that fill maps using the class of the type parameter as key

## Enums and Annotations

### Item 30 - Use enums instead of int constants
* Compile-time type-safety; clients cannot provide incorrect int values
* Reordering does NOT break a client
* Has a meaningful toString() representation
* Clients can access all elements in the set of constants using values()
* Rich enum types = Constants with (derived) constants, e.g. Planet -> mass, radius
* Possibility to add (abstract) methods and override them for a single enum value
* a) Same behavior related to the data of an enum ("") VS. b) different behaviors related to the data of an enum are both possible
* Instead of switching on enum values use a strategy through another enum. Example: PayRollDay -> PayType

### Item 31 - Don't use ordinal()
* ordinal() exposes the internal int representation of the enum
* Dependency is increased and if clients rely on ordinal() then enums cannot be safely changed
* Use instance variables instead

### Item 32 - Use EnumSet for sets of enums
### Item 33 - Use EnumMap for maps with enum as key
### Item 34 - Use interface to extends enums

### Annotations Tutorial
* Annotations are meta-data on the source code and have no direct impact on the source code, but can be used in the following way:
  - Compiler can analyze the code and emit warnings / errors
  - Tools can analyze the code, generate new code, instantiate objects, ...
  - Typical use cases are:
    * Source code is analyzed & reflection API is used to create objects, ...
    * Same on byte-code level when interpreted by JVM
* Predefined annotations
  - @Override: Annotated method must be a valid override
  - @Deprecated: If method is used then the compiler issues a warning since method should not be used any more
  - @SuppressWarnings: Suppress any warnings. There exists "deprecation" and "unchecked".
  - @SafeVarargs: Indicates that no unsafe operations are done on the varargs arguments of the annotated constructor or method
  - @FunctionalInterface: Marks an interface as a functional interface (to be used with lambdas)
* Meta annotations:
  - @Retention:
    * RetentionPolicy.SOURCE: Only kept at source level, ignored by compiler and JVM
    * RetentionPolicy.CLASS: Kept at source and class level, ignored by JVM
    * RetentionPolicy.RUNTIME: Kept up to runtime level, can be inspected by runtime-environment
  - @Documented: Must be documented with Javadoc
  - @Target: What are valid targets for this annotation, e.g. method, class, ...?
  - @Inherited: Is the annotation inherited to subclasses (default = NO)?
  - @Repeatable: Can one repeatably apply the annotation?
* XML vs. Annotations
  - XML: For global / aspect-oriented / cross-concern configuration, no recompilation allowed
  - Annotations: If context matters, if type-safety matters (XML is not typesafe)

### Item 35 - Prefer annotations to naming patterns
### Item 36 - Always use @Override
### Item 37 - Use marker interface instead of annotation on a type

### Item 38 - Check parameters for validity
- Throw NullPointer/IndexOutOfBounds/IllegalArgumentException to check for preconditions on parameters of public methods (API)
- Use assert for non-public methods to check preconditions (postconditions, class invariants are also possible)

### Item 39 - Make defensive copies when needed
- If mutable components leaking into or out of a class exist then one should copy defensively, otherwise clients can change the internal state of the class.
- Final is NOT enough if the component itself is mutable, e.g. a date can be changed using someDate.setYear(1978)
- Defensive copies should be made before preconditions are checked (another thread could change the components after they were checked otherwise)
 
### Item 40 - Design method signatures carefully
- Choose method name carefully
- Not too many methods in an interface or class
- Avoid long parameter lists (max. 4)
- Use interface types as parameters (not concrete implementations)
- Use two-element enums instead of boolean flags

### Item 41 - Overload judiciously
- Relevant for methods and constructors
- Do not overload if parameter list has the same length unless the calls are incompatible even with casts with each other, e.g. foo(int i, int j) and foo(Bar bar, Bar bar2) is OK
- If in a retrofitting scenario the above rule has to be violated then make sure all implementations behave the same

### Item 42 - Use varargs judiciously
- Varargs create an array at runtime (=> costs)
- Only use for methods that really have variable-length parameter
- If a vararg is only valid if it contains one or more elements consider foo(int first, int... rest) for a type-safe solution without parameter checks
- If performance is an issue use fixed length to cover the basic cases and varargs for the non-basic cases to avoid runtime costs of array creation

### Item 43 - Return empty array or collections, not nulls
- Never return null to signal an empty collection
- Client should not have to introduce null checks
- If array or collection is empty AND defensive copying is used then use a constant, e.g. Collection.emptyList() or a static empty array

### Item 44 - Document APIs using JavaDoc

### Item 45 - Minimize the scope of local variables

### Item 46 - Prefer for-each loops

### Item 47 - Know the API
- The must-knows are: java.lang, java.util including Collections, java.io (to lesser extent)

### Item 48 - Avoid float and double for exact answers

### Item 49 - Prefer primitive types to boxed types
- Primitives only have value, boxed types have identity (but may have the same primitive value)
- Auto-unboxing: Primitive type => boxed primitive type, e.g.
  * myInteger > myInteger2 (Integer does not understand ">", so both go to int)
  * myBoxed * myUnboxed (Integer and int mixed, so Integer goes to int)
- Auto-boxing: Boxed primitive type => primitive type, e.g.
  * sum = sum * i (with sum being a Long and i a long, sum * i makes unboxing, assignment makes boxing)
- Auto-unboxing can cause a NullPointerException (if the to be unboxed boxed primitive type is null)

### Item 50 - Avoid strings where other types are more appropriate

### Item 51 - Beware the performance of string concatenation
- String concatenation in a loop is slow; strings are immutable and the concatenated parts are copied
- Use StringBuilder

### Item 52 - Refer to objects by their interfaces

### Item 53 - Prefer interfaces to reflection

### Item 54 - Use native methods judiciously

### Item 55 - Adhere to generally accepted naming conventions

--------------------------
IDIOMS
--------------------------
- Read from console
  *
  * 

- File operations
  * Path
    * get(String... parts): Creates absolute or relative path
    * pathOne.resolve(pathTwo): Combines paths (pathTwo == absolute then pathTwo else pathOne concat pathTwo)
    * pathOne.relativize(pathTwo): Expresses way from one to two as Path (relative)
  * Files
    * readAllBytes(): reads binary data
    * lines(): read Stream of Strings
    *  write(toPath, someBytes), e.g. write(toPath, textAsString.getBytes(StandardCharsets.UTF_8)) to write text data

- Date and time (Java 8)
  * Current time
  * Current day

- Start new threads (Java 8)






