2018-04-23 Christoph Knabe

# Übung 3: Google Cloud - Compute Engine (CE)

**Google Cloud Platform** ist die Marke, unter der die gesamten Cloud-Aktivitäten von Google zusammengefasst sind.
**Compute Engine** ist das IaaS-Angebot der Google Cloud Platform.

## Vorbereitung

* Gehen Sie zu https://console.cloud.google.com/getting-started
  Dort werden Sie öfters Angebote für kostenloses Testen erhalten, bei denen Sie am Ende doch Ihre Kreditkartennummer angeben müssen.
  Bitte folgen Sie nicht diesem Weg! Wir haben für den Kurs von Google ein Budget bekommen, sodass Sie nicht in eine Kostenfalle fallen können.
* Klicken Sie rechts oben auf das Porträt-Icon und wählen Sie "Konto hinzufügen", wenn keines der angezeigten Google-Konten Ihnen passt. Sie kommen zu einem Dialog "Anmeldung". Erstellen Sie dort ein Konto mit Ihrer Hochschul-E-Mail-Adresse. (Sie können auch ein vorbestehendes Gmail-Konto oder ein anderes E-Mail-Konto benutzen. Der Zusammenhang zu Ihrem HRZ-Username oder Nachnamen muss aber für mich erkennbar sein, da ich nur die E-Mail-Adressen sehe.)
  Sie kommen dann zu einem Dialog "E-Mail-Adresse bestätigen" mit dem Text "Geben Sie den Bestätigungscode ein, der an IhreAdresse@provider.tld gesendet wurde. Falls Sie unsere E-Mail im Posteingang nicht finden, prüfen Sie bitte Ihren Spamordner."
  Meiner Erfahrung nach ist der Erhalt des Bestätigungscodes etwas unzuverlässig. Wenn Sie innerhalb einer Viertelstunde keine Nachricht erhalten (auch nicht im Spamordner), wiederholen Sie den Vorgang.
* Wählen Sie dann dieses Google-Konto für ihre Identität in der Google-Cloud-Konsole aus.
* Teilen Sie mir Ihr Google-Konto, Ihren Beuth-Username und Ihren Klarnamen mit.
* Ich werde für Sie ein Google-Cloud-Projekt mit Ihrem Vornamen und Nachnamen anlegen und Sie dazu einladen. Sie erhalten diese Einladung an die E-Mail-Adresse Ihrer für Google Cloud verwendeten Identität.
* Sie müssen diese Einladung durch einen Klick auf den darin enthaltenen Link bestätigen.

Hinweise zum Ablauf finden Sie unter 
https://lp.google-mkto.com/rs/248-TPC-286/images/GCPEDUInternationalFaculty.pdf
wobei diese eigentlich an Lehrkräfte gerichtet sind.

## Virtuelle Maschine anlegen

* Stellen Sie sicher, dass rechts oben Ihre richtige Google-Identität ausgewählt ist.
* Wählen Sie in der GCP-Konsole in der obersten Zeile auf der linken Seite rechts neben "Google Cloud Platform" bei "Projekt auswählen" das von mir für Sie angelegte Projekt, zu dem ich Sie eingeladen habe, aus. Ab dann beziehen sich alle Interaktionen auf dieses Projekt.
* Klicken Sie links oben auf das Icon "Produkte & Dienste" (3 horizontale Striche). Es erscheint links ein vertikales Kontextmenu beginnend mit "Startseite". Dort finden Sie unter COMPUTE die Menüpunkte *App Engine*, *Compute Engine*, *Kubernetes Engine* und *Cloud Functions*.
* Klicken Sie auf *Compute Engine*. Es erscheint links ein Unter-Kontextmenü.
* In diesem wählen Sie *Images* und können sehen, dass es viele Betriebssystemvarianten gibt, die angeboten werden. Darunter sind viele Linuxe (nicht alle am Namen erkennbar) und viele Windows-Varianten (von Microsoft).
* Im Compute-Engine-Submenü wählen Sie *Zonen*. Sie können sehen, dass Google in vielen Regionen der Welt Rechenzentren betreibt, in denen Sie virtuelle Maschinen buchen können. Die Zone `europe-west3`, Zone B liegt in Frankfurt am Main und sollte daher von uns präferiert werden.
* Im Compute-Engine-Submenü klicken Sie auf *VM-Instanzen* sowie dann in der erscheinenden zweitobersten Menüzeile auf "+ *Instanz erstellen*".
* Im Dialog VM-Instanz erstellen wählen Sie abweichend von der Vorbelegung
  **Name**: nachname-unterscheidungsmerkmal
  **Zone**: europe-west3-b (Frankfurt)
  **Maschinentyp**: Klein: g1-small (1 vCPU, 1,7 GB Speicherplatz)
  **Bootlaufwerk**: Neuer nichtflüchtiger Standardspeicher mit 10 GB, Image: Debian GNU/Linux 9 (stretch)
  **Firewalls**: HTTP-Traffic zulassen, als auch: HTTPS-Traffic zulassen
  Die Instanz wird erstellt und sofort gestartet und kostet, während sie läuft, stündlich 2,57 Cent. Daher bitte nach dem Testen jeden Tag wieder herunterfahren!
* Die VM-Instanz hat eine dauerhafte (Google-)Interne IP-Adresse und eine nach jedem Booten neu vergebene Externe IP-Adresse. Testen Sie, ob die VM erreichbar ist, in einem Terminalfenster mit `ping 35.198.88.248` (externe IP-Adresse: anpassen gemäß der Anzeige in der Liste Ihrer VM-Instanzen).
* Zeigen Sie auf die angezeigte Externe IP-Adresse, klicken Sie MausRechts und wählen Sie "in neuem Tab öffnen". Es stellt sich heraus, dass auf dieser VM kein Webserver läuft, der https-Verbindungen akzeptiert.

## Virtuelle Maschine ausrüsten (Provisioning)

Die Ausrüstung der Maschine mit der nötigen Software zum Betreiben einer Java-Web-Applikation werden wir hier manuell durchführen.

* Klicken Sie in der Cloud-Konsole in der Spalte *Verbinden* auf *SSH*. Es wird in einem neuen Browserfenster eine ssh-Terminalverbindung zur VM hergestellt. Dies macht für seltene Benutzung weniger Arbeit als manuell den eigenen öffentlichen Schlüssel auf der Zielmaschine zu registrieren.
* Probieren Sie die Befehle `pwd` und `ls -la`.
* Sie können die Terminalverbindung durch den Befehl `exit` oder die Tastenkombination &lt;Strg/D> beenden.
* Kopieren bzw. Einfügen von Textstücken erfolgen wie üblich mit &lt;Strg/C> bzw. &lt;Strg/V>. Wenn nichts markiert ist, bewirkt &lt;Strg/C> aber ein Beenden des aktuell im Terminalfenster laufenden Programms.
* Führen Sie im ssh-Fenster die Schritte des Kapitels *Werkzeuge Java, Maven, Git ... installieren* aus dem Dokument `vagrant-installation.md` durch. Testen Sie den Erfolg mit
  `java -version`
  `javac -version`
  `mvn -version`
  `git --version`

## Spring Petclinic einrichten

* Führen Sie die Schritte aus der Anleitung *Beispielprojekt Spring Petclinic ausführen* aus der Übung 1 durch (Klonen meiner stabilen Version; cd spring-petclinic-stable; mvn package). Sie können dabei einfacher `mvn package` aufrufen, da das Maven im gewählten Debian-Image genügend aktuell ist.
* Starten Sie den Webserver für die Petclinic-Web-Applikation durch den Befehl
  `java -jar target/spring-petclinic-x.x.z.jar`
  Dabei müssen Sie, da Sie auf Linux sind, den Dateinamen der Applikation nicht komplett eingeben. Es reicht aus, ihn bis zu `target/sp` einzugeben und dann mittels der Tabulatortaste ergänzen zu lassen.
* Sie können den Webserver mit &lt;Ctrl/C> beenden und müssen die VM dann mit dem Symbol ganz rechts wählen `Beenden`, damit über Nacht keine Kosten entstehen.

## Verbindung zur Petclinic aufnehmen

* Stellen Sie eine weitere ssh-Verbindung zur laufenden VM her.
* Testen Sie die lokale Erreichbarkeit des Webservers mittels `wget http://localhost:8080`
* Zeigen Sie die geholte Datei mittels `less index.html` an.

### Petclinic ohne Portangabe ansprechbar machen

**Wissen**: Kein Kunde möchte ein Web-Angebot über eine komplizierte URL mit numerischer Port-Angabe aufrufen. Stattdessen sollte der Webserver auf dem HTTP-Standard-Port 80 lauschen.
Es gibt dabei aber das Problem, dass auf Linux nur der User `root` Ports &lt; 1024 belegen darf. Native Programme können als `root` starten und nach Belegung des Ports auf einen nichtprivilegierten User umschalten. Java- und andere nichtnative Programme können das nicht.

Daher wird in der Praxis oft ein nativer Webserver vor den eigentlichen Webserver vorgeschaltet, der alle Requests auf Port 80 an Port 8080 weiterleitet. Traditionell war dies der Apache Webserver.

Meiner Erfahrung nach funktioniert jedoch dafür der vorgeschaltete sparsame und asynchrone Webserver **Nginx** am besten. Jetzt müssen Sie diesen richtig konfigurieren. 

* Bitte folgen Sie dazu der [Nginx-Proxy-Anleitung](nginx-proxy.md). Dabei ist in meiner stabilen Version des Spring-Petclinic-Projekts schon eine Datei `conf/nginx-proxy` abgelegt, die Sie nur noch symbolisch verlinken müssen.
* Testen Sie dann in der ssh-Verbindung mittels `wget http://localhost`, ob der Webserver in der Cloud jetzt korrekt auf Port 80 lauscht. Zeigen Sie die geholte Datei `index.html` (evtl. mit einer Folgenummer versehen) in der ssh-Verbindung an und prüfen Sie, ob sie von der Spring Petclinic generiert wurde.

Wenn es bis hierher gut ging, ist es an der Zeit, den Zugriff von außerhalb zu versuchen.

### Zugriff von außerhalb ermöglichen

* In der Google Cloud Console wird für eine laufende VM auch eine Externe IP-Adresse angezeigt. Diese ist leider numerisch und wechselt nach jedem Booten der VM. Wir werden aber dabei bleiben, da ein dauerhafter Servername auch dauerhaft Kosten produziert, selbst wenn die VM nicht läuft.
* Gehen Sie mit der Maus auf die angezeigte numerische Externe IP-Adresse. Es erscheint ein mit https: beginnender Link.
* Klicken Sie darauf. Dieser Link wird in einem neuen Tab geöffnet und schlägt fehl.
* Editieren Sie die angefragte URL in der Adresszeile des Browsers und ersetzen Sie http**s**: durch http:
* Jetzt sollte die Einstiegsseite der Petclinic erscheinen.

### Sicheren Zugriff mit https ermöglichen

Damit andere nicht die Anfragen an und Anworten von unserem Webserver einfach mithören können, sollten wir heutzutage nur noch HTTPS-Verbindungen verwenden. Diese müssen wir im erstannehmenden Webserver, also in Nginx konfigurieren.

Wie man das macht, ist auf der Seite https://nginx.org/en/docs/http/configuring_https_servers.html beschrieben. Im Wesentlichen müssen wir für den Server bei einer anerkannten Certification Authority (CA) ein Serverzertifikat beantragen und für Nginx bekannt machen.

Leider kommt dies für unseren Kurs nicht in Frage, da
1. das Vorgehen recht umständlich ist, und
2. solche Zertifikate nur für feste IP-Adressen oder Servernamen ausgegeben werden und diese bei Google Cloud dauerhafte Kosten verursachen.

## Zusammenfassung

Jetzt haben Sie erfolgreich in der Google Cloud 
* einen Debian GNU/Linux-Server eingerichtet,
* auf diesem die Spring Petclinic als WebApp ausgeführt und
* den Zugriff von außerhalb über Port 80 ermöglicht.


