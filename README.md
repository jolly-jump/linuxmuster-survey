# linuxmuster-survey

Limesurvey Docker App für linuxmuster. 

Die Idee ist Limesurvey als Umfrage und Feedback-Tool zu verwenden. Die Standardkonfiguration sieht vor, dass sich Lehrer anmelden und loslegen können und dass Schüler bei Bedarf (zum Beispiel für nicht-anonyme Umfragen) Klassen/Projektweise vordefiniert existieren und nur noch als Teilnehmer zu einer Umfrage hinzugefügt werden müssen.

TESTVERSION - **NICHT PRODUKTIV VERWENDEN**

## Verwendung

Entweder du hast schon einen Dockerhost mit docker, docker-compose, dehydrated und nginx oder du erzeugst dirkurz einen Dockerhost mit create-docker-host: https://github.com/linuxmuster-ext-docker/create-docker-host

### Dienstenamen und Zertifikat

Zuerst musst du dir Dienstenamen ausdenken und SSL-Zertifikat besorgen. Also z.B. feedback.meine-schule.tld

* Lege einen DNS Eintrag für deine Dockerapp, z.B. feedback.meine-schule.tld, der auf die IP des Dockerhosts zeigt. Das darf auch ein CNAME sein.

### Voraussetzungen

- installiere python3-ldap3 ``apt install python3-ldap3``

### App herunterladen, konfigurieren und starten

* Klone dieses Repo auf deinen Dockerhost nach ``/srv/docker``
  * ``cd /srv/docker``
  * ``git clone https://github.com/jolly-jump/linuxmuster-survey``
* Wechsle in das App-Verzeichnis: ``cd linuxmuster-survey``
* Passe die Werte in der Datei ``limesurvey.ini`` an.
* Erzeuge eine Konfiguration mit: ``./deploy/bin/turnkey -c limesurvey.ini``
* Führe ``dehydrated -c`` aus, um für diese Domäne ein LE-Zertifikat zu besorgen
* Starte die App mit dem Befehl ``docker-compose up -d``

Jetzt solltest du dich an deinem Limesurvey unter der Adresse https://feedback.meine-schule.de/ anmelden können, so wie du deinen Service-Host und deine Service-Domain gewählt und konfiguriert hast.

### Anpassung der limesurvey.ini

#### Abschnitt [setup]

* Die Variable "ldapusers" schränkt die Einloggmöglichkeit auf Lehrer ein (standard) oder hebt die Einschränkung mit dem Wert "all" auf.
* Die Variable "ldapusers_extra_rights" bestimmt, was die LDAP user beim ersten Einloggen dürfen:
```
# LDAP User bekommen automatisch bei erstem Login folgende Rechte
# create - LDAP user bekommen (immerhin) die Rechte, eigene anzulegen und zu managen
# read - LDAP user bekommen die Rechte, alle Umfragen einzusehen (und eigene anzulegen und zu managen)
# manage - LDAP user bekommen die Rechte, alle Umfragen zu managen
# superadmin - LDAP user bekommen alle superadmin Rechte (Vorsicht!) 
ldapusers_extra_rights=read
```
#### Abschnitt [groups]

- Hier werden alle Gruppen einfach Zeile für Zeile aufgelistet, für die Teilnehmer-Untergruppen als LDAP-Abfragen angelegt werden sollen. Standardmäßig stehen da die üblichen Bezeichner für Klassen, aber alle Projekte können hinzugefügt werden.
- Nicht existente Klassen/Projekte werden ignoriert
- Lehrer werden grundsätzlich aus der Abfrage herausgefiltert.
```
## Groups for participants (not log-in users) of students
# teachers werden  ausgefiltert, wenn man das Template ldap.group.php-template nicht editiert !(gidNumber=10000)
[groups]
# add all projects you might want to choose from
p_wifi
p_sudo
# add all classes you might want to choose from
5a
5b
5c
5d
5e
5f # there is a check, and the class only added, if it exists
6a
6b
6c
```
