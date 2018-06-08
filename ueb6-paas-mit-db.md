2018-06-08 Christoph Knabe

# Java-Applikation auf PaaS mit Datenbank

Das Ziel dieser Übung ist es, eine Java-Applikation, nämlich die Spring **Petclinic**, mit **persistenter Datenbank**nutzung in der Cloud auf einem **Platform-as-a-Service**-Anbieter zu betreiben.

Ich habe dafür Heroku als bekannten und bequemen PaaS-Anbieter ausgewählt, aber man darf die Aufgabe auch auf anderen Anbietern betreiben (z.B. OpenShift von RedHat oder Google Application Engine).

Arbeiten Sie zunächst einmal meine Anleitung [heroku.md](heroku.md) und die darin verlinkte Anleitung https://devcenter.heroku.com/articles/getting-started-with-java durch. Danach wissen Sie, wie man eine mavenisierte Java-Applikation lokal und bei Heroku ausführt und wie man diese mit einer PostgreSQL-Datenbank verbindet. PostgreSQL (kurz Postgres) erwähne ich, weil Heroku dafür ein unterstütztes Add-on anbietet und Datenbanken bis 10.000 Zeilen kostenlos betreibt.

Sodann sollen Sie die Petclinic mit einer SQL-Datenbank in der Cloud betreiben, sodass

- einige Beispieleinträge schon vorhanden sind
- Sie neue Einträge vornehmen können
- nach Herunterfahren und Wiederstarten der Applikation diese Einträge noch vorhanden sind.

Relevante Anpassungen der Petclinic müssen Sie mindestens an folgenden Stellen vornehmen:

- In Datei  `pom.xml`die Dependency für den Treiber der von Ihnen verwendeten SQL-Datenbank statt `hsqldb` eintragen. Wie dieser Datenbanktreiber heißt, müssen Sie selbst recherchieren.
- In `src/main/resources/application.properties` die andere Datenbankart auswählen.
- In `src/main/resources/db` für Ihre Datenbankart SQL-Skripte zum Erzeugen des Schemas und zum Füllen der Tabellen mit Beispieldaten ablegen (analog zu den dort vorhandenen Varianten).
- Alternativ können Sie statt mit einem selbstgeschriebenen SQL-Skript das Datenbankschema auch durch JPA/Hibernate generieren lassen, indem Sie die Property `spring.jpa.hibernate.ddl-auto` von `none` auf `update` umstellen. Siehe https://docs.spring.io/spring-boot/docs/current/reference/html/howto-database-initialization.html (Für Produktionsdatenbanken ist dies nicht empfohlen, da eine Schemaänderung immer kontrolliert ablaufen sollte.)

Es ist sinnvoll, die Applikation mit der persistenten Datenbank lokal zu testen, obwohl für die Abnahme ein Betrieb auf einem PaaS-Anbieter ausreicht.