# Docker in der Cloud

Wir haben gesehen, dass mit einem einzigen `Dockerfile` ein komplettes System so beschrieben werden kann, dass daraus ein ausführbares Image erzeugt werden kann.

Solch ein Image wollen wir nun in der Cloud zur Ausführung bringen.

Dazu folgen wir wieder der Getting-Started-Anleitung von Docker ab dem Abschnitt [Share your image](https://docs.docker.com/get-started/part2/#share-your-image).

Wir müssen unser Docker-Image in eine von der Cloud erreichbare Registry bringen.

## Registry, Repository, Image, Layer

**Def.:** Eine **Registry** ist eine Sammlung von Repositories und ein **Repository** ist eine Sammlung von Images. 

Im Unterschied zu **git** wird in einem Repository kein Quellcode gespeichert, sondern das binäre Ergebnis der einzelnen Kommandos im `Dockerfile`.  

Jedes Kommando im Dockerfile erzeugt einen neuen **Layer**, der im lokalen Repository vorgehalten wird. Diese Layers können wir durch das Kommando `docker history petclinic` auflisten. Zwecks Sparsamkeit sollte man daher die mehreren Applikationen gemeinsamen Kommandos möglichst an den Anfang ihrer Dockerfiles platzieren. Ebenso ist es sinnvoll, Kommandos, die immer gemeinsam benötigt werden, in ein Docker-Kommando zusammenzufassen. Also statt

```
RUN apt-get update
RUN apt-get --yes upgrade
```

besser 

`RUN apt-get update && apt-get --yes upgrade`

## Anmelden bei einem Docker-Registry-Provider

* Registrieren Sie sich (Sign up) bei https://cloud.docker.com/
  Achtung: In der **Docker ID** dürfen Sie nur Kleinbuchstaben und Ziffern verwenden.
  Bestätigen Sie die an Sie gesandte E-Mail.
  Testen Sie den Login auf cloud.docker.com
* In Ihrem lokalen Kommandofenster melden Sie sich bei Docker an mittels
  `docker login`
  Sie müssen Ihre Docker ID (username) und Ihr Kennwort eingeben.

## Markieren und publizieren Sie Ihr Image

Die volle Bezeichnung eines Images auf Docker-Hub hat den Aufbau 
*username*`/`*repository*`:`*tag*

Der **Tag** ist optional. Er dient der Unterscheidung verschiedener Versionen. Wenn Sie keinen Tag angeben, wird jeweils `latest` vergeben.

Sie **markieren** Ihr lokales Image dockerhub-tauglich mittels 
`docker tag ` *image dockerUsername*`/`*repository*`:`*tag*

In meinem Fall z.B. mittels
`docker tag petclinic knabe/petclinic:ueb5`

Dies wirkt sich zunächst nur lokal aus. Sie können das Ergebnis betrachten mittels Kommando
`docker images`
Ausgabe z.B.:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
knabe/petclinic     ueb5                ea9dbe87e3ea        2 months ago        548MB
petclinic           latest              ea9dbe87e3ea        2 months ago        548MB
openjdk             8-jdk-slim          52de5d98a41d        3 months ago        244MB
friendlyhello       latest              3b52ff254ee5        4 months ago        148MB
```

Man kann sehen, dass das ältere lokale Image `petclinic` dieselbe IMAGE ID wie das für die Cloud-Platzierung vorgesehene.

Jetzt **laden** Sie Ihr Image **hoch** mittels `docker push` *username*`/`*repository*`:`*tag*

In meinem Fall also mit
`docker push knabe/petclinic:ueb5`

Das dauert einige Zeit. Es werden die Transfers der Layers des Images angestoßen, von denen mehrere parallel erfolgen. Man sieht, dass das Image **library/openjdk** nicht hochgeladen werden muss, da es von Docker bezogen wurde.

Wenn der Upload fertig ist, kann man nach Anmeldung unter https://hub.docker.com/ sein neu angelegtes, öffentliches Repository **petclinic** mit den darin enthaltenen Images sehen.

## Holen und Starten eines Images vom entfernten Repository

> **Achtung**: Auf Windows oder MacOS müssen Sie zuerst einmalig `docker-machine start` aufrufen und nach Abschluss Ihrer Arbeit sollten Sie die Docker-VM `default` wieder herunterfahren mittels `docker-machine stop`.

Ab jetzt können Sie Ihr Image auf jeder Maschine, auf der Docker installiert ist, mit folgendem Kommando holen und ausführen:

`docker run -p 8080:8080 ` *username*`/petclinic:ueb5`

Wenn Ihnen kein zweiter Rechner zur Verfügung steht, probieren Sie es auf dem Laborserver `host01.beuth-hochschule.de` (Zugang wie in Übung 1), auf dem wir Docker installiert haben.

Es erscheinen Meldungen wie

```
Unable to find image 'username/petclinic:ueb5' locally
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

xxx

