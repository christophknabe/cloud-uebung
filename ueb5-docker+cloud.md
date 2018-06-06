2018-06-06 Christoph Knabe

# Docker in der Cloud

Wir haben gesehen, dass mit einem einzigen `Dockerfile` ein komplettes System so beschrieben werden kann, dass daraus ein ausführbares Image erzeugt werden kann.

Solch ein Image wollen wir nun in der Cloud zur Ausführung bringen.

Dazu folgen wir wieder der Getting-Started-Anleitung von Docker ab dem Abschnitt [Share your image](https://docs.docker.com/get-started/part2/#share-your-image).

Wir müssen unser Docker-Image in eine von der Cloud erreichbare Registry bringen.

## Registry, Repository, Image, Layer

**Def.:** Eine **Registry** ist eine Sammlung von Repositories und ein **Repository** ist eine Sammlung von Images. 

Im Unterschied zu **git** wird in einem Repository kein Quellcode gespeichert, sondern das binäre Ergebnis der einzelnen Kommandos im `Dockerfile`.  

Jedes Kommando im Dockerfile erzeugt einen neuen **Layer**, der im lokalen Repository vorgehalten wird. Diese Layers können wir durch das Kommando `docker history petclinic` auflisten. 

## Unterschiede zur Übung 4 (Docker lokal)

Der Dockerfile sollte bei der Entwicklung alleine oder mit möglichst wenig Dateien in einem Verzeichnis stehen, da alles im Verzeichnis in den Docker-Container aufgenommen wird.

Zwecks Sparsamkeit mit Layern sollte man die mehreren Applikationen gemeinsamen Kommandos möglichst an den Anfang ihrer Dockerfiles platzieren. Ebenso ist es sinnvoll, Kommandos, die immer gemeinsam benötigt werden, in ein Docker-Kommando zusammenzufassen. Also z.B. statt

```
RUN apt-get update
RUN apt-get --yes install maven git
```

besser 

`RUN apt-get update && apt-get --yes install maven git`

Gemäß den [Docker Best Practices](https://docs.docker.com/v17.09/engine/userguide/eng-image/dockerfile_best-practices/#run) sollte man wegen Cache-Problemen `apt-get upgrade` nicht durchführen, sondern bei Bedarf aktuellerer Versionen auf einem aktuelleren Basis-Image aufsetzen.

Wegen Komplikationen mit dem Ablauf mehrerer Prozesse in einem Docker-Container verzichten wir auf Nginx mit Port 80.

Am Ende muss das Kommando

```
docker build -t petclinic .
```

das Image so bauen, dass bei seiner Ausführung der Container auf Port 8080 lauscht (und dies an die Spring Boot App unter Port 8080 weitergibt). Also muss bei einer lokalen Ausführung mit

`docker run -p 8080:8080 petclinic`

die Applikation über http://localhost:8080 bzw. auf Windows entsprechend über die IP-Adresse der Docker-Machine ansprechbar sein.

## Anmelden bei einem Docker-Registry-Provider

* Registrieren Sie sich (Sign up) bei https://cloud.docker.com/
  Achtung: In der **Docker ID** dürfen Sie nur Kleinbuchstaben und Ziffern verwenden.
  Bestätigen Sie die an Sie gesandte E-Mail.
  Testen Sie den Login auf cloud.docker.com
* In Ihrem lokalen Kommandofenster melden Sie sich bei Docker an mittels
  `docker login`
  Sie müssen Ihre Docker ID (username) und Ihr Kennwort eingeben.

## Markieren und publizieren Sie Ihr Image

Die volle Bezeichnung eines Images auf Docker-Hub hat den folgenden Aufbau (ohne Zwischenräume): 

*username*`/`*repository*`:`*tag*

Der **Tag** ist optional. Er dient der Unterscheidung verschiedener Versionen. Wenn Sie keinen Tag angeben, wird jeweils `latest` vergeben.

Sie **markieren** Ihr lokales Image dockerhub-tauglich mittels 
`docker tag ` *image dockerUsername*`/`*repository*`:`*tag*

In meinem Fall z.B. mittels
`docker tag petclinic knabe/petclinic`

Dies wirkt sich zunächst nur lokal aus. Sie können das Ergebnis betrachten mittels Kommando
`docker images`
Ausgabe z.B.:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
knabe/petclinic     latest              ea9dbe87e3ea        4 minutes ago       559MB
petclinic           latest              ea9dbe87e3ea        5 minutes ago       559MB
openjdk             8-jdk-slim          52de5d98a41d        3 months ago        244MB
friendlyhello       latest              3b52ff254ee5        4 months ago        148MB
```

Man kann sehen, dass das ältere lokale Image `petclinic` dieselbe IMAGE ID wie das für die Cloud-Platzierung vorgesehene.

Jetzt **laden** Sie Ihr Image **hoch** mittels `docker push` *username*`/`*repository*`:`*tag*

In meinem Fall also mit
`docker push knabe/petclinic`

Das dauert einige Zeit. Es geht in der Hochschule bedeutend schneller als bei mir zu Hause. Es werden die Transfers der Layers des Images angestoßen, von denen mehrere parallel erfolgen. Man sieht, dass das Image **library/openjdk** nicht hochgeladen werden muss, da es von Docker bezogen wurde.

Wenn der Upload fertig ist, kann man nach Anmeldung unter https://hub.docker.com/ sein neu angelegtes, öffentliches Repository **petclinic** mit den darin enthaltenen Images sehen.

## Holen und Starten eines Images vom entfernten Repository

> **Achtung**: Auf Windows oder MacOS müssen Sie zuerst einmalig `docker-machine start` aufrufen und nach Abschluss Ihrer Arbeit sollten Sie die Docker-VM `default` wieder herunterfahren mittels `docker-machine stop`.

Ab jetzt können Sie Ihr Image auf jeder Maschine, auf der Docker installiert ist, mit folgendem Kommando holen und ausführen:

`docker run -p 8080:8080 ` *username*`/petclinic`

Wenn Ihnen kein zweiter Rechner zur Verfügung steht, probieren Sie es auf dem Laborserver `host01.beuth-hochschule.de` (Zugang wie in Übung 1), auf dem wir Docker installiert haben.

Es erscheinen Meldungen wie

```
Unable to find image 'username/petclinic:latest' locally
ueb5: Pulling from username/petclinic
8176e34d5d92: Pulling fs layer
...
e991b55a8065: Waiting
...
99f28966f0b2: Verifying Checksum
99f28966f0b2: Download complete
...
8176e34d5d92: Download complete
8176e34d5d92: Pull complete
...
```

Auch dieser Vorgang dauert abhängig von Ihrer Internet-Verbindung einige Zeit.

Jetzt sollten Sie wieder zu http://localhost:8080 browsen können.

> **Windows**: Auf Windows oder MacOS können Sie folgende Kommandos benutzen:
>
> * `docker-machine ip` zum Herausfinden der nichtroutbaren IP-Adresse der VM, bei mir 192.168.99.100

Mit `docker container ls` sehen Sie Ihren laufenden Container inklusive des Port Mappings.

Wenn Sie mit Browsen zu http://localhost:8080 (bzw. auf Windows zu http://192.168.99.100:8080) keinen Erfolg haben, sollten Sie zunächst mit ` curl 192.168.99.100:8080` probieren. Wenn das funktioniert, aber mit Ihrem Browser nicht, können Sie auch einen anderen Browser ausprobieren.

## Docker-Image in der Cloud ausführen

Wir wollen jetzt das gebaute Spring-Petclinic-Docker-Image auf der Google Compute Engine ausführen. Es gibt mehrere Möglichkeiten, Docker-Images oder andere Container auf GCE zu betreiben. Siehe https://cloud.google.com/compute/docs/containers/

Für den einfachsten Fall führen Sie bitte folgende Schritte durch:

* Melden Sie sich auf https://console.cloud.google.com/compute/instances an und wählen Sie Ihr Projekt aus.
* **Port 8080 in der Firewall erlauben**:
  - In der Cloud console links oben Navigation Menu &rarr; NETWORKING: VPC network &rarr; Firewall rules &rarr; Create firewall rule:
  - **Name**: http-8080
    **Beschreibung**: Tomcat bietet normalerweise seine Apps über 8080 an.
    **Direction of traffic**: Ingress
    **Action on match**: Allow
    **Targets**: All instances in the network
    **Sourcefilters: IP ranges:** 0.0.0.0/0
    **Protocols and Ports**: tcp:8080
    **Enforcement**: Enabled
* Erzeugen Sie eine neue GCE-VM analog zu Übung 3 mittels **+ CREATE INSTANCE** mit folgenden Eigenschaften:
  Name: frei wählbar
  Region: europe-west3 (Frankfurt)
  Machine type: small (1,7 GB memory)
  **Container: Deploy a container image to this VM instance**
  **Container image: *ihrDockerUsername*/petclinic**
  Die fettgedruckten Teile unterscheiden sich von [Übung 3](ueb3-google-cloud-ce.md).
  GCE wählt dann als Boot Image "Container-Optimized OS" aus (eine Linux-Variante mit vorinstalliertem Docker). 
  Firewall: Allow HTTP traffice, Allow HTTPS traffic.
* Diese VM wird automatisch gestartet (grüner Haken).
* Nach erfolgreichem Start klicken Sie auf die angezeigte **External IP**.
* Im Browser öffnet sich ein Fenster mit einer Fehlermeldung. Ersetzen Sie in der Adresse **https:** durch **http:** und hängen Sie an die Adresse **:8080** an.
* Es sollte die Petclinic-Oberfläche erscheinen.
* Öffnen Sie in der GCE-VM-Liste eine ssh-Verbindung zu Ihrer VM. Sie erhalten eine Shell mit dem Hinweis "You have logged in to the guest OS. To access your containers use 'docker attach' command". Sie sind also auf dem Linux, auf welchem Ihr Docker-Container läuft.
* Verifizieren Sie dieses mit dem Kommando `docker container ls`. Sie müssten Ihren Petclinic-Container sehen.
* Verbinden Sie die Ausgabe dieses Containers mit Ihrem Shell-Fenster durch `docker attach id` mit der richtigen ID des Containers.
* Jetzt klicken Sie im Browser in der Petclinic-Oberfläche auf **Error** zum Werfen einer Ausnahme. Sie müssten deren Stack Trace in Ihrem ssh-Fenster sehen.
* Sie können den Container wieder mit &lt;Strg/C&gt; abbrechen und die Shell mit Kommando `exit` beenden.
* Bitte stoppen Sie auch die VM-Instanz, um keine dauerhaften Kosten zu erzeugen.

Damit haben Sie erfolgreich ein Docker-Image erstellt und in der Cloud in Betrieb genommen.

## Einschränkung

Diese Lösung bietet die Petclinic über Port 8080 an. Wünschenswert wäre aber der Standardport 80. Dafür müsste man wie in [Übung 3](ueb3-google-cloud-ce.md) zusätzlich Nginx in dem Docker-Container oder in einem zweiten Docker-Container laufen lassen.

Beide Ansätze würden einen erhöhten Konfigurationsaufwand erfordern, den wir an dieser Stelle jedoch nicht treiben möchten.







