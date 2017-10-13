# Java JPA

JPA stands for *Java Persistence API* and offers a way to handle the persistence life-cycle for Java objects. 
 
JPA includes a specification on how to map Java classes to database tables (ORM) and offers an API to access or persist objects. 

JPA is a specification an there exist several implementations of it, e.g. Hibernate or EclipseLink.

## Basics

### EntityManager
This is the main abstraction an application uses to manage the persistence life-cycle of other classes. The to-be-persisted classes are called *entities*, the *EntityManager* manages those. 
 
An entity manager is always created using an *EntityManagerFactory* which in turn takes its configuration from a *persistence.xml* configuration file.

The life-cycle of the entity manager can be managed by the application or by the container.

```
// 1) Application-managed
EntityManager em = Persistence
	      .createEntityManagerFactory("my-persistence-unit")
	      .createEntityManager();
// do some stuff
em.close();
```

```
// 2) Container-managed and transaction-scoped
@PersistenceContext(unitName = "my-persistence-unit")
private EntityManager em;

// The container manages the life-cycle of the EntityManager
// No need to close it manually
```

The second approach is only applicable when deployed on application servers. From the API: 

> Expresses a dependency on a container-managed EntityManager and its associated persistence context
 
In detail:
- The EntityManager will be created using a factory configured by "my-persistence-unit"
- The EntityManager is container-managed => TODO

### PersistenceUnit
A *PersistenceUnit* is a logical grouping of persistable classes. It has the following properties:
* Name
* Description
* Provider: The class used for the JPA implementation
* Properties: Connection properties like username, password or database driver

The file *persistence.xml* is used to configure one or more EntityManagerFactories which in turn can create EntityManagers.

### PersistenceContext
The *PersistenceContext* is


## Transactions
Managed Entities
- Which objects should be managed by the container? Which ones not? Objects that hold state in a controller? Value objects? Utility classes? What about @Default?

Database Transactions
There are two approaches to deal with database transactions:
* Bean-managed: The application is responsible for database transaction demarcation and does this explicitly; transitive calls on other beans must be handled manually; only if really needed
* Container-managed & transaction-scoped: Container have a concept of JTA transaction that can be used to also model database transactions. JTA offers annotations on classes and methods to signal to what degree transactions are nested.

Transactions can occur at different levels: database-level transactions or JTA transactions on a higher level. There exist different approaches:

Either the application is responsible for the transaction

TODO:
- JTA Hello World (independent from persistence)

# References
- Overview: http://www.kumaranuj.com/2013/06/jpa-2-entitymanagers-transactions-and.html
- In Depth: Pro JPA 2 Book

