---
title: JPA/Hibernate - Avstå!
theme: blood
revealOptions:
    transition: fade
    transitionSpeed: fast    
---


---

## JPA / Hibernate

# _Avstå!_ <!-- .element: class="fragment" --> 



---

## Hva er Hibernate?

---

## Applikasjonskode

Javaobjekter <!-- .element: class="fragment" data-fragment-index="2"--> 

# Hibernate <!-- .element: class="fragment" data-fragment-index="1"--> 

SQL og resultater <!-- .element: class="fragment" data-fragment-index="3"--> 

## Database


---

## Impedance mismatch

:fear: :scream:





---

<!-- .slide: data-background="./img/most-interesting-man.jpg" -->

## I don't always create Java programs,

## but when I do, I always use Hibernate


---

```java
public class Konto {
    
    private long id;

    ...
}
```

---

```java
@Entity
@DynamicUpdate
@Table(name = "KONTO")
@Inheritance(strategy = InheritanceType.JOINED)
public class Konto {

    @Id
    @GenericGenerator(name = KONTO_SEQ, strategy = "org.hibernate.id.enhanced.SequenceStyleGenerator", parameters = {
            @Parameter(name = "sequence_name", value = "KONTO_SEQ"),
            @Parameter(name = "initial_value", value = "1000"),
            @Parameter(name = "increment_size", value = "100"),
            @Parameter(name = "optimizer", value = "org.hibernate.id.enhanced.PooledOptimizer") })
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "KONTO_SEQ")
    @Column(name = "ID")
    private long id;

    ...

}
```
