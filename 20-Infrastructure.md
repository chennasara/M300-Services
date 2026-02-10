# M300 – Dynamische Infrastruktur mit Vagrant

## 1. Ziel des Moduls
Ziel war es, eine dynamische Infrastruktur-Plattform aufzubauen, auf der virtuelle Maschinen automatisiert erstellt werden können.  
Dies wurde mit Vagrant und VirtualBox umgesetzt.

## 2. Theorie

### Cloud Computing
Cloud Computing bedeutet, dass IT-Ressourcen über ein Netzwerk bereitgestellt werden.  
Man unterscheidet:

- **IaaS:** Infrastruktur (VMs, Netzwerk)  
- **PaaS:** Plattform für Entwickler  
- **SaaS:** fertige Software  

### Infrastructure as Code
Infrastructure as Code bedeutet, dass Infrastruktur nicht manuell, sondern über Code definiert wird.

Vorteile:

- Wiederholbar  
- Schnell  
- Weniger Fehler  
- Versionierbar  

## 3. Verwendete Tools
- VirtualBox: Virtualisierung  
- Vagrant: VM Automatisierung  
- Bash/Shell: Provisioning  
- optional Packer  

## 4. Umsetzung

### 4.1 Projektordner erstellt
Ordner erstellt und Vagrant initialisiert:


### 4.2 Vagrantfile angepasst
Konfiguration:

- Ubuntu Box  
- RAM zugewiesen  
- Portweiterleitung  
- Provisioning Script  

Beispiel:

- Installation Apache  
- Hostname setzen  

### 4.3 VM gestartet

Ergebnis:

- VM wurde automatisch erstellt  
- Software installiert  

### 4.4 Verbindung getestet

Ergebnis: gleiche Umgebung automatisch erstellt.

## 5. Ergebnis
Eine automatisierte VM-Umgebung wurde erfolgreich erstellt.  
Die Infrastruktur kann jederzeit neu aufgebaut werden.

## 6. Reflexion
Mit Infrastructure as Code kann Infrastruktur schnell und fehlerfrei erstellt werden.  
Änderungen sind einfach möglich und Systeme sind reproduzierbar.  
Dies ist besonders wichtig in modernen IT-Umgebungen und Cloud-Systemen.
