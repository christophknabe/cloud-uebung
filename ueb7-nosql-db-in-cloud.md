2018-06-15 Christoph Knabe

# Spring-Petclinic mit NoSQL-Datenbank in der Cloud

Das Ziel dieser Übung ist es, eine Java-Applikation, nämlich die Spring **Petclinic**, mit Nutzung einer **persistenten [NoSQL-Datenbank](https://de.wikipedia.org/wiki/NoSQL) in der Cloud** zu betreiben.

Diese Übung ist ein individuelles Projekt. Jeder Studierende muss sich eine eigene NoSQL-Datenbank suchen und diese bei mir anmelden.

## 1. NoSQL-Datenbanksystem aussuchen

Im Zuge der immer höher werdenden Anforderungen an Skalierbarkeit für das Web 2.0 hat man auf einige Einschränkungen und Garantien der relationalen Datenbanken verzichtet. Es entstand dabei eine Vielzahl von [NoSQL-Datenbanksystemen](http://nosql-database.org/). Sie müssen sich daher ein solches Datenbanksystem suchen, das Sie in der Cloud betreiben können.

Informieren Sie sich z.B., für welche NoSQL-Datenbanksysteme Google Cloud gemanagte Dienste anbietet. Alternativ können Sie ein beliebiges NoSQL-Datenbanksystem mittels Docker in der Google Cloud betreiben. Sinnvoll ist es natürlich eines auszuwählen, das Sie auch auf Ihrem Entwicklungsrechner ausführen können.

Damit jeder Studierende sein individuelles Projekt erbringt, müssen Sie Ihre Entscheidung für ein NoSQL-Datenbanksystem bei mir anmelden. Wenn zwei sich für dasselbe Datenbanksystem entscheiden, hat die frühere Anmeldung Priorität. Ich werde die akzeptierten und abgelehnten Anmeldungen im Moodle-Kurs veröffentlichen.

## 2. Petclinic auf Ihr Datenbanksystem umstellen

Das Ziel ist jetzt, die [Spring Petclinic Stable](https://github.com/ChristophKnabe/spring-petclinic-stable) auf Ihr gewähltes Datenbanksystem umzustellen.

Das [Spring Framework](https://spring.io/) unterstützt Modularität hervorragend. Leider erklärt die Dokumentation der aktuellen [Spring Petclinic](https://github.com/spring-projects/spring-petclinic) vergleichsweise wenig zur Architektur der Anwendung. Deutlich mehr Einführung finden Sie in der älteren Variante [Spring Petclinic](http://projects.spring.io/spring-petclinic/). 

Dort finden Sie im Abschnitt **PetClinic Database**  die Tabellen der relationalen Datenbank `owners`, `types`, `pets`, `vets`, `specialties`, `vet_specialties` und `visits` mit ihren Fremdschlüsselbeziehungen.

Da eine komplette Umstellung der 7 Tabellen und ihrer Zugriffsdienste (Repositories) zu aufwändig wäre, wird die Aufgabe auf die ersten 3 Tabellen eingeschränkt. Also es sollen nur Instanzen der Klassen `Owner`, `PetType` und `Pet` verwaltet werden können.

Im Java-Paket `petclinic.owner` finden Sie die sowohl die Entity-Klassen  `Owner`, `PetType` und `Pet` als auch die Zugriffs-Interfaces `OwnerRepository`und `PetRepository`. [Spring Data JPA](https://projects.spring.io/spring-data-jpa/) generiert beim Starten der Spring-Applikation aus den Interfaces entsprechende Implementierungen. Daher gibt es im Musterprojekt Spring-Petclinic keine expliziten Iplementierungen dieser Interfaces.

Ihre Aufgabe ist es jetzt, entweder diese beiden Interfaces selbst passend für Ihr Datenbanksystem zu implementieren oder eine andere Spring-Komponente zu finden und korrekt zu konfigurieren, sodass diese die Interfaces bei Programmstart implementiert.

Damit alles kompilier- und testbar wird, müssen Sie die anderen Repository-Interfaces `vet.VetRepository` und `visit.VisitRepository` als Dummies implementieren.

Wenn Sie `mvn package` ausführen, werden die vorgesehenen Tests wegen der Dummy-Repositories vermutlich nicht alle funktionieren. Um dennoch das gewünschte Produkt bauen zu können, müssen Sie dann `mvn -DskipTests package` verwenden.

## 3. Abnahmekriterien

Sie müssen einen gedruckten Bericht von ca. 3 A4-Seiten abgeben mit folgenden Inhalten

* Eigener Name, Datum
* Auswahlkriterien für Ihr Datenbanksystem
* Beschreibung der aufgetretenen Probleme
* Beschreibung der gewählten Lösung (u.a. welche Änderungen an der Spring Petclinic)

Außerdem müssen Sie Ihre NoSQL-Variante der Petclinic in einem gitlab-Projekt  versionieren und mich in dieses Projekt als Developer aufnehmen, damit ich Ihren Quellcode anschauen kann.

Sodann müssen Sie in der Cloud Ihre Datenbank und Ihre Petclinic starten. Es soll möglich sein, `Owner` zu verwalten und zu einem ausgewählten `Owner` seine `Pet`s zu verwalten. Es ist nicht nötig, dass man über die Oberfläche neue `PetType`s definieren kann. Es reicht, wenn diese bei Programmstart definiert werden.

Als Nachweis der Persistenz müssen eingetragene Veränderungen auch nach Herunter- und Wiederhoch-Fahren der Petclinic erhalten bleiben. Die als getrennter Service betriebene Datenbank muss dabei nicht heruntergefahren werden, damit man auch eine der aktuellen hochperformanten In-Memory-Datenbanken auswählen kann.


