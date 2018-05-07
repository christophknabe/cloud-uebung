2018-05-04 Christoph Knabe
# Docker lokal einsetzen

## a) Docker-Installation und Test

**Docker** ist ein Containerisierungswerkzeug, um ein oder 
mehrere Programme in einer reproduzierbaren Umgebung ausführen zu können. Es verfolgt damit das gleiche Ziel wie Vagrant.

Wie Sie in dieser Übung selbst erfahren werden, erfolgt das Hochfahren eines Docker-Containers viel schneller als das Hochfahren einer virtuellen Maschine. Dies liegt daran, dass ein Docker-Container nicht das komplette Betriebssystem enthält, sondern nur die Unterschiede zum Wirtsbetriebssystem. Dies ist insbesondere für den Betrieb mehrerer Programme auf einem Cloud-Rechner kostengünstig oder zum Ausführen vorkonfigurierter Services auf einem Entwicklungsrechner gut geeignet. Docker hat viele Ideen von Vagrant übernommen, setzt diese allerdings viel effizienter um. Zum Siegeszug von Docker siehe z.B. den [Kommentar des innoQ-Consultants Eberhard Wolff](https://www.heise.de/developer/meldung/Kommentar-Docker-das-Ende-der-Virtualisierung-3949022.html) 

Es gibt aber eine **Einschränkung**: Docker funktioniert nur auf **Linux**-Systemen. Dies ist in der Cloud kein Problem, da die meisten Services ohnehin auf Linux laufen. Bei Entwicklungsrechnern ist dies nicht so. Diese sind of Windows- oder MacOS-Systeme. Daher wurde später Docker auch auf diese Systeme portiert. Es startet zuerst eine virtuelle Maschine mit Linux, was vergleichsweise lange dauert. Danach können Docker-Container in dieser VM superschnell gestartet werden. Der Bediener muss sich erfreulicherweise selbst nicht um die Einrichtung dieser VM kümmern.

Dokumentation: https://docs.docker.com

### Installation auf Linux Mint 17.2 (Ubuntu 14.04 LTS)
Dies folgt der Anleitung https://docs.docker.com/install/linux/docker-ce/ubuntu/ für die Docker Community Edition.

   - Frühere Versionen deinstallieren: `sudo apt-get remove docker docker-ce docker-engine docker.io`

   - Nicht nötig ab Ubuntu 16.04: Installiere die `linux-image-extra-*`-Pakete gemäß der Anleitung.

Konfiguriere das Docker-Repository gemäß  https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository :

   - Den apt-Package-Index aktualisieren:

      `sudo apt-get update`

   - https-Repository-Zugriff ermöglichen: `sudo apt-get install apt-transport-https ca-certificates curl software-properties-common`

   - Dockers GPG-Schlüssel hinzufügen: 
      `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

   - Erfolg prüfen: `sudo apt-key fingerprint`. Der voraussichtlich letzte Schlüssel der Ausgabe muss mit `pub   4096R/0EBFCD88 2017-02-22` beginnen und `Docker Release (CE deb) <docker@docker.com>` enthalten.

   - Das neue Docker-Repo hinzufügen: ` sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu trusty stable"`

   - Paketindex aktualisieren: `sudo apt-get update`

   - Aktuellste Version von Docker CE installieren: `sudo apt-get install docker-ce`

   -  Docker prüfen durch das HelloWorld-Image: `sudo docker run hello-world`

### Docker auf Linux ohne `sudo` aufrufbar machen
Dies folgt der Anleitung https://docs.docker.com/install/linux/linux-postinstall/ 

-  Gruppe `docker` erzeugen: `sudo groupadd docker`
-  Den User zur Gruppe `docker` hinzufügen: `sudo usermod -aG docker $USER`
-  Logout! Login! Damit die Gruppenzuteilung wirksam wird.
-  Testen mit Kommando: `id`
  Es werden folgende Infos gedruckt: die eigene `uid` und `gid` sowie alle Gruppen, zu denen der eigene User gehört. Darunter muss nun auch `docker` sein.
-  Testen mit: `docker run hello-world`

### Get Started Guide durcharbeiten
Dies folgt dem Guide unter https://docs.docker.com/get-started/
Davon Part 1 "Orientation" und Part 2 "Containers" bis ausschließlich Abschnitt "Share your image" durcharbeiten!

#### Netzwerkproblem
Im Abschnitt https://docs.docker.com/get-started/part2/#build-the-app tritt im Hochschulnetz beim Kommando `docker build -t friendlyhello .` folgender Fehler auf:

```
RUN pip install --trusted-host pypi.python.org -r requirements.txt
 ---> Running in efbf015470cb
Collecting Flask (from -r requirements.txt (line 1))
  Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by 'NewConnectionError('<pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x7f4e140c5dd0>: Failed to establish a new connection: [Errno -3] Temporary failure in name resolution',)': /simple/flask/
```
Gemäß https://stackoverflow.com/questions/44761246/temporary-failure-in-name-resolution-errno-3-with-docker, Antwort von **Jack Fan**, handelt es sich um ein Netzwerkproblem bei Aufenthalt des Laptops in einem Unternehmensnetzwerk. Er verweist auf den Beitrag https://stackoverflow.com/questions/24151129/docker-network-calls-fail-during-image-build-on-corporate-network und empfiehlt, in Datei `/etc/default/docker` in der Umgebungsvariablen DOCKER_OPTS eigene DNS-Server anzugeben, da der von Docker für Ubuntu vorgesehene DNS-Server 8.8.8.8 durch die Unternehmenskonfiguration blockiert wird. 

##### Lösung
* Auf Wirtsrechner mittels `ping dns.beuth-hochschule.de` die IP-Adresse des primären Beuth-DNS-Servers feststellen: 141.64.0.200
* Als User `root` in Datei `/etc/default/docker` die Umgebungsvariable DOCKER_OPTS aktivieren (# entfernen) und vorne um die Angabe `--dns 141.64.0.200` ergänzen.
* Den Docker-Service neu starten: `sudo service docker restart`
* Probeweise einen Docker-Container interaktiv mit bash starten:
  `docker run -it debian /bin/bash`
  und darin `ping scala-lang.org` probieren. Jetzt sollte es klappen.
* Dann sollte auch das Kommando `docker build -t friendlyhello .` klappen bis hin zur Meldung z.B.:
```
Successfully built 3b52ff254ee5
Successfully tagged friendlyhello:latest
```
* Die lokal verfügbaren Images auflisten: `docker image ls`

#### Ausführen
* Image-Existenz prüfen: `docker image ls`
  Die Ausgabe sollte auch das Image `friendlyhello` auflisten.
* Ausführen mit Port-Mapping:
  `docker run -p 4000:80 friendlyhello`
  Dann zu http://localhost:4000/ browsen. Man sieht die Hello-World-Botschaft, die der Webserver im Container auf Port 80 anbietet.
* Den Container beenden mit &lt;Strg/C&gt;

### Alternative: Installation von Docker auf Windows 10
Dies folgt der Anleitung unter https://docs.docker.com/docker-for-windows/install/

In dem **README FIRST** wird darauf hingewiesen, dass Docker for Windows die Virtualisierungslösung [Microsoft Hyper-V](https://de.wikipedia.org/wiki/Hyper-V) benutzt und damit Oracle VirtualBox funktionsunfähig wird.

Da wir aber Oracle VirtualBox für mindestens eine Übung benötigen (und da bei Hyper-V auch das Windows als Gast läuft, was Ihren Rechner permanent verlangsamen kann), wollen wir stattdessen die alternative Lösung [Docker Toolbox](https://docs.docker.com/toolbox/overview/) nutzen, die auf VirtualBox basiert.

* Gehen Sie zur Seite [Install Docker Toolbox on Windows](https://docs.docker.com/toolbox/toolbox_install_windows/)
* Folgen Sie dem Link "Get Docker Toolbox for Windows", der das Programm DockerToolbox.exe (211 MB) holt.
* Prüfen Sie Ihre Systemvoraussetzungen gemäß **Step 1**.
* Installieren Sie Docker Toolbox gemäß **Step 2**:
  - Alle Virtuellen Maschinen in VirtualBox herunterfahren.
  - Beenden Sie das VirtualBox GUI.
  - Führen Sie das oben geholte Programm **DockerToolbox.exe** aus.
  - Erlauben Sie Systemveränderungen.
  - Im Dialog **Select Components** wählen Sie **VirtualBox** und **Git for Windows** ab, falls diese schon installiert sind.
* Überprüfen Sie Ihre Installation gemäß **Step 3**:
  - Starten Sie über die Windows-GUI das **Docker Quickstart Terminal**.
    Es wird ein Terminalfenster geöffnet, darin das Verzeichnis **%USERPROFILE%\\.docker**  erzeugt und **boot2docker.iso** geholt.
  - Erlauben Sie dem **VirtualBox Interface** Änderungen am System.
  - Dann wird die virtuelle Maschine **default** gestartet und deren IP-Adresse angezeigt (bei mir 192.168.99.100).
  - Sie landen in einem MINGW64-Kommandoshell (unix-ähnlich).
  - Testen Sie mit `docker run hello-world`. Es sollte die Meldung `Hello from Docker` ... erscheinen. Wenn das funktioniert, ist **docker-machine**, basierend auf VirtualBox, funktionsfähig auf Ihrem Windows-Rechner.

### Alternative: Ohne Installation im Labor

Im SWE-Labor ist Docker auf dem Server `host01` installiert. Wenn Sie es nicht schaffen, sich Docker auf Ihrem eigenen Rechner zu installieren, können Sie es auf dem Laborrechner benutzen. Dazu müssen Sie sich gemäß der Anleitung https://gitlab.beuth-hochschule.de/SWE/info auf dem Server 141.64.18.193 (host01) anmelden oder auf ihn mittels ssh zugreifen wie in [Übung 1](https://github.com/ChristophKnabe/cloud-uebung/blob/master/ueb1-git-maven-ssh.md#spring-petclinic-auf-laborserver-ausf%C3%BChren).

Prüfen Sie dort bitte mittels o.g. mit Kommando: `id`, ob Sie auch in der Gruppe `docker` sind. Wenn noch nicht, bitten Sie den Laboringenieur, Herrn Shahbaz, Sie der Gruppe hinzuzufügen.

### Get Started Guide durcharbeiten

Dies folgt dem Guide unter https://docs.docker.com/get-started/

Davon Part 1 "Orientation" und Part 2 "Containers" bis ausschließlich Abschnitt "Share your image" durcharbeiten!


## b) Aufgabe: Docker nutzen

Sie sollen die Schritte aus dem Kapitel "Docker-Installation und Test" analog nachvollziehen, um die **Spring-Petclinic** lokal **in** einem **Docker**-Container auszuführen, sodass Sie die Petclinic auf dem Wirtssystem Ihres Rechners unter der URL http://localhost benutzen können.

Im Wesentlichen müssen Sie in einem neuen Verzeichnis einen `Dockerfile` erstellen und darin das aufnehmen, was Sie in einer früheren Übung mit Vagrant im Skript `provision.sh` durchgeführt hatten. Dabei gibt es einige syntaktische Unterschiede. Alle Docker-Instruktionen beginnen mit einem durchgängig GROSS geschriebenen Wort. Dabei werden alle Instruktionen nur beim Erzeugen des Images (**Provisioning**) ausgeführt. Einzig die CMD-Instruktion wird bei jedem Starten eines Containers ausgeführt. Sie sollten also möglichst alle zeitaufwendigen Aktionen in die Provisioning-Phase vorziehen.

Das Provisioning eines Docker-Images geschieht anfangs als User `root`, da man ja oft bestimmte Pakete installieren muss. Wenn Sie die root-Rechte nicht mehr benötigen, sollten Sie mittels der Instruktion USER den aktuellen User wechseln.



### Anforderungen

* Legen Sie das offizielle Docker-Image `openjdk:8-jdk-slim` zugrunde. Dieses basiert auf Debian 9 und enthält schon den OpenJDK 8 (headless).  Die Liste aller offiziellen Docker-Images mit OpenJDK finden Sie unter https://hub.docker.com/_/openjdk/

* Sorgen Sie im Dockerfile zunächst durch `apt-get update` und `apt-get upgrade` dafür, dass alle Debian-Pakete aktuell sind, bevor Sie die Pakete `maven` und `git` installieren.

* Auf dem Wirtssystem muss der Befehl `docker build --tag petclinic .` zur Erzeugung des Images fehlerfrei durchlaufen.

* Danach muss auf dem Wirt der Befehl `docker run -p 80:8080 petclinic` die Applikation starten, sodass Sie sie durch Browsen zu http://localhost  (bei Docker Toolbox auf Windows: http://192.168.99.100 oder welche IP-Adresse bei Ihnen für die virtuelle Maschine **default** angezeigt wurde) bedienen können. Wenn das funktioniert, sehen Sie in der Konsolenausgabe *FrameworkServlet 'dispatcherServlet': initialization completed in 84 ms* und im Browser die Petclinic-Oberfläche.

* Wenn das funktioniert, modifizieren Sie den `Dockerfile`so, dass die Applikation nicht mehr unter dem User `root`, sondern einem nichtprivilegierten Username läuft. Dieses ist begründet in
  https://blog.csanchez.org/2017/01/31/running-docker-containers-as-non-root/
  und dort wird der User **nobody** (uid **65534**) in dem Verzeichnis **/tmp** vorgeschlagen, weil beide auf den meisten Linux-Systemen vordefiniert sind.
  Abweichend von der im genannten Beitrag beschriebenen Problemlage können wir diese Festlegungen in den `Dockerfile` eintragen, da wir ihn selbst erstellen.
  Benutzen Sie folgende Instruktionen dafür
  \# Aktuellen Benutzer auf *username* setzen:
  USER *username*
  \# Aktuelles Arbeitsverzeichnis festlegen:
  WORKDIR *neuesArbeitsverzeichnis*
  \# Umgebungsvariable definieren:
  ENV *NAME* *value*

  Sie sollten auch die Umgebungsvariable JAVA_TOOL_OPTIONS auf `-Duser.home=/tmp` setzen, damit alle Java-Programme denken, das Home-Verzeichnis sei `/tmp`.

#### Beenden

Auf Windows wird das Terminierungssignal bei &lt;Strg/C&gt; nicht zum Linux-Gast durchgereicht, sondern unterbricht nur die ssh-Verbindung zu diesem. Der Webserver mit der Spring-Petclinic läuft also weiter.  Also:

* <Strg/C> oder einen neuen Shell öffnen, um in den Bedienshell auf der Wirtsmaschine zu kommen.
* Mittels `docker container ls` alle laufenden Container auflisten. In der ersten Spalte werden hexadezimale CONTAINER IDs und in der letzten deren Klarnamen angezeigt.
* Sodann mit `docker stop id` den entsprechenden Container herunterfahren. Dabei reicht es aus, einen eindeutigen Anfang der ID einzugeben.
* Erneut starten wieder mit `docker run -p 80:8080 petclinic`. Dabei werden Sie merken, dass der Start des Linux in Nullkommanichts erfolgt (die Docker-VM lief ja noch). Nur der Start der Java-Applikation dauert seine Zeit.
* Wenn man die Arbeit beendet, sollte man auf Windows auch die von Docker benutzte virtuelle Maschine beenden. Dazu `docker-machine ls` um alle anzuzeigen und `docker-machine stop` um die einzig laufende Maschine `default` zu beenden.
