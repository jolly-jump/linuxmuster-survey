# linuxmuster-limesurvey

Limesurvey Docker App für linuxmuster. 

TESTVERSION - **NICHT PRODUKTIV VERWENDEN**

## Verwendung

Entweder du hast schon einen Dockerhost mit docker, docker-compose, dehydrated und nginx oder du erzeugst dirkurz einen Dockerhost mit create-docker-host: https://github.com/linuxmuster-ext-docker/create-docker-host

### Dienstenamen und Zertifikat

Zuerst musst du dir Dienstenamen ausdenken und SSL-Zertifikat besorgen. Also z.B. feedback.meine-schule.tld

* Lege einen DNS Eintrag für deine Dockerapp, z.B. feedback.meine-schule.tld, der auf die IP des Dockerhosts zeigt. Das darf auch ein CNAME sein.

### App herunterladen, konfigurieren und starten

* Klone dieses Repo auf deinen Dockerhost nach ``/srv/docker``
  * ``cd /srv/docker``
  * ``git clone https://github.com/linuxmuster-ext-docker/linuxmuster-limesurvey``
* Wechsle in das App-Verzeichnis: ``cd linuxmuster-limesurvey``
* Passe die Werte in der Datei ``limesurvey.ini`` an.
* Erzeuge eine Konfiguration mit: ``./deploy/bin/turnkey -c limesurvey.ini``
* Starte die App mit dem Befehl ``docker-compose up -d``

Jetzt solltest du dich an deinem Limesurvey unter der Adresse https://feedback.meine-schule.de/ anmelden können, so wie du deinen Service-Host und deine Service-Domain gewählt und konfiguriert hast.


