Christoph Knabe, 2018-04-23

# Übung 1: git, Maven, ssh, Spring Petclinic

## Installationen

* **git** ist ein Versionierungssystem. Lesen Sie bei Bedarf die Folien. Installieren Sie **git** und **git-gui** auf Ihrem Laptop. Siehe https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  Am einfachsten ist es, dies mit dem Paketmanager des benutzten Betriebssystems zu installieren, auf Debian-Linux z. B. `sudo apt-get install git git-gui `. Notieren Sie die installierte Version!
* **Java** ist eine Programmiersprache mit Laufzeitsystem. Installieren Sie den Java 8 SE JDK von http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
  Auf Debian ist es hier vorteilhaft, dies vom PPA WebUpd8 zu installieren, da dann es in den Debian-Aktualisierungsmechanismus enbezogen wird. Siehe http://www.webupd8.org/2014/03/how-to-install-oracle-java-8-in-debian.html
  Sie können stattdessen gerne auch den OpenJDK 8 nehmen, da in der Cloud ohnehin der OpenJDK üblich ist. Dann ist der Umweg über WebUpd8 unnötig.
  Notieren Sie die installierte Version!
* **Maven** ist ein Build-Werkzeug. Lesen Sie bei Bedarf die Folien. Installieren Sie Maven auf Ihrem Laptop. Siehe https://maven.apache.org/install.html
  Auch hier ist es wieder am einfachsten, wenn Sie dies mit dem Paketmanager Ihres Betriebssystems machen können, auf Debian-Linux z.B. mit `sudo apt-get install maven `.  Notieren Sie die installierte Version!

Manchmal sind die über den Linux-Paketverwalter beziehbaren Versionen inkompatibel veraltet. Dann muss man sich die entsprechende Software manuell downloaden und installieren.

## Beispielprojekt Spring Petclinic ausführen

* Das Framework [Spring](https://spring.io/) startete 2003 als leichtgewichtige Alternative zu Enterprise Java Beans für die Entwicklung von Unternehmensanwendungen mit Java. Es versucht, den Anwendungscode weitgehend von Abhängigkeiten zur Plattform zu entkoppeln und benötigte statt der ungefähr 20 APIs, die in einem Java EE Server zur Verfügung stehen, nur das Java Servlet API. Siehe auch ab Seite 8 in den Folien "Domain Driven Design". Traditionell werden Spring-Anwendungen als .war-Dateien gebaut und auf einen Java-Webserver deployt.

* [Spring Boot](https://projects.spring.io/spring-boot/) vereinfachte 2014 die Entwicklung von Web-Applikatonen und Microservices, indem die Installation eines Java-Webservers vor der eigenen Applikation abgelöst wurde durch das Einbinden modularisierter Webserverkomponenten in die eigene Applikation. Daher kann man eine Spring-Boot-Anwendung einfach mit `java -jar` starten. Dies ist besonders für heutige Cloud-Umgebungen geeignet. Außerdem konfiguriert Spring Boot für alle möglichen Anwendungsbereiche benötigte Komponenten schon vor. Wir nutzen Version 1.5.4. Spring Boot hat zahlreiche [Getting Started Guides](https://spring.io/guides), in denen bestimmte Techniken kurz eingeführt werden, allesamt mit fertigen Maven/Gradle-Projekten.

* Die [Spring PetClinic Sample Application](https://github.com/spring-projects/spring-petclinic) hingegen ist eine Beispiel-Web-Applikation, in der eine Reihe von Techniken sinnvoll kombiniert zum Einsatz kommen. Da sie fortlaufend weiterentwickelt wird, findet man jedoch auf ihrem Github-Home immer die aktuelle Snapshot-Version, die leider bei jedem Build erneut alle Artefakte bei allen Snapshot-Repositories erneut abfragt. Das ist für die Einarbeitung unhandlich.

* [Spring Petclinic Stable](https://github.com/ChristophKnabe/spring-petclinic-stable) ist mein Klon basierend auf der letzten stabilen Version. Sie nutzt Spring 4. Sie können diese auf jedem Rechner ausführen, auf dem der Java 8 JDK installiert und zugreifbar ist. Die Petclinic wollen wir auf verschiedenen Plattformen deployen und später auch modifizieren.

* Klonen Sie dieses Projekt mittels `git clone https://github.com/ChristophKnabe/spring-petclinic-stable.git`.  Die Applikation bringt ihr eigenes Build-Werkzeug **Maven** mit, da auf Linux-Distributionen zu oft eine veraltete Version installiert ist. Daher wird hier Maven mit `./mvnw` statt `mvn` aufgerufen.

* Bilden Sie es mittels
  `cd spring-petclinic-stable`
  `mvn package` oder wenn Ihr Maven älter als 3.1.1 ist, mittels
  `./mvnw package`.
  Es werden benötigte Packages downgeloadet, die Testsuite ausgeführt und das Produkt gebaut. Maven legt alle Generate unter `target/` ab. 

* Führen Sie die Spring Petclinic mit ihrem eingebauten Webserver aus mittels

  ` java -jar target/spring-petclinic-1.5.1.jar`

* Nach Start tätigt die Applikation eine ganze Menge Log-Ausgaben und meldet in der vorletzten Zeile
  `Tomcat started on port(s): 8080 (http)`

* Starten Sie einen Browser und gehen Sie zur Adresse http://localhost:8080/

* Die Petclinic ist eine Applikation, in der man Haustiere und Tierärzte erfassen und Behandlungstermine vereinbaren kann. In der Standardkonfiguration benutzt sie eine eingebettete Datenbank und trägt bei Programmstart einige Beispieldaten ein. Sie ist wie folgt technisch charakterisiert:

* **Persistenz**: Die Spring Petclinic arbeitet mit einer relationalen Datenbank, auf die mittels JPA (Java Persistence API) zugegriffen wird. Ohne Umkonfiguration ist dies die In-Memory-Datenbank HSQLDB. Dies ist konfiguriert in Datei `spring-petclinic-stable/src/main/resources/application.properties`
  Zum Umstellen auf die persistente MySQL-DB muss man es so wie in `application-mysql.properties` machen.

* **Webserver**: Spring Boot arbeitet mit einem eingebetteten Webserver namens **Tomcat**. D.h. wir müssen diesen nicht installieren, sondern können einfach die .jar-Datei starten.

* **User Interface**: Die Petclinic setzt als Web-Oberflächenframework **Spring Web MVC** und die Templating Engine **Thymeleaf** ein.

* Spielen Sie etwas mit den Funktionen der Spring Petclinic

* Zum Beenden klicken Sie wieder auf das Terminalfenster, in dem die Petclinic gestartet wurde und beenden sie mit **Ctrl/C**.

## Konnektivität

* **ssh** (Secure Shell) ist ein Werkzeug zum Herstellen einer sicheren Verbindung mit einem entfernten Rechner. Normalerweise startet dadurch ein Kommandointerpreter auf dem entfernten Rechner. ssh dient auch als Basis für Dienste wie Dateitransfer, Versionsverwaltung und Remote Desktop. Auf Linux-Systemen ist ssh normalerweise vorinstalliert. Auf Windows können Sie entweder ein Terminalprogramm wie **Putty** benutzen oder Sie nutzen ssh als Teil von Cygwin oder git. Z.B. nach der Installation von **Git for Windows** wählen Sie im Explorer auf einem Verzeichnis mittels MausRechts *Git Bash here*. Innerhalb des erscheinenden Kommandofensters können Sie dann ein ssh-Kommando eingeben.
* `compute.beuth-hochschule.de` ist der Login-Server des Hochschulrechenzentrums (HRZ). Melden Sie sich dort an mittels `ssh compute.beuth-hochschule.de`. Wenn Sie auf Ihrem Laptop einen anderen Username benutzen als beim HRZ, müssen Sie auch den HRZ-Username im ssh-Kommando angeben: `ssh username@compute.beuth-hochschule.de`. Sie werden zur Eingabe eines Kennworts aufgefordert. Geben Sie Ihr HRZ-Kennwort an.
* Stellen Sie die Linux-Kernelversion und die Distributionsversion des Zielsystems fest mittels `cat /proc/version`
* Zeigen Sie alle installierten Pakete an mittels `dpkg -l`
* Die Kennworteingabe kann man wie folgt einsparen:
* Schlüsselpaar generieren, falls noch nicht erfolgt: `ssh-keygen`
  Der Einfachheit halber alle Vorschläge akzeptieren inklusive der *empty passphrase*.
  Dies erzeugt im Verzeichnis `$HOME/.ssh` die private Schlüsseldatei `id_rsa` und die zugehörige öffentliche Schlüsseldatei `id_rsa.pub`.
* Den eigenen öffentlichen Schlüssel auf dem Zielrechner installieren. Dazu `ssh-copy-id username@compute.beuth-hochschule.de` ausführen. Dies kopiert den eigenen öffentlichen Schlüssel auf den Zielrechner in die Datei `$HOME/.ssh/authorized_keys`
  Detaillierte Beschreibung: https://www.ssh.com/ssh/copy-id
* Jetzt sollte der entfernte Login auf den Zielrechner ohne Kennworteingabe funktionieren.

## Spring Petclinic auf Laborserver ausführen

* Der Laborserver `host01` lauscht für ssh auf Port 60509 statt dem standardmäßigen 443. Wenn Sie außerhalb der Hochschule sind, können Sie aber keine Verbindung zu einem Port 60509 aufnehmen. Daher zunächst mittels ssh auf der `compute` einloggen.
* Dann von der compute aus `ssh host01 -p 60509`. Dies stellt eine Verbindung auf dem Port 60509 her. Wenn Sie mit Ihrem Laptop im WLAN der Hochschule sind, ist der Umweg über `compute` nicht nötig.
* Jetzt haben Sie ein Debian-Linux-System zur Verfügung, auf dem Java 8, git und Maven schon installiert sind. Klonen, bauen und starten Sie darauf die Spring Petclinic Stable wie oben beschrieben.
* Innerhalb der Hochschule können Sie zu http://host01:8080/ browsen und sehen wieder die Petclinic.
* Wenn Sie statt *Tomcat started on port(s): 8080 (http)* einen Portkonflikt gemeldet bekommen, läuft noch von einem anderen Benutzer ein Tomcat auf dem Server `host01`. Dann sollten Sie Spring einen Port zufällig wählen lassen: `java -jar spring-petclinic-1.5.1.jar --server.port=0` und es erscheint eine Meldung wie *Tomcat started on port(s): 48119 (http)*. Dann müssen Sie zu http://host01:48119/ browsen.