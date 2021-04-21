# Container absichern
***
Zu den wichtigsten Dingen, um einen Container abzusichern, gehören:
* Die Container laufen in einer VM oder auf einem dedizierten Host, um zu vermeiden, dass andere Benutzer oder Services angegriffen werden können.
* Der Load Balancer / Reverse-Proxy ist der einzige Container, der einen Port nach aussen freigibt, wodurch viel Angriffsfläche verschwindet. Monitoring oder Logging-Services sollten über private Schnittstellen oder VPN nutzbar sein.
* Alle Images definieren einen Benutzer und laufen nicht als root.
* Alle Images werden über den eigenen Hash heruntergeladen oder auf anderem Wege sicher erhalten und verifiziert.
* Die Anwendung wird überwacht und es wird Alarm ausgelöst, wenn eine ungewöhnliche Netzwerklast oder auffällige Zugriffsmuster erkannt werden.
* Alle Container laufen mit aktueller Software und im Produktivmodus – Debug-Informationen sind abgeschaltet.
* AppArmor oder SELinux sind auf dem Host aktiviert
* Services wie z.B. Apache, Mysql ist mir irgendeiner Form der Zugriffskontrolle oder einem Passwortschutz ausgestattet.

**Weitere Massnahmen:** <br>
* Unnötige `setuid-Binaries` werden aus den `identidock-Images` entfernt. Damit verringert sich das Risiko, dass Angreifer, die Zugriff auf einen Container erhalten haben, ihre Berechtigungen erweitern können.
* Dateisysteme werden so weit wie möglich schreibgeschützt eingesetzt.
* Nicht benötigte Kernel-Berechtigungen werden so weit wie möglich entfernt.

**Beim Einsatz sicherheitskritischer Container:** <br>
* Der Speicher für jeden Container wird durch das Flag `-m` begrenzt. Damit werden ein paar DoS-Angriffe und Speicherlecks eingedämmt. Die Container müssen dabei entweder per Profiler analysiert werden oder man gibt sehr grosszügige Speichergrenzen vor.
* SELinux mit speziellen Typen für die Container ausführen. Das kann eine sehr effektive Sicherheitsmassnahme sein, aber sie erfordert einiges an Arbeit.
* Ein `ulimit` auf die Anzahl der Prozesse anwenden. Diese Grenze ist für den Benutzer des Containers gültig, daher kann es schwieriger einzusetzen sein, als man denkt. So vermeidet man die Gefahr von Fork-Bomben, die als DoSAngriff eingesetzt werden.
* Interne Kommunikation wird verschlüsselt, so dass es für Angreifer schwieriger wird, die Daten zu beeinflussen.

Zusätzlich sollte es regelmässige Audits für das System geben, um sicherzustellen, dass alles aktuell ist und sich keine Container Ressourcen unter den Nagel reissen.


### Container nach Host trennen
***
Hat man ein Multitenancy-Setup, bei dem Container für mehrere Benutzer laufen (sei es, dass es sich um interne Benutzer im Unternehmen oder um externe Kunden handelt), stellt man sicher, dass jeder Benutzer auf einem eigenen Docker Host untergebracht ist.

Das ist zwar weniger effizient, als Hosts mit mehreren Benutzern zu teilen, aber sehr wichtig für die Sicherheit. Der Hauptgrund ist, damit Container-Breakouts zu verhindern, bei denen ein Anwender Zugriff auf die Container oder Daten eines anderen Anwenders erhält.

Geschieht solch ein Breakout, befindet sich der Angreifer immer noch auf einer getrennten VM oder einem eigenen Rechner, sodass er nicht problemlos auf Container anderer Benutzer zugreifen kann.


### Weitere Sicherheitstipps
***
Diese Seite enthält praktische Tipps zum Absichern von Container-Deployments.

Nicht alle Ratschläge werden für alle Deployments umsetzbar sein, aber man sollte sich mit den grundlegenden Tools vertraut machen, die eingesetzt werdenkönnen.

**User setzen** <br>
Um zu vermeiden, dass `root` genutzt wird, sollten man in den Dockerfiles immer einen Benutzer mit weniger Rechten erstellen und mit der USER-Anweisung zu ihm wechseln.
```Shell
    $ RUN groupadd -r user_grp && useradd -r -g user_grp user
    $ USER user
```

**Netzwerkzugriff beschränken** <br>
Ein Container sollte in der Produktivumgebung nur die Ports öffnen, die er tatsächlich benötigt, und diese sollten auch nur für die anderen Container erreichbar sein, die sie brauchen.

**setuid/setgid-Binaries entfernen** <br>
Die Wahrscheinlichkeit, dass eine Anwendung keine setuid- oder setgid-Binaries benötigt, ist recht hoch. Können wir solche Binaries deaktivieren oder entfernen, verhindern wir, dass sie zur unerlaubten Rechteauswertung eingesetzt werden.
```Shell
    $ FROM ubuntu:14.04

       ... Installation der benötigten Software
       ... User anlegen

    $ RUN find / -perm +6000 -type f -exec chmod a-s {} \; || true
```

**Speicher begrenzen** <br>
Durch die Begrenzung des verfügbaren Speichers schützen man sich vor DoSAngriffen und Anwendungen mit Speicherlecks, die nach und nach den Speicher auf dem Host auffressen.
```Shell
    $ docker run -m 128m --memory-swap 128m amouat/stress stress --vm 1 --vm-bytes 127m -t 5s
```

**CPU-Einsatz beschränken** <br>
Kann ein Angreifer einen Container – oder eine ganze Gruppe – dazu bringen, die CPU des Host vollständig auszulasten, werden andere Container auf dem Host nicht mehr arbeiten können, und man hat es mit einem DoS-Angriff zu tun.

In Docker wird die CPU-Zuteilung über eine relative Gewichtung ermittelt, wobei ein Standardwert von 1024 genutzt wird – normalerweise erhalten also alle Container gleich viel CPU-Zeit.

Beispiel: Starten von 4 Container mit unterschiedlicher CPU-Zuweisung:
```Shell
    $ docker run -d --name load1 -c 2048 amouat/stress
    $ docker run -d --name load2 amouat/stress
    $ docker run -d --name load3 -c 512 amouat/stress
    $ docker run -d --name load4 -c 512 amouat/stress

    $ docker stats $(docker inspect -f {{.Name}} $(docker ps -q))
```

**Neustarts begrenzen** <br>
Stirbt ein Container immer wieder und wird dann neu gestartet, muss das System nicht unerheblich Zeit und Ressourcen aufwenden, was im Extremfall auch zu einem DoS führen kann.

Das lässt sich leicht mit der Neustart-Vorgabe `on-failure` statt `always` vermeiden:
```Shell
    $ docker run -d --restart=on-failure:10 my-flaky-image
```

**Zugriffe auf die Dateisysteme begrenzen** <br>
Wenn man verhindert, dass Angreifer in Dateien schreiben, stört man damit eine ganze Reihe von Angriffen und machen das Leben von Hackern ganz allgemein schwerer.

Kein Skript kann in eine Datei schreiben und die eigenen Anwendung dazu bringen, diese auszuführen, oder kritische Daten oder Konfigurationsdateien überschreiben.

Seit Docker 1.5 kann `docker run` das Flag `--read-only` übergeben, welches das Dateisystem des Containers komplett schreibgeschützt macht:
```Shell
    $ docker run --read-only ubuntu touch x
```

**Capabilities einschränken** <br>
Der Linux-Kernel definiert eine Reihe von Berechtigungen (Capabilities), welche Prozessen zugewiesen werden können, um ihnen einen erweiterten Zugriff auf das System zu gestatten.

Die Capabilities decken einen grossen Funktionsbereich ab, vom Ändern der Systemzeit bis hin zum Öffnen von Netzwerk-Sockets.

```Shell
    $ docker run --cap-drop all --cap-add CHOWN ubuntu chown 100 /tmp
```

**Ressourcenbeschränkungen (ulimits) anwenden** <br>
Der Linux-Kernel definiert Ressourcenbeschränkungen, die für Prozesse gesetzt werden können – z.B. die Anzahl der Kind-Prozesse, die sich forken lassen, oder die Anzahl der zulässigen offenen File-Deskriptoren.

Diese lassen sich auch auf Docker-Container anwenden – entweder durch Übergabe des Flags `--ulimit` an `docker run` oder durch das Setzen containerübergreifender Standards per `--default-ulimit` beim Start des Docker Daemon.

```Shell
    $ docker run --ulimit cpu=12:14 amouat/stress stress --cpu 1
```

## Container Sicherheit
### Sicherheiten
#### Nicht als root in den Container gehen
Dazu muss man folgende Zeilen im Dockerfile machen:
```
RUN useradd -ms /bin/bash NeuerUserName

USER NeuerUserName

WORKDIR /homeNeuerUserName
```
Der unterste Befehl ist nicht nötigt. Dieser sagt nur aus in welchem Verzeichnis man sein sollte, wenn man sich mit dem Container verbindet (im Home Verzeihcnis hat der User Admin Berechtigung)

#### Read-Only
Wenn man den Docker mit der Option read-only startet, können keine Änderungen am Dateisystem vorgenommen werden (auch mit sudo nicht):
`docker run --read-only -d -t --name NameDesContainer Image`

#### Dockerfile's
Ein wichtiger Punkt bei dem man sich und seine Netzwerkumgebung schützen kann ist, dass man seine Dockerfile's selber schreibt. Wenn man das macht und nicht irgendwelche Images aus dem Internet herunterladet kann man sich sicher sein, dass diese nicht mit Malware oder sonst was verseucht sind.

## Monitoring
#### Cadvisor
Cadvisor ist eine Überwachungs Tool von Google. Mit folgendem Befehl kann ein Container erstellt werden, welcher Cadvisor enthält:\
`docker run -d --name cadvisor -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro -p 8081:8080 google/cadvisor:latest`

## Docker Hub
### Image bereitstellen
Zuerst muss ein Container mit einem Image erstellt werden. Danach führt man folgenden Befehl aus:
`docker commit ContainerIDMitDemGewünschtenImage DockerhubUserName/GewünschterName:Tag`

Danach pusht man das ganze auf Docker Hub:
`docker push DockerHubUserName/GewünschterName:Tag`

Achtung: Das funktioniert nur, wenn man an der console mit einem Docker Account angemeldet ist (`docker login`).

### Image pullen
Das Image kann nun mit pull heruntergelade werden. Dazu muss man folgenden Befehl eingeben:
`docker pull DockerHubUserName/GewünschterName:Tag`