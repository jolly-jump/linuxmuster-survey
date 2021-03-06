# linuxmuster-survey

Limesurvey Docker App für linuxmuster. 

Die Idee ist Limesurvey als Umfrage und Feedback-Tool zu
verwenden. Die Standardkonfiguration sieht vor, dass sich Lehrer
anmelden und loslegen können und dass Schüler bei Bedarf (zum Beispiel
für nicht-anonyme Umfragen) klassen-/projektweise vordefiniert
existieren und nur noch als Teilnehmer zu einer Umfrage hinzugefügt
werden müssen.

## Verwendung

Entweder du hast schon einen Dockerhost mit docker, docker-compose,
dehydrated und nginx oder du erzeugst dir kurz einen Dockerhost mit
create-docker-host:
https://github.com/linuxmuster-ext-docker/create-docker-host

### Dienstenamen und Zertifikat

Zuerst musst du dir einen Dienstenamen ausdenken und ein SSL-Zertifikat besorgen. Also z.B. feedback.meine-schule.tld

* Lege einen DNS Eintrag für deine Dockerapp, z.B. feedback.meine-schule.tld, der auf die IP des Dockerhosts zeigt. Das darf auch ein CNAME sein.
* Trage diesen Host in die Datei ``/etc/dehydrated/domains.txt`` ein.
* Führe den Befehl ``dehydrated -c`` aus. Jetzt hast du die Zertifikate im Verzeichnis ``/var/lib/dehydrated/certs/<hostname>/`` zur Verfügung, der Docker Host aktualisiert diese per Cronjob.

### Voraussetzungen

- installiere python3-ldap3 ``apt install python3-ldap3`` auf dem docker-host

### App herunterladen, konfigurieren und starten

* Klone dieses Repo auf deinen Dockerhost nach ``/srv/docker``
  * ``cd /srv/docker``
  * ``git clone https://github.com/jolly-jump/linuxmuster-survey``
* Wechsle in das App-Verzeichnis: ``cd linuxmuster-survey``
* Passe die Werte in der Datei ``limesurvey.ini`` an ([siehe unten](#anpassung-der-limesurveyini))
* Erzeuge eine Konfiguration mit: ``./deploy/bin/turnkey -c limesurvey.ini``
* Starte die App mit dem Befehl ``docker-compose up -d``

Jetzt solltest du dich an deinem Limesurvey unter der Adresse https://feedback.meine-schule.de/admin anmelden können.

Entweder als ``limeadmin`` oder wie du deinen Administrator in der .ini-Datei genannt hast, dafür muss die interne Datenbank ausgewählt werden.

![Login als limeadmin](/docs/login-internal.png)

Oder als Lehrer mit deinem Benutzernamen und Passwort der Schule über LDAP,

![Login als Lehrer](/docs/login-ldap.png)

### Anpassung der limesurvey.ini für v6.2

#### Abschnitt [setup]

* Die Variable "ldapusers" schränkt die Einloggmöglichkeit auf Lehrer ein (Standard) oder hebt die Einschränkung mit dem Wert "all" auf.
```
ldapusers=
```
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

## Einschränkungen

- Man sollte wissen, dass dieses Image von der Community gepflegt wird
  und daher genauso gut ist, wie der/die Ersteller es
  pflegen. Hinweise/issues etc. herzlich willkommen. Am besten über
  https://ask.linuxmuster.net/t/linuxmuster-survey/3505

- Alles was in der Datenbank gespeichert wird, wird bei einem Update
  des Images von limesurvey übernommen/migriert. Alles was auf der
  Festplatte landet, wird von einem neuen Image überschrieben bzw. man
  muss sich selbst um ein Backup/Recovery kümmmern. Dazu gehören
  eigene Themes inkl. Logos etc.
