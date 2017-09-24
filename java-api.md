# The Java API

## Data Types

### Primitive Data Types
- 4 number types: all are signed, "widening conversion" from lower- to higher-bit representations possible
  * byte: 8-bit
  * short: 16-bit
  * int: 32-bit
  * long: 64-bit
- 2 floating-point number types: scientific notation, used to represent real numbers with significand and exponent => distance between two representable numbers is NOT uniform
  * float: 32-bit
  * double: 64-bit
- character: Unicode character 16-bit, e.g. '\u0000' is hexadecimal notation for
- boolean: 1-bit

### Boxed Primitive Types
- Objects around primitive types
  * parseSomeType(String representation): Creates the primitive type from a string representation, possibly throwing exceptions (static)
  * valueOf(String representation), valueOf(byte|short|int|double someValue): Creates the boxed primitive type from a string or primitive representation (static)
  * compare(..): Compares two primitive types, returning { negative, zero, positive }
  * byte|short|int|doubleValue(): Converts boxed primitive to desired primitive number type

## java.lang

### Math
- Only static methods used for mathematical computations, typically uses double|float

### Strings
- String
  * String is an immutable class; once created, never changed
  * Since it's immutable strings can be shared, e.g. by multiple threads
  * The string constant pool in the heap reuses string literals of the same content
- StringBuffer
- StringBuilder

### System classes
- Class
- ClassLoader
- Process
- ProcessBuilder
- Runtime
- SecurityManager
- System
  * Configuration is done through command line arguments, properties or environment variables
  * Properties: Extends HashMap; offers load(someSource), get(someKey) and a copy constructor Properties(someDefault)
  * 

## java.util

### Arrays
* sort()
* parallelSort()
* fill()
* equals(foo, bar): Compares arrays
* copyOf(int n): Returns an array with the first n elements copied
* copyOfRange(int): Returns an array range (inclusive from, exclusive to)
* binarySearch(): TODO
* deepEquals()
* deepToString()
* stream()

## java.util.collection

### Collection
Traversial with foreach-Loop, iterator() or forEach(), add(), remove(), size(), containsAll(), addAll(), removeAll(), retainAll(), clear()

### Set
* equality: Sets are equal if they contain exactly the same elements (equal elements)
* Interface like Collection
* Especially the xxxAll() operations are useful
  - Set union: a.addAll(b) => a contains union
  - Set intersection: a.retainAll(b) => a contains intersection
  - Set difference: a.removeAll(b) => a contains difference
* Standard implementation: HashSet
* Other implementations: TreeSet (sorted), LinkedHashSet (keeps insertion order)

### List
* equality: Lists are equals if they contain exactly the same elements in the same order (equal elements)
* creation:
  - Can be done with Arrays.asList() which returns a special ArrayList implementation (Arrays$ArrayList instead of ArrayList)
  - This implementaiton does NOT copy the array provided (faster, no array copying), but not all operations are supported, e.g. add() will throw exception
  - new ArrayList<>(Arrays.asList(...)) is most common idiom to create a new list
* Additional interface:
  - Indexed access: get(index), ...
  - Search
  - Advanced iteration (forward, backward, ...)
  - Range-view
  	* subList() returns a view of the backed list (implementation is ArrayList$SubList)
	* ArrayList$SubList should be transient since it can change the backed list, e.g. clear() removes view from backed list
	* This is useful when incrementally removing parts from a list
	* subList().indexOf(someEl) gives the index in the view
    * Standard implementation: ArrayList (array resizing list)
    * Other implementations: LinkedList (good in special cases, e.g. insertion at end to prevent array resizing)

### Queue
* Insertion / Removal / Inspection in two flavors: with exception, without exception (but special value): add(), offer() / remove(), poll() / element(), peek()
* LinkedList is standard implementation

### Deque
* Allows both ends with additional xxxFirst(), xxxLast() methods
* ArrayDeque is standard implementation

### Map
* Associative array, key-value store; models a function
* Basic operations: put(), get()
* Bulk operations: putAll()
* Collection views
  - keySet(): Returns the set of keys
  - values(): Returns a collection of values
  - entrySet(): Returns the set of entries (key plus value)
* The collection views allow removal operations on the backing map, but no add operations => use put(), putAll()
* Standard implementation: HashMap (offers all optional operations and null keys / null values)

### Comparision
*Comparable* interface for the natural ordering of a (value) object, e.g. Foo implements Comparable<Foo>

*Comparator* is (ad-hoc) class that implements *Comparable*

Sorting in reverse or custom order: Comparator's reversed(), Comparator.comparing(lambda) or Collections.sort(data, lambda)

### Sorted Collections
- SortedSet, e.g. TreeSet
  * Range-views: subSet(T fromValueInclusive, T toValueExclusive), headSet(T toValueExclusive), tailSet(T fromValueInclusive)
  * Endpoints: first(), last()
  * Comparator access: comparator()

- SortedMap, e.g. TreeMap
  * Range-views: subMap(T fromKeyInclusive, T toKeyExlusive), headMap(T toKeyExclusive), tailMap(T fromKeyExclusive)
  * Endpoints: firstKey(), lastKey()
  * Comparator access: comparator()
  
## java.time

There are three types of time bound abstraction
- Absolute: Absolute UTC time 
  * Instant: Point on the absolute UTC time line
- Local: Date and/or time not bound to a time-zone
  * LocalDate
  * LocalTime
  * LocalDateTime
- Zoned: Date and/or time bound to a time-zone
  * ZonedDateTime
    + withZoneSameInstant(): Same instant in another time-zone
    + withZoneSameLocal(): Same local datetime in another time-zone (different instant)
  * ZoneId: Time-zones
- Intervals
  * Duration
    + Interval expressed in nanos / seconds WITHOUT date-semantics
  * Period
    + Intervals expressed in days / hours WITH date-semantics like leap hour adjustment
- Adjustments
  * Temporal
    + Interface for date/time scales, consists of a set of TemporalUnits
    + Date/time scale has a plus(int amount, TemporalUnit unit) and minus(...), with(...) semantics
    + Examples: LocalTime, LocalDate, LocalDateTime, ZonedDateTime, ...    
  * TemporalAdjuster
    + @FunctionalInterface that offers Temporal adjustInto(someTemporal)
    + Strategy to return a transformed Temporal; implemented as lambda
    + someTemporal.with(someTemporalAdjuster) returns an adjusted Temporal
    + Use TemporalAdjusters for common adjustments    
    
  
