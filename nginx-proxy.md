2018-05-09 Christoph Knabe

# Nginx als Reverse Proxy für eine WebApp

## Problem

Java- und andere Apps können auf Linux ohne root-Rechte nur auf Ports ab 1024 lauschen. Für WebApps ist allerdings Port 80 üblich. Es wäre aber zu gefährlich, eine WebApp als User `root` laufen zu lassen.

## Lösung

Nativ programmierte Applikationen können nach dem Binden an einen Systemport ihren Username von `root` herabstufen zu einem normalen User. Daher wird üblicherweise ein nativer, sogenannter Reverse Proxy vor die nichtnative WebApp vorgeschaltet. Traditionell war dies der Apache Webserver. Schlanker und effizienter ist aber der asynchrone `nginx`. Dieser wird häufig auch noch zum Load Balancing und zum Ausliefern statischer Ressourcen benutzt.

Wir folgen der Nginx-Anleitung [Setting Up a Simple Proxy Server](https://nginx.org/en/docs/beginners_guide.html#proxy).

### Nginx installieren und als Dienst starten

Ich beschreibe hier nur den Weg für Debian-Linux. Andere Linuxe sind bis auf die Paketverwaltung ähnlich.

`sudo apt-get install nginx-light`

Dieses installiert und startet den Webserver `nginx` und richtet ihn als Systemdienst ein, sodass er bei Systemstart automatisch mitgestartet wird.

Zur Kontrolle browsen wir zur Seite http://localhost
Es wird eine Begrüßungsseite von **nginx** angezeigt.

**Achtung**:  In der Cloud können wir nicht einfach zu http://localhost browsen, da wir keine GUI und somit keinen Browser zur Verfügung haben. Wir können aber die Funktionsfähigkeit auf der Kommandozeile testen mittels Kommando
`wget http://localhost`
Dies lädt die Einstiegsseite als Datei `index.html` ins aktuelle Arbeitsverzeichnis.

### Einige Dienstverwaltungsbefehle

`sudo service nginx stop` hält den Dienst an.

`sudo service nginx restart` fährt den Dienst runter und startet ihn erneut.

`sudo service nginx reload` bringt den Dienst dazu, seine Konfigurationsdateien erneut auszulesen.

### Nginx als Proxy vor einer WebApp betreiben

Wenn wir im einfachsten Fall alle Requests von Port 80 nach dem für Tomcat typischen Port 8080 weiterleiten wollen (und nicht statische Ressourcen direkt durch Nginx ausliefern lassen wollen), geht es sogar noch einfacher als in der Nginx-Doku beschrieben.

Wir legen eine Nginx-Konfig-Datei in unserem Projekt z.B. unter `conf/nginx-proxy` an:

```
server {
    location / { #Pass any request on port 80 to port 8080
        proxy_pass http://localhost:8080;
    }
}
```

Die Nginx-Konfigurationsdateien liegen unter `/etc/nginx/`.
In  `/etc/nginx/sites-available/` liegen Konfig-Dateien für alle potentiell verfügbaren Websites.
In  `/etc/nginx/sites-enabled/` liegen Konfig-Dateien für die aktuell freigeschalteten Websites. Anfänglich ist dort nur ein symbolischer Link namens `default` auf die in  `/etc/nginx/sites-available/default` konfigurierte Nginx-Begrüßungssite.

Jetzt müssen wir  in `sites-enabled` den symbolischen Link löschen und stattdessen einen auf unsere o.g. Datei `nginx-proxy`anlegen und dies nginx mitteilen.
**Achtung**: Ersetzen Sie den Pfadanteil `.../myproject` durch ihren spezifischen Pfadanteil zu Ihrem Projekt.

```
cd /etc/nginx/sites-enabled/
sudo rm default
sudo ln -s /home/.../myproject/conf/nginx-proxy .
sudo service nginx reload
```

Wenn wir nun erneut zu http://localhost browsen, sollten wir eine Seite mit der Fehlermeldung **502 Bad Gateway** sehen.
Wenn wir nun eine auf Port 8080 lauschende Applikation, wie z.B. eine Spring-Boot-Applikation, starten und dann zu http://localhost browsen, sollte diese schön angezeigt und benutzbar werden.

Mit den gleichen Aktionen können wir auf einem Debian-Linux-Server in der Cloud bewirken, dass eine Java-WebApp von außerhalb auch ohne Port-Angabe nutzbar ist.