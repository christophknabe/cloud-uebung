Christoph Knabe, 2018-04-25

# Übung 2: Virtualisierung mit VirtualBox und Vagrant

**Virtualisierung** beim Rechnerbetrieb meint die Darstellung eines Gast-Rechners (guest) und Betriebssystems mittels einer Abstraktionsschicht auf einem Wirtsrechner (host). Diese stellt eine Vorstufe des CloudComputing dar.

Das Ziel dieser Übung ist es, in einer virtuellen Maschine das Betriebssystem Ubuntu Server 16.04 zu installieren, darauf die Spring Petclinic zu bilden und auszuführen und vom Wirtssystem mittels Browser zu benutzen.

Dazu müssen wir folgende Installationen vornehmen. Diese weichen natürlich voneinander ab, wenn Ihr Laptop mit einem anderen Betriebssystem läuft als meiner. Dann orientieren Sie sich bitte an den im Netz auffindbaren Anleitungen für Ihr Betriebssystem.

## a) VirtualBox

[VirtualBox](https://www.virtualbox.org/) von Oracle ist eine kostenlose Umgebung, die die Virtualisierungsfähigkeiten moderner Intel/AMD-Prozessoren nutzt, um Gastbetriebssysteme auf einem Wirtssystem auszuführen.  Kommerzielles Alternativprodukt ist z.B. VMware. Siehe https://de.wikipedia.org/wiki/Liste_von_Virtualisierungsprodukten

Man kann damit z.B. ein Linux-System auf einem Windows-Rechner oder umgekehrt ausführen.

Für die Installation von VirtualBox muss man die Variante für das eigene Wirts-Betriebssystem auswählen und installieren. Siehe https://www.virtualbox.org/wiki/Downloads

### Installation auf Wirtssystem Linux Mint (Ubuntu-Dialekt) aus den Paketquellen

Entgegen der Empfehlung auf der VirtualBox-Seite hatte ich besseren Erfolg damit, die in der Ubuntu-Distribution enthaltene VirtualBox-Version zu installieren und zu benutzen.

1. Falls man schon mal andere Installationsversuche von VirtualBox hatte:
   Die VirtualBox-Paketquelle aus `/etc/apt/sources.list` bzw. aus allen Dateien in `/etc/apt/sources.list.d` entfernen. Mit Synaptic alle installierten VirtualBox-Pakete vollständig entfernen. Es stehen danach immer noch mehrere VirtualBox-Versionen von Ubuntu zur Verfügung. 
2. Gemäß https://www.linuxmintusers.de/index.php?topic=33576.msg471132#msg471132 werden mehrere VB-Pakete benötigt. Mit `virtualbox-5.0` hatte ich kein Glück und es fehlen in den offiziellen Linux-Mint-Paketquellen die benötigten Zusatzpakete. Daher nehme ich die, wenn auch ältere, Version 4.3.36, da für sie alle Zusatzpakete vorhanden sind.
   In Synaptic die Pakete `virtualbox`, `virtualbox-dkms`,  `virtualbox-guest-dkms`, `virtualbox-guest-utils`, `virtualbox-qt` und `virtualbox-guest-additions.iso`installieren.
3. Da wir VirtualBox nur über Vagrant und nur mittels Komamndozeile benutzen werden, sind die folgenden Schritte wahrscheinlich nicht notwendig.
4. Für Zusatzfunktionalität wie USB wird auch das wirtsspezifische **VirtualBox Extension Pack** benötigt. Gemäß https://wiki.ubuntuusers.de/VirtualBox/Installation/ von Seite https://www.virtualbox.org/wiki/Download_Old_Builds die historische Version 4.3.6 (all platforms) geholt und mit VirtualBox geöffnet. Dadurch wird es installiert.
5. Jetzt kann man sich eine Gastsystem-ISO-Datei downloaden, eine neue VM anlegen und von der .iso-Datei das Gastsystem installieren.
6. Verzeichnis `$HOME/VirtualBox VMs/`*machineName*`/`*machineName*`.vbox` mit Oracle VM VirtualBox öffnen.
7. Als SharedFolder festlegen: Name `host-knabe`, Pfad auf Host `/home/knabe`

#### Alternativ auf Windows 10 VirtualBox  installieren

1. Wenn vorhanden: Ältere Version von VirtualBox für Windows deinstallieren.
2. Von http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html den neusten Windows-Installer (Version 5.2.8 am 17.04.18) holen und möglichst seine SHA256-Checksum mit der Angabe auf der Oracle-Website vergleichen. Den Installer  ausführen, dabei Systemveränderung erlauben.
3. Das aktuelle **Virtual Box Extension Pack** holen und installieren.
4. Jetzt kann man sich manuell eine .iso-Datei holen und eine virtuelle Maschine davon booten lassen. Einfacher aber ist dies über Vagrant. Da wir die VM nur über die Kommandozeile benutzen werden, sind in der installierten VM keine *Guest Additions* nötig.

## b) Vagrant-Installation

**Vagrant** ist ein Kommandozeilenwerkzeug zur Verwaltung virtueller Maschinen oder Container. Es setzt auf Providern wie VirtualBox, VMware, AWS oder Docker auf. Der Vorteil von Vagrant gegenüber der direkten Verwendung der spezifischen GUIs oder Kommandozeilenwerkzeuge der unterschiedlichen Provider ist die einheitliche Schnittstelle, über die die Provider angesprochen werden können.

Vagrant erlaubt die Definition einer auszuführenden virtuellen Maschine mit der zu installierenden Software in einer einfachen Textdatei, dem `Vagrantfile`. Früher wurden stattdessen oft Images virtueller Maschinen von mehreren Gigabytes verschickt, wenn Sie eine bestimmte VM auf einem anderen Rechner reproduzieren wollten. Vagrant ist in Ruby geschrieben und dementsprechend ist der Vagrantfile in Ruby-Syntax.

### Getting Started mit der aktuellsten Version

Wir folgen https://www.vagrantup.com/intro/getting-started/ .

1. Installation eines Providers: Auf Windows oder Mac sollte dies eine Virtualisierung wie [VirtualBox](https://www.virtualbox.org/) sein. Auf Linux würde eine sparsame Containerverwaltung wie [Docker](https://www.docker.com/what-docker) ausreichen, wir werden hier aber dennoch übungshalber eine komplette VM mit VirtualBox benutzen.
2. Wir folgen der Empfehlung von Vagrant, seine neueste Version zu installieren, passend für die Wirtsplattform (bei mir Debian 64-bit). Es wird Version 2.0.3 installiert.
3. Ein Vagrant-Projekt einrichten:
   `mkdir meinProjekt`
   `cd meinProjekt`
   `vagrant init`
   Dies erzeugt eine Datei `Vagrantfile`und ein Unterverzeichnis `.vagrant` im Projektverzeichnis.
4. Eine Vagrant-Box installieren:
   `vagrant box add ubuntu/xenial64` (aktuelle Version von Ubuntu Server 16.04 LTS)
   Es wird die Variante für VirtualBox geholt und installiert (dauert einige Minuten). [Auf Windows 10 musste ich in der Firewall Ruby den Zugriff nach außen auch in öffentlichen Netzen erlauben.]
5. Die Box für das Projekt benutzen: 
   Ändern Sie in dem `Vagrantfile` die entsprechende Stelle zu
   `config.vm.box = "ubuntu/xenial64"`
6. Die Box starten:
   `vagrant up`
   Das dauert einige Minuten, da das Betriebssystem der Gast-VM (Ubuntu) gestartet werden muss. Es erscheinen viele Meldungen dazu, z.B. zu Shared Folders und zu ssh-Verbindungen.
   Der VM wird unter dem Pfad `/vagrant` Zugriff auf das Projektverzeichnis auf dem Wirtssystem gegeben.
7. ssh-Zugriff:
   `vagrant ssh`
   Dies öffnet eine Secure Shell auf der VM. Nun kann man der VM beliebig Befehle erteilen, z.B.
   `id` zeigt den aktuellen Benutzernamen an,
   `ls -la /vagrant` zeigt das Projektverzeichnis des Wirtssystems an.
   `logout` 
   beendet die ssh-Verbindung zur VM. Die VM läuft aber weiter, was man sehen kann, wenn man die VirtualBox-Manager-GUI startet. 
8. Terminierung/Aufräumen:
   - `vagrant suspend`  friert den VM-Zustand auf der Festplatte ein. Es wird dann kein Arbeitsspeicher mehr belegt.
   - `vagrant halt` fährt das Gastsystem herunter. Es wird kein zusätzlicher Platz für den aktuellen Zustand der VM auf der Festplatte benötigt.
   - `vagrant destroy` räumt noch gründlicher auf. Nur die heruntergeladene Box bleibt auf dem Rechner. Das Hochfahren dauert wegen dem erneuten Provisioning aber entsprechend länger.

## c) Werkzeuge Java, Maven, Git auf Gast installieren

Diese für Java-Entwicklung benötigten Werkzeuge können wie folgt installiert werden. Erneut mit `vagrant ssh` sich auf dem Gastsystem anmelden. Vor den Installationen  die Paketlisten mit APT (A Packaging Tool) aktualisieren:
`sudo apt-get update`

Sodann die neueren Paketversionen installieren:
`sudo apt-get upgrade`

### Java Development Kit installieren

1. JDKs suchen:
   `apt-cache search jdk`
   listet viele JDK-bezogene Pakete auf.

2. Da im Serverbereich OpenJDK deutlich mehr verbreitet ist als Oracle JDK, den OpenJDK ohne GUI-Bibliotheken (headless) installieren:

   `sudo apt-get install openjdk-8-jdk-headless`
   Prüfen mit 
   `java -version`und `javac -version`

### Java-Build-Werkzeug Maven installieren

1. Maven-Paket suchen:
   `apt-cache search maven`
   findet das Paket  als 
   `maven - Java software project management and comprehension tool`.
2. Maven installieren:
   `sudo apt-get install maven`
3. Prüfen mit 
   `mvn -version`
   zeigt `Apache Maven 3.3.9` mit Angabe der Java-Version.

### Versionierungssystem Git installieren

1. Git suchen, da lange Liste, sortiert und seitenweise:
   `apt-cache search git | sort | less`
   findet das Paket  als 
   `git - fast, scalable, distributed revision control system`
2. Git installieren:
   `sudo apt-get install git`
   zeigt **git** als schon installiert.
3. Git überprüfen:
   `git --version`
   zeigt `git version 2.7.4`

## d) Zusammenfassung Installationen

Damit haben wir 

- VirtualBox auf dem Wirtssystem installiert
- Vagrant auf dem Wirtssystem installiert,
- als Gast eine Vagrant-Box mit aktuellem Ubuntu Server eingerichtet und
- auf dieser die für Java-Entwicklung nötigen Kommandos `java`, `javac`, `mvn` und `git` bereitgestellt. Diesen letzten Abschnitt macht man normalerweise im Provisioning-Abschnitt des `Vagrantfile`, damit er automatisch reproduziert werden kann.
- Das Hochfahren einer Vagrant-Box dauert wegen des Betriebssystemstarts einige Minuten.
- Eine laufende Vagrant-Box benötigt, da sie ein volles Betriebssystem enthält, vergleichsweise viel Platz.

## e) Benutzung des Gasts

### Port für Web-Applikationen verfügbar machen

1. Eine Vagrant-Box mit Java, Maven und Git versehen und hochfahren, mit ssh einloggen (siehe Vagrant-Installation).
2. In den Ordner `/vagrant` das Projekt https://github.com/ChristophKnabe/spring-petclinic-stable klonen.
3. Mit cd hineingehen.
4. Mittels `mvn package` die ausführbare Java-Datei bauen.
5. Mittels `java -jar target/spring-petclinic-x.y.z.jar` ausführen.
   Startet den Webserver Tomcat auf Port `8080`, der die Spring Petclinic als Web-Applikation anbietet.
6. Da wir in der VM keine GUI zur Verfügung haben, müssen wir auf die Web-Applikation mit einem Browser des Wirtssystems zugreifen. Dafür muss der Port 8080 exportiert werden. In Vagrantfile eintragen:
   `config.vm.network :forwarded_port, guest: 8080, host: 8080`
7. Damit das Effekt hat, die Java-Applikation mittels <Strg/C> abbrechen und
   `vagrant reload` (vagrant halt; vagrant up) ausführen.
8. Auf der Wirtsmaschine zu http://localhost:8080 browsen. Es erscheint die Spring-Petclinic-GUI.

### Konfiguration als Code (Provisioning)

Einer der großen Vorteile von Vagrant ist die Vermeidung des "Not working here"-Syndroms. D.h. alle notwendigen Konfigurationen werden in einer versionierten Datei festgehalten und können auf einer anderen Plattform exakt so wieder reproduziert werden.

Daraus folgt, dass neben der Auswahl der grundlegenden Vagrant-Box auch die nötigen Downloads und Installationen als Code beschrieben werden sollen. Diesen Vorgang - Ausrüstung der VM - nennt man **Provisioning**.

Wir schreiben also im Projektverzeichnis (dem mit dem Vagrantfile) ein Shell-Skript `provision.sh`, in welches wir an den Stellen ... die nötigen Befehle aus dem Abschnitt *Werkzeuge Java, Maven, Git auf Gast installieren* eintragen. Dabei bitte alle Aufrufe von `apt-get` zur Vermeidung von Bestätigungsnachfragen versehen mit der Option `--yes`, also z.B. `sudo apt-get --yes upgrade`:

    :
    #Provisioning of this VM for Java development.
    # Paketlisten aktualisieren:
    ...
    # Neuere Paketversionen installieren:
    ...
    # openjdk-8-jdk-headless installieren:
    ...
    # maven installieren:
    ...
    # das Petclinic-Projekt innerhalb von /vagrant klonen:
    ...
    # das Petclinic-Projekt paketieren:
    ...

Das Skript sollte so formuliert sein, d.h. dass es bei Ausführung auf einem Verzeichnis, in dem nur der Vagrantfile vorhanden ist, fehlerfrei durchläuft.

Dieses Skript zunächst innerhalb der VM testen. Wenn es erfolgreich durchläuft, die installierten Pakete deinstallieren und es erneut testen.

Jetzt noch dieses Skript in den Vagrantfile in der Zeile vor `end` als Provisioning eintragen:

`config.vm.provision :shell, path: "provision.sh"`

Danach außerhalb der VM:

    vagrant destroy
    vagrant up

Jetzt sollte die VM beim erstmaligen Hochfahren automatisch mit Java, Maven und Git versorgt werden und die Petclinic klonen und bauen und damit richtig portabel sein.

Wir starten dann die Petclinic vom Wirtssystem aus mittels 
    vagrant up
    vagrant ssh
    cd /vagrant/spring-petclinic-stable
    java -jar target/spring-petclinic-1.5.1.jar




