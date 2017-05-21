JPA / Hibernate - avstå!
=========================================





Detaljert manus
-------------------------

Hei, jeg heter Rune og jobber som utvikler.
For å sørge for at vi ikke gir inntrykk av at i BEKK så driver vi _bare_ med sånn dødshipp teknologi, som Elm og sånn, har jeg tenkt å rette oppmerksomheten mot noe litt mer traust og velkjent for sikkert så og si alle som jobber med Java:

🔺
Java Persistence API (JPA)
eller Hibernate om du vil, da dette er vel den mest utbredte implementasjonen av JPA.

🔺
Jeg har lyst til å begynne med å si at dersom jeg kommer meg gjennom denne lille presentasjonen uten å fornærme noen så er det fint, 🔺 men jeg har heller ikke gjort noen særskilte grep for å unngå det, så jeg tar gladelig i mot slag og spark, ev. røddige diskusjoner etterpå.

🔺
Håndsopprekking:
- Er det mange her på Java-prosjekter som lagrer data i relasjonsdatabaser?
- bruker dere JPA/Hibernate?
- liker dere å bruke JPA?
	- nei: ikke sant? Det er noe tøys!
	- ja: å kom igjen a! Virkelig? Jeg tror dere lyver.



🔺
Hva er JPA?
Data i objektorientert kode (f.eks. Java) representeres strukturelt litt forskjellig i.f.t. hvordan de samme dataene representeres i en relasjonsdatabase, og spesielt hvordan man får data ut fra databasen i spørringer. For at en Java-applikasjon skal kunne behandle data fra en database på en fornuftig måte må dataene transformeres når de leses opp fra databasen til hvordan man ønsker å ha de i minnet i applikasjonen, og likeledes må de oversettes tilbake til et database-vennlig format når man skal lagre de.

🔺
JPA er kort fortalt noe som skal ligge "i mellom" 🔺 databasen din og Java-koden, og som gjør denne transformasjonen for deg. Det er et rammeverk man inkluderer i applikasjonen som skal gjøre at man skal liksom slippe å forholde seg til hvordan data representeres i 🔺 rader og kolonner i en database, og 🔺kun trenge å tenke på data i objektform.

🔺
Man har til og med assosiert et litt sånn skummel og pompøs techy betegnelse for denne forskjellen i hvordan data representeres og oversettingen mellom de: **Impedance mismatch**
🔺Vi har en mismatch dere! Dette trenger vi da et flere millioner kodelinjer langt komplekst rammeverk for å løse! Altså, det _er_ 2017 nå, og vi har bl.a. funnet ut at komponentbaserte Web-rammeverk som forsøker å abstrahere bort HTTP og request/response-flyten var et feilskjær. Det er på tide å innse at dette også gjelder slik abstraksjon av database-kommunikasjon!

🔺
Jeg har inntrykk av at JPA er noe man drar inn litt på autopilot i alle Java-applikasjoner.

Men jeg mener, med hånden på hjertet, at 🔺 JPA er noe du bør avstå fra å ta inn i applikasjonen din!


Nøkkelproblemet til JPA er at det fungerer litt for godt til de helt enkle casene.
Det er nesten litt sånn forførersk,. for det *er* ganske tilfredsstillende å bare kunne kaste noen annotasjoner på noen enkle domeneobjekter, og så blir de magisk lagret og hentet frem igjen. Men snart ser klassene dine slik ut:

🔺

```
public class Konto {
	
	private long id;

	...
}
```


(Venter litt, ser på reaksjoner, snur meg mot lerret)
Nei, jeg mener slik!

🔺

```
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

For meg er dette et skrekkeksempel på et litt sånn misforstått ideal om "single source of truth". Alt skal kunne leses ut fra domeneklassene, men jeg mener at detaljer rundt persistering av domenets tilstand er en separat problemstilling fra forretningslogikken, som er det som hører hjemme i domenemodellen.

Paradoksalt nok så lager JPA en temmelig sterk binding mellom databasen og domenemodellen:
🔺
- annotasjoner: Beskriver hvordan objekttilstand korresponderer til rader og kolonner i databasen + en rekke andre tekniske detaljer
🔺
- designet på koden i seg selv blir svært begrenset for at JPA skal greie å faktisk kunne behandle og lese ut tilstanden fra den. Det er trøblete å ta i bruk språkfeatures man har tilgjengelig for å skrive god kode.

Både bruken av annotasjoner og at designet er så implisitt bundet til databasen gjør det vanskelig å resonnere rundt hvilke ringvirkninger refaktorering av domenemodellen vil få, og domenemodellen bør være noe av den mest endringsvillige koden man har i en applikasjon! Jeg sikker på at folk kjenner igjen svære entitetsklasser som over tid har vokst ut i det ugjennkjennelige, og man bruker alltid kun et subsett av tilstanden i de ulike kontekstene man populerer de med data fra databasen. Jeg tror det er dette som egentlig er grunnen til at man må forholde seg til

🔺
Lazy loading. Disse svære altomfattende entitetsklassene kan ikke få populert opp all tilstand i alle kontekster de brukes da dette vil åpenbart ha fatale konsekvenser for ytelsen. Dette anti-patternet med å bruke en felles "core" modell for alle kontekster et domenekonsept er under behandling i har man altså kommet opp med en kompleks teknikk med et helt eget sett med utfordringer for at man skal kunne beholde dette anti-patternet som JPA er en driver for. Jeg har aldri savnet lazy-loading de gangene jeg har utviklet applikasjoner uten JPA. Ønsker du å utsette uthenting av data til de trengs, ikke spør etter de før de trengs.

🔺

Så hvilke alternativer har man?
🔺
Man kan bruke god gammeldags SQL rett i koden, samt skrive egen kode som oversetter resultater fra databasen til en lean & mean domenemodell. Jeg skal innrømme at JDBC ikke er det mest freshe APIet i JDKen, og har en del gotchas, men her kan jeg anbefale f.eks. Spring JDBC Template som et forenklende API på toppen JDBC. Da har du full kontroll på hva du spør etter, og når ting brekker trenger du ikke å nøste i hvordan et sett med deklarative annotasjoner blir på generisk og magisk vis oversatt til en SQL-spørring som ikke er ment å leses av mennesker. Du får et nært forhold til databasemodellen, vet nøyaktig hvilke spørringer som gjøres, og ikke minst _når_ de gjøres, kan enkelt utnytte databasespesifikk funksjonalitet i spørringer siden det er "ren SQL", og mapping og spørringer eksisterer eksplisitt i koden, er isolert, og enkelt å feilsøke.
🔺
Radaren har også et punkt om micro-ORMer under "vurder", som jeg vil anbefale å ta en titt på. Dette tilbyr en mer lettvekts tilnærming til transformasjonen mellom database-tilstand og objekter.


🔺
JPA fungerer greit først og fremst til de helt enkle casene. Straks man får litt mer avanserte relasjoner og behov for å gjøre komplekse spørringer blir JPA gjerne litt i veien, og man må streve for å få annotasjonene til å produsere akkurat de spørringene man vil. Dette er en uheldig tilnærming til å bygge robuste og ytende applikasjoner, som det er forventet at vi gjør. Jeg har flere ganger tatt meg selv i å tenke at "denne gangen skal jeg gjøre det riktig" nettopp fordi det er så fristende helt i starten mens applikasjonen er på sitt enkleste. Jeg vil oppfordre til å kjenne etter denne følelsen fordi jeg mener jeg det ikke er verdt å dra inn et så komplekst og invaderende rammeverk i utgangspunktet når det kun hjelper for de helt enkle casene man fint kan implementere med JDBC og SQL.

Jeg håper dette kanskje kan inspirere til å utfordre litt etablerte "sannheter" innen utvikling av Java-applikasjoner. Det er bare å huke tak i meg senere ikveld både dersom du er enig eller dypt uenig i budskapet her.



🔺
Takk for meg!













Noen gang forsøkt å finne ut hvor mange spørringer som gjøres med Hibernate? Slik gjøres det:
http://blog.takipi.com/hibernate-logging-tips-and-solutions-to-common-problems/

Sånn gjøres det i en applikasjon som _ikke_ bruker Hibernate:
- les koden som inneholder spørringen(e) som kjøres
- tell spørringen(e)





Masse stråmannsargumentasjon, logic fallacies:
- https://www.quora.com/What-does-JPA-do-differently-or-better-than-JDBC/answer/Vlad-Mihalcea-1
- https://www.quora.com/Is-Hibernate-ORM-still-a-hot-skill-and-tool-in-tech-in-the-Silicon-Valley/answer/Vlad-Mihalcea-1?share=cdf39ac8
