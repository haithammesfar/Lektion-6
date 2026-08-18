# Lektion 6_1 – NoSQL | Klausur-Spickzettel

## 1. NoSQL – Grundlagen

- **NoSQL:** „Not only SQL“; Datenbanksysteme, die nicht auf das klassische relationale Modell beschränkt sind.
- **Hauptmotivation:** Skalierbarkeit und Vermeidung des Object-Relational Impedance Mismatch.
- **Non-relational:** Daten müssen nicht als klassische relationale Tabellen gespeichert werden.
- **Schema-free / Schema-flexible:** Datensätze müssen nicht zwingend exakt dieselbe Struktur besitzen.
- **Distributed:** Datenverarbeitung/-speicherung ist auf mehrere Nodes verteilt.
- **Horizontal Scaling:** Mehr Nodes/Server hinzufügen, statt einen einzelnen Server stärker zu machen.
- **Vertical Scaling:** Einen Server mit mehr CPU, RAM usw. ausstatten.
- **Replication:** Gleiche Daten auf mehreren Nodes speichern.
- **Tunable Consistency:** Stärke der Konsistenz kann je nach Anwendung eingestellt werden.


# 2. Die 4 NoSQL-Datenmodelle

## Key-Value Store

**Struktur:**

`key → value`

Beispiel:

`user:42 → {Name: "Ali", Alter: 25}`

- **Key:** Eindeutiger Schlüssel zum Finden eines Werts.
- **Value:** Beliebiger gespeicherter Wert, z.B. String, Liste oder Objekt.
- **Schema-free:** Datenstruktur der Values kann flexibel sein.
- **Typische Operationen:** `set(key,value)`, `get(key)`, `delete(key)`.
- **Primary Index:** Index auf dem Key.
- **Vorteil:** Sehr einfacher und schneller Zugriff über den Key.
- **Nachteil:** Komplexe Abfragen über Inhalte der Values sind schwieriger.
- **Systeme:** Redis, DynamoDB, Riak.


## Document Store

**Struktur:**

`key → document`

Beispiel:

{
  "id": 1,
  "name": "Ali",
  "address": {
    "city": "Berlin"
  }
}

- **Document:** Strukturierter Datensatz, meist JSON oder BSON.
- **JSON:** Textformat für hierarchisch strukturierte Daten.
- **BSON:** Binäre Darstellung von JSON.
- **Hierarchical:** Dokumente können andere Objekte oder Arrays enthalten.
- **Collection:** Gruppe von Dokumenten, z.B. `customers`.
- **Primary Index:** Typischerweise auf Document-ID.
- **Secondary Index:** Index auf anderen Dokumentfeldern, z.B. `name`.
- **Composite Index:** Index über mehrere Felder gemeinsam.
- **Vorteil:** Flexible und natürliche Speicherung komplexer Objekte.
- **Systeme:** MongoDB, CouchDB, Couchbase.


## Column Family Database

Grundstruktur:

`(rowkey, column, timestamp) → value`

- **Row Key:** Eindeutiger Schlüssel einer Zeile.
- **Column:** Einzelnes Attribut mit einem Wert.
- **Column Family:** Gruppe zusammengehöriger Columns.
- **Timestamp:** Kann verschiedene Versionen desselben Wertes unterscheiden.
- **Column Families:** Müssen grundsätzlich vorher definiert werden.
- **Columns:** Können flexibel sein und müssen nicht für jede Row identisch sein.
- Daten werden physisch nach **Column Family** organisiert.
- **Cassandra:** Begriff `Column Family` entspricht ungefähr einer `Table`.
- **Systeme:** Cassandra, HBase, Google Cloud Bigtable.


## Graph Database

Grundmodell:

`G = (V, E)`

- **Vertex / Node (V):** Entität, z.B. Person, Film oder Produkt.
- **Edge / Relationship (E):** Beziehung zwischen zwei Nodes.
- **Property:** `(key,value)`-Attribut eines Nodes oder einer Relationship.
- **Label:** Kennzeichnet die Rolle/Art eines Nodes.
- **Directed Graph:** Beziehungen besitzen eine Richtung.
- **Property Graph:** Nodes und Relationships können Properties besitzen.
- **Traversal:** Navigation durch Relationships eines Graphen.
- **Cypher:** Graph Query Language, die z.B. von Neo4j verwendet wird.
- **Vorteil:** Sehr gut für stark vernetzte Daten und schnelle Navigation über Beziehungen.
- **Systeme:** Neo4j, ArangoDB, OrientDB.


# 3. Welches Datenmodell wofür?

- **Key-Value:** Einfacher Zugriff über eindeutigen Key, z.B. Sessions oder Cache.
- **Document:** Hierarchische/objektartige Daten, z.B. Produkte, Benutzerprofile.
- **Column Family:** Große verteilte Datenmengen mit vorher bekannten Zugriffsmustern.
- **Graph:** Beziehungen sind zentral, z.B. Social Networks oder Recommendation-Systeme.

Merke:

`Key-Value → Key wichtig`

`Document → Objekt/Dokument wichtig`

`Column Family → große verteilte strukturierte Daten`

`Graph → Beziehungen wichtig`


# 4. Polyglot Persistence

- **Polyglot Persistence:** Verschiedene Datenbanksysteme innerhalb einer Anwendung verwenden, je nachdem welches System für eine Aufgabe am besten geeignet ist.

Beispiel E-Commerce:

`Session → Key-Value`

`Bestellungen → Document Store`

`klassische Daten → RDBMS`

`Social Graph → Graph DB`

Merke:

**Nicht „One Size Fits All“, sondern richtige Datenbank für die richtige Aufgabe.**

- **Multi-Model DBMS:** Ein einzelnes DBMS unterstützt mehrere Datenmodelle.
- **Polystore:** System kombiniert mehrere unterschiedliche Datenbanksysteme.


# 5. Object-Relational Impedance Mismatch

- **Object-Relational Impedance Mismatch:** Unterschied zwischen objektorientierten Datenstrukturen der Anwendung und relationalen Tabellen.
- Ein komplexes Objekt muss in relationalen DBs oft auf mehrere Tabellen verteilt werden.
- NoSQL-Dokumente können komplexe Objekte häufig direkter speichern.


# 6. Data Modeling – wichtigste Entscheidung

## Embedding vs. Referencing

### Embedding

Daten werden direkt ineinander gespeichert.

Beispiel:

{
  "customer": "Ali",
  "address": {
    "city": "Berlin"
  }
}

- **Embedding:** Zusammengehörige Daten direkt innerhalb desselben Dokuments/Aggregates speichern.
- **Vorteil:** Daten können häufig mit einem Zugriff gelesen werden; kein JOIN nötig.
- **Nachteil:** Redundanz und große Dokumente möglich.

### Referencing

Daten werden getrennt gespeichert und über IDs verbunden.

Beispiel:

Customer:
`addressId = 17`

Address:
`id = 17`

- **Referencing:** Beziehung über einen Schlüssel/eine ID speichern.
- **Vorteil:** Weniger redundante Daten und unabhängige Aktualisierung.
- **Nachteil:** Mehrere Zugriffe bzw. JOIN-artige Verarbeitung können nötig sein.

Merke:

`Embedding → zusammen speichern`

`Referencing → getrennt speichern + ID`


# 7. Denormalization & Redundancy

- **Denormalization:** Daten bewusst zusammenführen/duplizieren, damit Queries schneller werden.
- **Redundancy:** Dieselbe Information wird mehrfach gespeichert.
- Vorteil: Weniger JOINs und häufig schnellere Reads.
- Nachteil: Änderungen müssen eventuell an mehreren Stellen durchgeführt werden.


# 8. Aggregate-Oriented Modeling

- **Aggregate:** Zusammengehörige Daten, die als eine Einheit gespeichert und verarbeitet werden.
- **Aggregate-Oriented DBs:** Key-Value, Document und Column Family.
- **Intra-Aggregate Relationship:** Beziehung innerhalb desselben Aggregates → einfach.
- **Inter-Aggregate Relationship:** Beziehung zwischen verschiedenen Aggregates → schwieriger.
- **Query-First Approach:** Datenmodell danach entwerfen, welche Queries die Anwendung später ausführen muss; besonders wichtig bei Cassandra.

Merke:

**NoSQL-Modellierung beginnt häufig mit den Zugriffsmustern/Queries, nicht nur mit der Datenstruktur.**


# 9. Cassandra Primary Key

Ein Cassandra Primary Key besteht aus:

`Primary Key = Partition Key + Clustering Columns`

- **Primary Key:** Identifiziert eine Row eindeutig.
- **Composite/Compound Key:** Primary Key aus mehreren Spalten.
- **Partition Key:** Bestimmt, zu welcher Partition zusammengehörige Rows gehören.
- **Clustering Columns:** Ordnen Rows innerhalb derselben Partition.
- **Clustering Order:** Reihenfolge der Daten innerhalb einer Partition.
- **Static Column:** Wert, der von allen Rows derselben Partition geteilt wird.

Merke:

`Partition Key → WO liegen Daten?`

`Clustering Columns → WIE sind Daten innerhalb der Partition geordnet?`


# 10. Graph Data Modeling

Best Practices:

- **Nodes for Things:** Objekte/Entitäten als Nodes modellieren.
- **Relationships for Structure:** Verbindungen als Relationships modellieren.
- **Complex Values:** Komplexe Werte können als eigene Nodes modelliert werden.
- Graphmodelle können häufig direkt aus einer fachlichen Zeichnung abgeleitet werden („Whiteboard Friendlyness“).


# 11. Nested Data abfragen

- **Nested Data:** Daten, die innerhalb anderer Objekte/Dokumente liegen.
- **Dot Notation:** Zugriff auf verschachtelte Felder über Punkte.

Beispiel:

`customer.address.city`

- **Array:** Liste mehrerer Werte innerhalb eines Dokuments.
- **Collection/Array Operators:** Operatoren zum direkten Abfragen von Arrays.
- MongoDB-Beispiele: `$in`, `$all`, `$size`.
- **Unnest / Flatten:** Array in einzelne Elemente auflösen.
- MongoDB: `$unwind`.
- Couchbase: `UNNEST`.
- **Problem:** Unnest/Flatten kann teuer sein → wenn möglich Collection Operators verwenden.


# 12. Heterogeneous Interfaces

- **Heterogeneous Interfaces:** Verschiedene NoSQL-Systeme verwenden unterschiedliche APIs und Query Languages.
- **Language Binding:** Schnittstelle, mit der eine Programmiersprache auf ein DBMS zugreift.
- Beispiele: Java, Python, C#, REST, Thrift.
- **Vendor Lock-in:** Anwendung wird stark von einem bestimmten Hersteller/System abhängig.
- Problem: Dieselbe Query sieht bei MongoDB, Cassandra, HBase usw. unterschiedlich aus.


# 13. Object-NoSQL Mapper

- **Object-NoSQL Mapper:** Zwischenschicht zwischen Anwendung und NoSQL-Datenbank.
- Er übersetzt Anwendungsobjekte/Queries in die jeweilige Sprache des NoSQL-Systems.
- Ähnliche Idee wie ORM bei relationalen Datenbanken.
- **Multi Data Store Mapper:** Unterstützt mehrere verschiedene Datenbanksysteme.
- **Single Data Store Mapper:** Unterstützt nur ein bestimmtes Datenbanksystem.
- **JPA:** Java Persistence API.
- **JPQL:** Java Persistence Query Language.
- Problem: Nicht jedes NoSQL-System unterstützt dieselben Funktionen, daher kann ein Mapper nur eingeschränkte Funktionalität anbieten.


# 14. JOINs in NoSQL

- **JOIN:** Verknüpft zusammengehörige Daten aus unterschiedlichen Datenstrukturen.
- Viele NoSQL-Systeme unterstützen JOINs gar nicht oder nur eingeschränkt.
- Deshalb sind **Embedding, Denormalization und query-orientierte Modellierung** wichtig.
- **MongoDB `$lookup`:** Operator für JOIN-artige Operationen.
- **Couchbase N1QL:** SQL-ähnliche Query Language mit JOIN-Unterstützung.

Merke:

**NoSQL → möglichst so modellieren, dass benötigte Daten zusammenliegen und teure JOINs vermieden werden.**


# 15. Schema in NoSQL

NoSQL bedeutet NICHT automatisch „kein Schema“.

Es gibt:

- **Without native schema support:** DB erzwingt kein Schema, z.B. Couchbase, CouchDB, Neo4j.
- **Optional schema support:** Schema kann verwendet werden, z.B. MongoDB.
- **Mandatory schema:** Schema ist erforderlich, z.B. Cassandra.

- **Schema Flexibility:** Unterschiedliche Datensätze können unterschiedliche Strukturen besitzen.
- Vorteil: Gut für agile Entwicklung und häufige Änderungen.
- Nachteil: Unterschiedliche Schema-Versionen können gleichzeitig in der Datenbank existieren.


# 16. Schema Management

## Forward Engineering

- **Forward Engineering:** Schema definieren/erstellen und daraus Regeln für die Datenbank ableiten.
- Beispiel: JSON Schema definieren und Daten dagegen validieren.
- **JSON Schema:** Beschreibung der erwarteten Struktur eines JSON-Dokuments.

## Reverse Engineering

- **Reverse Engineering:** Aus bereits vorhandenen Daten deren Schema/Struktur ableiten.
- **Schema Overview:** Übersicht darüber, welche Strukturen in den vorhandenen Daten vorkommen.
- **Data Exploration:** Vorhandene Daten und deren unterschiedliche Strukturen untersuchen.

Merke:

`Forward Engineering: Schema → Daten`

`Reverse Engineering: Daten → Schema`


# 17. Continuous Database Evolution

- **Schema Evolution:** Schema verändert sich zusammen mit neuen Anwendungsversionen.
- **Data Migration:** Bereits vorhandene Daten werden an eine neue Struktur angepasst.
- **Schema Version:** Bestimmte Version der Datenstruktur.
- **Advanced Reverse Engineering:** Unterschiedliche Schema-Versionen aus bestehenden Daten erkennen.

Continuous Database Evolution umfasst insbesondere:

`Schema Evolution + Data Migration`


# 18. Wichtigste Unterschiede für die Klausur

## Key-Value vs. Document

- **Key-Value:** Value wird hauptsächlich über Key gefunden.
- **Document:** Value besitzt eine durchsuchbare hierarchische Dokumentstruktur.

## Document vs. Graph

- **Document:** Zusammengehörige Daten als Aggregate/Dokument speichern.
- **Graph:** Beziehungen zwischen Daten stehen im Mittelpunkt.

## Embedding vs. Referencing

- **Embedding:** Daten zusammen speichern → schnelle gemeinsame Reads, aber Redundanz möglich.
- **Referencing:** Daten getrennt speichern → weniger Redundanz, aber zusätzliche Zugriffe nötig.

## Relational vs. Aggregate-Oriented

- **Relational:** Daten auf Tabellen verteilen und über Beziehungen/JOINs verbinden.
- **Aggregate-Oriented:** Zusammengehörige Daten möglichst als eine Einheit speichern.

## Forward vs. Reverse Engineering

- **Forward:** Schema → Daten.
- **Reverse:** Daten → Schema.


# 19. Klausur-Schnellentscheidung

Frage enthält **„Beziehungen / Netzwerk / Freunde / kürzester Pfad“**
→ `Graph DB`

Frage enthält **„JSON / hierarchisches Objekt / flexible Attribute“**
→ `Document Store`

Frage enthält **„einfach Key → Value / Session / Cache“**
→ `Key-Value Store`

Frage enthält **„Cassandra / Row Key / Column Family / Timestamp“**
→ `Column Family DB`

Frage enthält **„Daten häufig zusammen lesen“**
→ eher `Embedding`

Frage enthält **„Daten unabhängig ändern / Redundanz vermeiden“**
→ eher `Referencing`

Frage enthält **„Datenmodell anhand der Queries entwerfen“**
→ `Query-First Approach`

Frage enthält **„mehrere DB-Typen für unterschiedliche Aufgaben“**
→ `Polyglot Persistence`

Frage enthält **„Schema aus bestehenden Daten herausfinden“**
→ `Reverse Engineering`

Frage enthält **„Schema vorgeben/erstellen“**
→ `Forward Engineering`

Frage enthält **„alte Daten an neues Schema anpassen“**
→ `Data Migration`

# Lektion 6_2 – NoSQL Techniques | Klausur-Spickzettel

## 1. Grundbegriffe

- **Node:** Ein Server/Rechner innerhalb eines verteilten Datenbanksystems.
- **Replica:** Eine Kopie derselben Daten auf einem anderen Node.
- **Stale Replica:** Replica mit einem veralteten Datenstand.
- **Latency:** Zeit, die eine einzelne Operation benötigt.
- **Throughput:** Anzahl der Operationen, die pro Zeiteinheit verarbeitet werden.
- **Availability:** Das System kann Anfragen weiterhin beantworten.
- **Durability:** Erfolgreich gespeicherte Daten überleben einen Ausfall.
- **Scalability:** Fähigkeit, durch zusätzliche Ressourcen/Nodes mehr Last zu verarbeiten.
- **Fault Tolerance:** System funktioniert trotz Ausfall einzelner Komponenten weiter.
- **Consistency:** Wie einheitlich/aktuell die Daten auf verschiedenen Replicas sichtbar sind.


# 2. Consistency

## CAP-Theorem

- **C – Consistency:** Alle Clients sehen einen konsistenten/aktuellen Datenstand.
- **A – Availability:** Jede Anfrage an einen funktionierenden Node erhält eine Antwort.
- **P – Partition Tolerance:** System kann mit Netzwerkunterbrechungen zwischen Nodes umgehen.
- **CAP-Trade-off:** Bei einer Network Partition muss zwischen Consistency und Availability abgewogen werden.

## PACELC

- **P → A vs. C:** Bei einer Partition: Availability oder Consistency.
- **Else → L vs. C:** Ohne Partition: niedrige Latency oder stärkere Consistency.

## Consistency-Modelle

- **Strong Consistency:** Nach abgeschlossenem Write liefern folgende Reads den neuen Wert.
- **Weak Consistency:** Nach einem Write kann ein Read vorübergehend noch einen alten Wert liefern.
- **Eventual Consistency:** Wenn keine neuen Writes kommen, werden irgendwann alle Replicas konsistent.
- **Inconsistency Window:** Zeitraum, in dem Replicas unterschiedliche Versionen besitzen können.

### Varianten von Eventual Consistency

- **Causal Consistency:** Kausal abhängige Operationen werden in der richtigen Reihenfolge gesehen.
- **Read-your-writes:** Ein Client sieht nach seinem eigenen Write bei seinen folgenden Reads mindestens seine eigene Änderung; andere Clients können kurzfristig noch alte Daten sehen.
- **Session Consistency:** Read-your-writes gilt innerhalb derselben Benutzersitzung.
- **Monotonic Reads:** Hat ein Client Version 5 gesehen, bekommt er später keine ältere Version 4.
- **Monotonic Writes:** Writes desselben Clients werden in ihrer ursprünglichen Reihenfolge ausgeführt.

## BASE

- **Basically Available:** System versucht trotz Fehlern verfügbar zu bleiben.
- **Soft State:** Datenzustand kann sich durch Hintergrundsynchronisation auch ohne neuen Client-Write verändern.
- **Eventually Consistent:** Replicas werden mit der Zeit konsistent.


# 3. Konflikte & Versionen

## Lamport Timestamp

- **Lamport Timestamp:** Logische Uhr zur Ordnung von Ereignissen in einem verteilten System.
- Jeder Node besitzt einen logischen Counter.
- Bei einem Ereignis wird der Counter erhöht.
- Beim Empfang eines größeren Timestamps wird die eigene Clock nach vorne gesetzt.
- **Problem:** Lamport Timestamps können Ereignisse ordnen, erkennen aber Concurrent Writes nicht eindeutig.

## Vector Clock

- **Vector Clock:** Liste von `(Node, Counter)` zur Erkennung der Beziehung zwischen Datenversionen.
- Beispiel: `[A:1, B:0]`.
- `[1,0] → [1,1]`: zweite Version ist Nachfolger der ersten.
- `[1,0]` vs. `[0,1]`: keine Version dominiert die andere → Concurrent Writes.
- **Concurrent Writes:** Unabhängige/gleichzeitige Änderungen, bei denen keine Version eindeutig aus der anderen entstanden ist.
- Beim Zusammenführen von Vector Clocks wird pro Node das Maximum betrachtet.


# 4. Sharding

- **Sharding:** Unterschiedliche Teile der Daten werden auf verschiedene Nodes verteilt.
- **Wichtig:** Sharding = unterschiedliche Daten verteilen; Replication = gleiche Daten kopieren.

## Sharding-Arten

- **Hash Sharding:** `hash(key)` bestimmt den Shard; gute Lastverteilung, aber schlechte Data Locality/Range Queries.
- **Range Sharding:** Wertebereiche werden auf Shards verteilt; gut für Range Queries, aber ungleichmäßige Last möglich.
- **Entity-Group Sharding:** Zusammengehörige Daten werden auf demselben Shard gespeichert; lokale Transaktionen werden einfacher.
- **Data Locality:** Zusammengehörige Daten liegen möglichst nahe bzw. auf demselben Node.
- **Repartitioning/Rebalancing:** Daten werden neu zwischen Shards verteilt, um Last auszugleichen.

## Consistent Hashing

- **Consistent Hashing:** Keys und Nodes werden auf einen logischen Hash-Ring abgebildet.
- Ein Key gehört zum nächsten Node im Uhrzeigersinn.
- Vorteil: Beim Hinzufügen/Entfernen eines Nodes müssen vergleichsweise wenige Keys verschoben werden.
- **Virtual Node (VNode):** Ein physischer Node besitzt mehrere Positionen auf dem Hash-Ring → bessere Lastverteilung.


# 5. Replication

- **Replication:** Dieselben Daten werden auf mehreren Nodes gespeichert.
- Ziele: Availability, Fault Tolerance und bessere Read-Performance.

## Synchron vs. Asynchron

- **Synchronous Replication:** Write wird erst bestätigt, nachdem benötigte Replicas aktualisiert wurden → stärkere Consistency, höhere Latency.
- **Asynchronous Replication:** Write wird früher bestätigt und andere Replicas werden später aktualisiert → schnelle Writes, aber stale Replicas möglich.
- **Acknowledgement (ACK):** Technische Rückmeldung eines Replicas: „Ich habe den Write erhalten/gespeichert.“


# 6. Quorum Consensus

- **N:** Gesamtzahl der Replicas.
- **W:** Anzahl der Replicas, die einen Write bestätigen müssen.
- **R:** Anzahl der Replicas, die bei einem Read kontaktiert werden.

### Wichtige Bedingung

`W + R > N`

→ Read- und Write-Gruppe müssen sich überschneiden → laut Vorlesungsmodell Strong Consistency durch Quorum Assembly.

Beispiel:

`N=3, W=2, R=2`

`2+2 > 3` → Überschneidung garantiert.

### Konfigurationen

- **Read Optimized / ROWA:** `W=N, R=1` → Writes teuer, Reads schnell.
- **Write Optimized:** `W=1, R=N` → Writes schnell, Reads teuer.
- **Majority Consensus:** `R=W=N/2+1`, z.B. `N=3 → R=2, W=2`.
- **ROWA:** Read One, Write All.
- **Tunable Consistency:** Anwendung/DB-Konfiguration kann Read-/Write-Consistency-Level einstellen.


# 7. Failure- & Repair-Techniken

- **Hinted Handoff:** Write für einen ausgefallenen Replica wird vorübergehend auf einem anderen Node gespeichert und später übertragen.
- **Gossip Protocol:** Nodes tauschen regelmäßig Informationen über den Zustand anderer Nodes/Cluster aus.
- **Read Repair:** Beim Lesen wird ein stale Replica erkannt und aktualisiert.
- **Anti-Entropy:** Replicas werden im Hintergrund verglichen und repariert, auch wenn die Daten nicht gelesen werden.

Merke:

`Read Repair = Reparatur beim Read`

`Anti-Entropy = Reparatur im Hintergrund`


# 8. Leader / Primary Copy

- **Leader / Primary Copy:** Node, der für bestimmte Daten Writes annehmen darf.
- **Single-Leader:** Nur ein Leader akzeptiert Writes → einfacher konsistent zu halten, aber Leader kann Bottleneck/SPOF werden.
- **Bottleneck:** Engpass, der die Gesamtleistung begrenzt.
- **SPOF – Single Point of Failure:** Einzelne Komponente, deren Ausfall das System stark beeinträchtigt.
- **Update Anywhere / Multi-Leader:** Mehrere Nodes können Writes annehmen → höhere Availability, aber Konflikte zwischen Writes möglich.


# 9. Storage Management

- **RAM:** Sehr schneller, aber flüchtiger Speicher.
- **SSD:** Schneller persistenter Speicher.
- **HDD:** Persistenter, günstiger Massenspeicher; Random Access vergleichsweise langsam.
- **Persistent:** Daten bleiben nach Neustart/Stromausfall erhalten.
- **Volatile:** Daten gehen ohne Strom verloren.
- **Random I/O:** Zugriff auf unterschiedliche/verstreute Speicherstellen.
- **Sequential I/O:** Daten werden hintereinander gelesen/geschrieben.

## Techniken

- **Caching:** Häufig benötigte Daten im schnellen Speicher halten → geringere Read Latency.
- **In-Memory:** Daten hauptsächlich im RAM halten → sehr niedrige Latency.
- **Logging:** Änderungen in einem Log speichern → unterstützt Durability.
- **WAL – Write-Ahead Log:** Änderung zuerst ins Log schreiben, bevor die eigentliche Datenstruktur geändert wird.
- **Update-in-Place:** Alten Wert direkt an seiner bestehenden Speicherposition überschreiben → gut für Reads.
- **Append-Only:** Alten Wert nicht überschreiben; neue Version hinten anhängen → Sequential Writes und hoher Write Throughput.

Merke:

`Caching/In-Memory → niedrige Latency`

`Update-in-Place → gute Read Latency`

`Append-Only → hoher Write Throughput`

`Logging/WAL → Durability`


# 10. Query Processing & Indexe

- **Index:** Zusätzliche Datenstruktur, mit der Daten schneller über bestimmte Attribute gefunden werden.
- **Primary Index:** Index über den primären Schlüssel, z.B. `UserID`.
- **Secondary Index:** Index über ein anderes Attribut, z.B. `Farbe`, `Stadt` oder `Alter`.

Beispiel:

Daten: `ID=56, Farbe=Blau`

Index auf Farbe:

`Blau → [56, 188, 192]`

## Local Secondary Index

- Jede Datenpartition besitzt ihren eigenen lokalen Index.
- Beispiel: Jede Partition hat einen eigenen `Farbe → IDs`-Index.
- **Vorteil:** Schnelle Writes, weil nur der lokale Index aktualisiert werden muss.
- **Nachteil:** Query muss eventuell mehrere Partitionen durchsuchen.

### Scatter-Gather

- **Scatter:** Query an mehrere Partitionen schicken.
- **Gather:** Ergebnisse der Partitionen einsammeln und zusammenführen.

`Local Index → Fast Writes + Scatter-Gather Queries`

## Global Secondary Index

- Index wird global nach dem Suchattribut/-wert partitioniert.
- Beispiel: Alle `Blau`-Indexeinträge liegen auf einer bestimmten Index-Partition.
- **Targeted Query:** Query kann direkt zur zuständigen Index-Partition geschickt werden.
- **Vorteil:** Schnelle Queries.
- **Nachteil:** Writes aufwendiger, weil Daten und globaler Index auf verschiedenen Nodes aktualisiert werden können.

`Global Index → Fast Queries + Slow/Complex Writes`

## Weitere Query-Techniken

- **Query Planning:** Planung, auf welchen Nodes und in welcher Reihenfolge eine Query ausgeführt wird.
- **Distributed Query Planning:** Query-Planung über mehrere Nodes.
- **Analytics Framework:** Ausweichlösung für Analyseoperationen, die das NoSQL-System selbst nicht gut unterstützt.
- **Materialized View:** Vorberechnetes und gespeichertes Query-Ergebnis → schnelle Reads, aber muss bei Writes aktualisiert werden.
- **Range Query/Range Scan:** Suche über einen Wertebereich, z.B. `Alter BETWEEN 20 AND 30`.


# 11. Systeme aus der Vorlesung

- **Redis:** u.a. In-Memory, Caching, Logging, Primary Copy, asynchrone Replication.
- **Dynamo/Riak:** u.a. Hash Sharding, Consistent Hashing, Update Anywhere, asynchrone Replication.
- **HBase:** u.a. Range Sharding, Primary Copy, synchrone Replication, Append-Only Storage.
- **Cassandra:** u.a. Hash Sharding, Consistent Hashing, Update Anywhere, asynchrone Replication, Append-Only Storage.
- **MongoDB:** u.a. Range/Hash Sharding, Primary Copy, Replication, Local Index und Query Planning.


# 12. Wichtigste Unterschiede für die Klausur

**Sharding vs. Replication**
- Sharding → unterschiedliche Daten auf verschiedene Nodes.
- Replication → gleiche Daten auf mehrere Nodes.

**Strong vs. Eventual Consistency**
- Strong → neuer Wert sofort für folgende Reads.
- Eventual → Replicas dürfen kurz unterschiedlich sein, werden aber irgendwann gleich.

**Synchronous vs. Asynchronous Replication**
- Sync → stärker konsistent, aber langsamer.
- Async → schneller, aber stale Replicas möglich.

**Read Repair vs. Anti-Entropy**
- Read Repair → beim Lesen.
- Anti-Entropy → im Hintergrund.

**Update-in-Place vs. Append-Only**
- Update-in-Place → alten Wert überschreiben → Read-orientiert.
- Append-Only → neue Version anhängen → Write-orientiert.

**Local vs. Global Index**
- Local → schnelle Writes, Scatter-Gather Queries.
- Global → schnelle/gezielte Queries, aufwendigere Writes.

**Lamport vs. Vector Clock**
- Lamport → logische Reihenfolge von Ereignissen.
- Vector Clock → erkennt zusätzlich Beziehungen und Concurrent Versions.

**Read Optimized vs. Write Optimized**
- Read optimized → `W=N, R=1`.
- Write optimized → `W=1, R=N`.


# 13. Klausur-Formeln

### Quorum

`W + R > N`

- N = Replicas insgesamt
- W = notwendige Write-Bestätigungen
- R = beim Read kontaktierte Replicas

### Majority

`R = W = N/2 + 1`

Beispiel:

`N=3 → R=2, W=2`

### Beispiel Klausur

`N=5, W=3, R=3`

`W + R > N`

`3 + 3 > 5` ✅

→ Read- und Write-Quorum überschneiden sich.
