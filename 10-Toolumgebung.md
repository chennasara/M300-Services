# M300 – Einrichtung der Toolumgebung

## 1. Ziel der Arbeit
Ziel dieser Aufgabe war es, eine vollständige Toolumgebung für das Modul M300 einzurichten.  
Diese Umgebung wird benötigt, um mit virtuellen Maschinen, Git und Infrastruktur-Automatisierung arbeiten zu können.

Die installierten Programme ermöglichen:

- Versionsverwaltung mit GitHub  
- Virtualisierung mit VirtualBox  
- Automatisierte VM-Erstellung mit Vagrant  
- Bearbeitung der Dateien mit Visual Studio Code  

## 2. Voraussetzungen
Für die Installation wurde ein Computer mit genügend Leistung benötigt:

- mindestens 8 GB RAM  
- genügend Speicherplatz  
- stabile Internetverbindung  

Die meisten Arbeiten wurden im Terminal (Git Bash) ausgeführt.

## 3. GitHub einrichten

### 3.1 GitHub Account erstellen
Zuerst wurde ein GitHub-Account erstellt.  
GitHub dient als Cloud-Speicher für Dokumentationen und Projekte.

Schritte:

- Account auf github.com erstellt  
- E-Mail bestätigt  
- Anmeldung durchgeführt  

### 3.2 Repository erstellen
Ein neues Repository wurde erstellt, um Dateien zu speichern.

Einstellungen:

- Repository Name festgelegt  
- Public ausgewählt  
- README erstellt  

## 4. Git Client installieren

### 4.1 Installation
Git wurde installiert, damit lokal mit Repositories gearbeitet werden kann.

### 4.2 Konfiguration
Git wurde mit Benutzername und E-Mail konfiguriert:


### 4.3 Repository klonen und pushen
Repository wurde lokal geklont:


Änderungen hochladen:

## 5. SSH-Key erstellen
Ein SSH-Key wurde erstellt, damit eine sichere Verbindung zu GitHub möglich ist.

Befehl:


Der öffentliche Schlüssel wurde anschliessend im GitHub-Account hinterlegt.

## 6. VirtualBox installieren

### 6.1 Installation
VirtualBox wurde installiert, um virtuelle Maschinen auszuführen.

### 6.2 Ubuntu ISO herunterladen
Ein Ubuntu Desktop Image wurde heruntergeladen.

### 6.3 Virtuelle Maschine erstellen
In VirtualBox wurde eine VM erstellt mit:

- Linux Ubuntu  
- ca. 2 GB RAM  
- 25 GB Speicher  
- ISO eingebunden  

Ubuntu wurde anschliessend installiert.

### 6.4 VM einrichten
Nach der Installation:


