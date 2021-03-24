# M300 - Plattformübergreifende  Dienste in ein Netzwerk integrieren

## 1. Einleitung

Das Hauptthema ist Cloud Computing. In diesem Modul werden wir unter anderem mit Github, Vagrant und VisualStudio Code vertraut gemacht. 

<img src="/Bilder/Bilder1.jpg" alt="Check"/>
<img src="/Bilder/Bilder2.jpg" alt="Check"/>




## 2. Voraussetzungen
### 2.1 Virtualbox installiert
### 2.2 Gitpush Account registriert
### 2.3 Editor Visualstudio Code installiert
Extensions installieren
Wir fügen dem Editor drei wichtige Extensions hinzu:

Markdown All in One (von Yu Zhang)
Vagrant Extension (von Marco Stanzi)
vscode-pdf Extension (von tomiko1207)
Dazu müssen folgende Anweisungen befolgt werden:

Visual Studio Code öffnen
Die Tastenkombination CTRL + SHIFT + X drücken und in der Suchleiste die erwähnten Extensions suchen.

10 Toolumgebung aufsetzen
GitHub Bash
Konfigurieren:

git config --global user.name "username" git confug --global user.email "E-Mail"

Befehle
Repository klonen
Cd in gewünschtes Repository, in das es kopiert werden sollte:

git clone "URL vom Repository"

Cd in das geklone Repository und Status abfragen:

git pull git status

Lokale Änderungen in Repository pushen
Cd in Repository git add - A "Filename" git commit -m Kommentar (falls gewünscht) git push

VSC
Extensions
Auf das Zeichen mit den 4 Vierecken auf der linken Seite oder ctrl + , --> Extension suchen und installieren

Lokale Änderungen in Repository pushen
Änderung mit ctrl + s speichern --> auf der linken Seite wird bei einem Icon eine 1 erscheinen --> Text Message eingeben --> auf Hacken drücken --> drei Punkte --> Push

Vagrant
Vorbereitung
VirtualBox, Cmder und Vagranat herungerladen

Vagrant File erstellen
Voranmerkung: https://app.vagrantup.com/boxes/search hier findet man die VM Vorlagen, welche man benutzen kann.

cd Ort, an dem die VM abgelegt werden sollte und einen neuen Ordner erstellen:

vagrant init ubuntu/xenial64 |War das Beispiel in der Aufgabe

vagrant installieren und starten
vagrant up --provider virtualbox

Info: Dannach ist die VM in VirtualBox eingerichtet. Mit VmWare hat es nicht geklappt, da es noch eine extra Lizenz für VMWare gebraucht hätte

SSH Zugriff
Cd in das Verzeichnis der VM:

vagrant ssh

20 Infrastruktur-Autorsierung
Markdown
Markdownlight Extension heruntegeladen und, wie man an diesem Beispiel hier sieht eingerichtet.

Vagrant | Boxen
Boxen sind VM Vorlagen. Man kann diese direkt im cmder mit folgendem Befehl herunterladen:

vagrant box add "box-name" oder vagrant box add "box-url"

Mit folgendem Befehl kann man Sie wieder entfernen

vagrant box remove "box-name" --> Der Box name wird folgendermassen aufgebaut: Entwickler/Box (z.B. ubuntu/xenial64)

Box Befehle
Box hinzufügen:

vagrant add "box-name"

Lokal vorhandene Boxen anzeigen:

vagrant box list

Vm erstellen

Neuer Ablage Ordner erstellen: mkdir xy in beliebten Verzeichnis

cd neuer Ordner

vagrant init "box-name"

vagrant up

Status anzeigen: vagrant status

Vm Updaten:

Nach einer Änderung im Vagrantfile kann ein Server wie folgt aktualisiert werden: vagrant provision

VM löschen:

Vagrant destroy -f

Packer
Packer: Tool, mit dem man Images bzw. Boxen für eine Vielzahl von Dynamic Infrastructure Platforms mit einer Konfdatei erstellen kann

Funktionsweise
Provisioning: Anweisung auf ein anderes Programm --> Eine Shell, wie z.B. Bash Builder: Erstellt Image, für eine bestimmte dynamische Infrastruktur Plattform --> z.B. VirtualBoc Post-processors: Bestandteile von Packer, welche das ERgebnis eines Bilders übernehmen

Installation
Herunterladen und entpacken. Normalerweise müsste man den "Variabel-Pfad" ergänzen, jedoch hat es im cmd automatisch gepackt und mit "packer --version" kommt die Versions Angabe