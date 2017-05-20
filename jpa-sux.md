---
title: JPA/Hibernate - Avstå!
theme: blood
revealOptions:
    transition: fade
    transitionSpeed: fast    
---


---

## Java Persistence API

# JPA

---

<!-- .slide: data-background="./img/hugging-kittens2.jpg" -->

Note: presentasjon uten å fornærme noen

---

<!-- .slide: data-background="./img/catpunch2.jpg" -->

Note: kan ta diskusjon etterpå

---

<!-- .slide: data-background="./img/raised-hands.jpg" -->


Note: håndsopprekking
- mange på Java-prosjekter med relasjonsdatabaser?
- bruker dere JPA/Hibernate?
- liker dere JPA/Hibernate?
    - nei: ikke sant? Det er noe tøys!
    - ja: å kom igjen a! Virkelig? Jeg tror dere lyver.

---

<!-- .slide: data-background="./img/blue-shirt-headscratch.jpg" -->

# JPA?

## Sånn helt enkelt forklart

---

## Applikasjonskode

↕️ Javaobjekter ↕️ <!-- .element: class="fragment" data-fragment-index="3"--> 

# JPA <!-- .element: class="fragment" data-fragment-index="1" style="background-image: url(./img/brickwall.jpg); background-size: cover;"--> 

↕️ SQL og resultater ↕️  <!-- .element: class="fragment" data-fragment-index="2"--> 

## Database


---

## Impedance mismatch

## 😨 😱 <!-- .element: class="fragment" data-fragment-index="1"--> 





---

<!-- .slide: data-background="./img/most-interesting-man2.jpg" -->

### I don't always create 
### Java applications,

### but when I do, 
## I <em>always</em> use 
# JPA


---

## Java Persistence API

# Avstå!


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

---

## annotasjoner 

# & <!-- .element: class="fragment" data-fragment-index="2" -->

### suboptimal <!-- .element: class="fragment" data-fragment-index="2" --> <span style="font-size: 0.7em">og lite smidig</span> kode <!-- .element: class="fragment" data-fragment-index="2" -->

---

<!-- .slide: data-background="./img/boxer-sleeping.jpg" -->

# JPA is lazy <!-- .element: style="padding-bottom: 1.2em" -->

## I only fetch when you least expect it

---

### Alternativer?

# SQL               <!-- .element: class="fragment" data-fragment-index="2"--> 
## Micro-ORMs       <!-- .element: class="fragment" data-fragment-index="3"--> 

---

<!-- .slide: data-background="./img/rainbow.jpg" -->

### Alternativer?

# SQL
## Micro-ORMs

---

## Java Persistence API

# Avstå!
