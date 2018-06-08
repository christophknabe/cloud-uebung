2018-01-15 Christoph Knabe
# Heroku

**[Heroku](https://www.heroku.com/)** ist eine **Platform as a Service** (PaaS), um Applikationen in populären Programmiersprachen in der Cloud bilden und ausführen zu lassen.
Heroku bedient sich der Infrastruktur von Amazon Web Services (AWS). 

Relevante Abwägungen zwischen Heroku und AWS finden sich auf Quora in den Antworten unter [Why do people use Heroku when AWS is present? What distinguishes Heroku from AWS?](https://www.quora.com/Why-do-people-use-Heroku-when-AWS-is-present-What-distinguishes-Heroku-from-AWS)

Kurz gesagt, ist Heroku bequemer in der Benutzung, wohingegen AWS kostengünstiger und effizienter sein kann.

## Preismodelle
Hier die auf Seite https://www.heroku.com/pricing beruhenden Kategorien:

   -  **Free**: Bis 512 MB RAM, Postgres DB bis 10K Zeilen, Redis DB bis 25 MB RAM, 1 web + 1 worker process, schläft nach 30 Min. Inaktivität, 550 Stunden pro Monat inklusive, ohne Kreditkarte.
   -  **Hobby**: Für 7$/Monat immer aktiv mit bis zu 512 MB RAM, 10 Prozesse, DB-spezifische Preise.
   -  **Standard 1x, 2x, Performance M, L**: Für 25\$...500\$/Monat immer aktiv mit 512 MB...14 GB RAM und unbegrenzt vielen Prozessen, DB-spezifische Preise.

### Preisbewusstes Verhalten
Auf der Seite https://dashboard.heroku.com/account/billing kann man unter **Free Dyno Usage** die eigenen Applikationen sehen inklusive der von ihnen verbrauchten Dyno-Zeit.

-  Geben Sie die URL Ihrer Applikation nicht bekannt, damit sie nicht ungewollt durch Andere  wachgehalten wird!
-  Skalieren Sie Ihre Applikation nach Beendigung Ihrer Tests auf 0 *Dynos* mittels Kommando `heroku ps:scale web=0`
-  Mehr dazu: https://devcenter.heroku.com/articles/free-dyno-hours

## Getting Started on Heroku with Java durcharbeiten
Arbeiten Sie folgende Anleitung durch: https://devcenter.heroku.com/articles/getting-started-with-java

