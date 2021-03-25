# Userverwaltung

| Bezeichnung       | commands           | Beschreibung                      |
| :--------------------:   |:-------------         | :-----                            |
| Gruppen          | unter /etc/group           | Hier befinden sich alle Gruppen.                          |
| User            | Vunter /etc/passwd             | Hier befinden sich alle erstellten Benutzern.                            |
| Neue Benutzer erstellen           | sudo adduser BENUTZERNAME --ingroup GRUPPENNAME         |                              |
| VNeue Gruppe erstellen        | sudo addgroup GRUPPENNAME        |                              |
| Benutzer löschen           | sudo deluser (--remove-home) BENUTZERNAME            |                        |
| Passwort ändern           | sudo passwd BENUTZERNAME           |                                |
| Eigentümer von Dateien oder Ordnern ändern             | chown          |                                |
| Gruppenzugehörigkeit von Dateien und Ordnern ändern          | chgrp          |                                |
| Zugriffsrechte von Dateien ändern          | chmod          |                                |

Mehr infos kann man unter https://wiki.ubuntuusers.de/Shell/Befehls%C3%BCbersicht/#Benutzerverwaltung finden.