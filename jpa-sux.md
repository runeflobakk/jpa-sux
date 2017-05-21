---
title: JPA/Hibernate - Avstå!
theme: blood
revealOptions:
    transition: fade
    transitionSpeed: fast    
---

Note: 
- Rune -> utvikler
- Ikke _bare_ dødshipp tech -> traust og velkjent

---

## Java Persistence API

# JPA

Note: JPA
- ev. Hibernate, mest utbredte impl.

---

<!-- .slide: data-background="./img/hugging-kittens2.jpg" -->

Note: 
- presentasjon uten å fornærme noen?
- ingen grep for å unngå det

---

<!-- .slide: data-background="./img/catpunch2.jpg" -->

Note: 
- slag, spark, ev. diskusjon etterpå

---

<!-- .slide: data-background="./img/raised-hands.jpg" -->


Note: Håndsopprekking
- mange Java-prosjekter med DB?
- bruker dere JPA/Hibernate?
- liker dere å bruke JPA?
    - nei: ikke sant? Det er noe tøys!
    - ja: å kom igjen a! Virkelig? Jeg tror dere lyver.

---

<!-- .slide: data-background="./img/blue-shirt-headscratch.jpg" -->

# JPA?

## Sånn helt enkelt forklart

Note: **Hva** er JPA?
- OO-data strukturelt ulikt DB-data, resultater fra DB
- transformere til OO når data leses fra DB
- transformere tilbake til DB ved lagring

---

## Applikasjonskode

↕️ Javaobjekter ↕️ <!-- .element: class="fragment" data-fragment-index="3"--> 

# JPA <!-- .element: class="fragment" data-fragment-index="1" style="background-image: url(./img/brickwall.jpg); background-size: cover;"--> 

↕️ SQL og resultater ↕️  <!-- .element: class="fragment" data-fragment-index="2"--> 

## Database


Note:
- JPA ligger "i mellom"
- rammeverk
- slippe å forholde seg til hvordan data repr. i DB
- kun tenke på data i objektform

---

## Impedance mismatch

## 😨 😱 <!-- .element: class="fragment" data-fragment-index="1"--> 


Note: Skummel og pompøs techy betegnelse
- skremme folk fra SQL, håndskrevet oversetting
- "Vi har en mismatch, dere!"
- flere millioner kodelinjer langt generisk rammeverk
- 2017, komponentbaserte Web-rammeverk abstraherer HTTP -> feil
- gjelder også DB-kommunikasjon


---

<!-- .slide: data-background="./img/most-interesting-man2.jpg" -->

### I don't always create 
### Java applications,

### but when I do, 
## I <em>always</em> use 
# JPA


Note: JPA brukes på autopilot

Men jeg mener, med hånden på hjertet...

---

## Java Persistence API

# Avstå!

Note: **JPA bør du avstå fra å ta inn i appen din!**

- Nøkkelproblem: JPA fungerer greit til enkle case
- Forførersk:
    - tilfredsstillende med annotasjoner på enkle objekter
    - magisk lagret og hentet frem igjen

Snart ser klassene slik ut:

---

```java
public class Konto {
    
    private long id;

    ...
}
```

Note: _Venter litt, ser raksjoner, snur meg_

"Nei, jeg mener slik!"



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

Note:
- Skrekkeksempel: single source of truth, alt i domeneklassene
- persistering: separat problemstilling
- forretningslogikk hører hjemme i domenemodell

JPA, paradoksalt: sterk binding mellom database og domenemodell og forretningslogikk

---

## annotasjoner 

# & <!-- .element: class="fragment" data-fragment-index="2" -->

### suboptimal <!-- .element: class="fragment" data-fragment-index="2" --> <span style="font-size: 0.7em">og lite smidig</span> kode <!-- .element: class="fragment" data-fragment-index="2" -->

Note: **Annotasjoner:**
- hvordan objekter korresponderer til DB
- andre tekniske detaljer

**Designet på koden**
- begrenset
- JPA skal behandle og lese tilstand
- trøblete å bruke språkfeatures


- implisitt bundet til DB
- vanskelig å resonnere ringvirkninger
- domenemodell bør være endringsvillig!
- svære entitetsklasser, vokst over tid
    - subsett av tilstand i ulike kontekster
    - egentlige grunnen til...

---

<!-- .slide: data-background="./img/boxer-sleeping.jpg" -->

# JPA is lazy <!-- .element: style="padding-bottom: 1.2em" -->

## I only fetch when you least expect it 


Note: **Lazy loading**
- kan ikke populere opp all tilstand
- fatalt: ytelse, minnebruk
- anti-pattern: altomfattende "core" modell for alle kontekster
    - lazyloading, teknikk med egne utfordringer
    - for å beholde anti-patternet
- savnet lazy-loading uten JPA?
- utsette uthenting  ikke spør etter de før de trengs

---

### Alternativer?

# SQL               <!-- .element: class="fragment" data-fragment-index="2"--> 
## Micro-ORMs       <!-- .element: class="fragment" data-fragment-index="3"--> 

Note: **Alternativer**

- SQL rett i koden
- egen kode som oversetter mellom DB og OO
- utfordring: JDBC, gotchas
    - Spring JDBC Template
    - forenklende API over JDBC
- full kontroll på spørringer
- når ting brekker: ingen nøsting i annotasjoner -> genererte spørringer
- nært forhold til DB-modell
- vet hvilke og når spørringer gjøres
- utnytte DaB-spesifikk funksjonalitet
- alt eksplisitt i koden, isolert, enkelt å feilsøke

- punkt om Micro-ORMer: "Vurder!". Anbefales!

---

<!-- .slide: data-background="./img/rainbow.jpg" -->

### Alternativer?

# SQL
## Micro-ORMs


Note: Micro-ORM forts:
- lettvekt alt. til transformasjon mellom DB og OO

---

## Java Persistence API

# Avstå!


Note: **JPA**
- greit til enkle case
- avanserte relasjoner og spørringer: JPA "i veien"
- streve med å få JPA til å gjøre spørringene man vil
- uheldig tilnærming for:
    - robust
    - ytende
    - endringsvillig
- "denne gangen riktig", fristende i starten, enkel app
- kjenne igjen følelsen
- ikke verdt å dra inn komplekst og invaderende rammeverk, hjelper kun de enkle casene, kan fint løses med JDBC og SQL
- utfordre litt etablerte "sannheter"

- huk tak i meg. Både enig og dypt uenig i budskapet.



---

## 👋

Note: **Takk for meg!**
