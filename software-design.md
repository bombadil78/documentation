# Software Design

## Design Patterns

### Creational
- Static factory method: No subclass, give construtor a name
- Singleton: Only one instance
- Factory: Use name to retrieve a product
- AbstractFactory: Use name to retrieve a factory that creates products
- Builder: Object with a lot of optional parameters in constructor

### Structural
- Decorator / Wrapper:
- Adapter: 
- Flyweight: 

### Behavioral
- Iterator: Abstract navigation on data structure from data structure
- Decorator / Wrapper: 
- Visitor: Double dispatch; combine fixed hierarchy with flexible class hierarchy
- Observer / Observable: Register interested clients
- Strategy: Abstraction for an algorithm, e.g. sorting
- Chain of Responsibility: Abstract single logical step and process list of responsibilities
- Template method: Programming by difference for an algorithm; subclass only implements specific

## Principles

### SOLID
- S: Single responsibility principle = A class should serve a single purpose
- O: Open-closed principle = Open for extension, closed for modification
- L: Liskov substitution principle = Subtyping
- I: Interface segregation principle = Create "minimal" interfaces such that implementor only need to implement what is used
- D: Dependency inversion principle = Client code should depend on interfaces, not on concrete classes
