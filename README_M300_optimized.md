# M300 – Services (ELF)
## Dokumentation

## 1. Ziel der Arbeit

Ziel dieser Arbeit ist es, eine lokale Entwicklungs- und Testumgebung aufzubauen, in der virtuelle Maschinen und Container automatisiert, konsistent und reproduzierbar erstellt werden. Dabei werden theoretische Konzepte der Infrastruktur-Automatisierung praktisch umgesetzt.

---

## 2. Voraussetzungen

Hardware:
- Windows-PC mit aktivierter Virtualisierung (Intel VT-x / AMD-V)

Software:
- Git (Git Bash)
- VirtualBox
- Vagrant
- Visual Studio Code
- Docker Desktop
- Internetverbindung

---

## 3. Theoretische Grundlagen – Infrastruktur-Automatisierung

### 3.1 Cloud Computing

Cloud Computing bezeichnet die Bereitstellung von IT-Ressourcen über ein Netzwerk, ohne dass diese lokal installiert sein müssen.

Servicemodelle:
- IaaS (Infrastructure as a Service): Virtuelle Maschinen und Infrastruktur
- PaaS (Platform as a Service): Laufzeitumgebungen für Anwendungen
- SaaS (Software as a Service): Fertige Anwendungen
- CaaS (Container as a Service): Containerisierte Anwendungen (z. B. Docker, Kubernetes)

### 3.2 Dynamische Infrastruktur-Plattformen

Eine dynamische Infrastruktur-Plattform stellt virtualisierte Ressourcen wie Compute, Storage und Network bereit und verwaltet diese programmgesteuert.

Beispiele:
- Public Cloud: AWS, Azure, Google Cloud
- Private Cloud: OpenStack, VMware vCloud
- Lokale Virtualisierung: VirtualBox

Voraussetzungen für Automatisierung:
- programmierbar (API)
- on-demand
- self-service
- portabel
- sicher

### 3.3 Infrastructure as Code (IaC)

Infrastructure as Code ist ein Paradigma, bei dem Infrastruktur wie Software behandelt wird. Systeme werden in Dateien definiert, versioniert und automatisiert ausgerollt.

Ziele:
- konsistente und reproduzierbare Systeme
- schnelle Änderungen ohne manuellen Aufwand
- weniger Fehler durch Automatisierung
- einfache Wiederherstellung
- kontinuierliche Verbesserungen

### 3.4 Vagrant

Vagrant ist ein Open-Source-Tool zur Erstellung und Verwaltung von virtuellen Maschinen über die Kommandozeile. Die Konfiguration erfolgt über ein Vagrantfile (Box/OS, Provider, Netzwerk, Provisionierung).

### 3.5 Aufgabenbezug

Die theoretischen Inhalte dienen als Grundlagenmaterial. Die praktischen Aufgaben sind in den Hands-on-Abschnitten dokumentiert.

---

## 4. Praktischer Teil – Vagrant Umgebung

### 4.1 Git konfigurieren

```bash
git config --global user.name "sarachenna"
git config --global user.email "chennasara07@gmail.com"
```

### 4.2 SSH-Key erstellen (GitHub)

```bash
ssh-keygen -t rsa -b 4096 -C "chennasara07@gmail.com"
ssh -T git@github.com
```

### 4.3 Projektstruktur erstellen

```bash
cd "/c/Users/sara/OneDrive - Berufsbildungszentrum Schaffhausen/BBZ/Blockschule/300"
mkdir -p vagrant/web
cd vagrant/web
```

---

## 5. Apache Webserver automatisiert mit Vagrant

### 5.1 Vagrantfile erstellen

```bash
vagrant init ubuntu/trusty64
```

### 5.2 Vagrantfile konfigurieren

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.provision "shell", inline: <<-SHELL
    apt update
    apt install -y apache2
    systemctl enable apache2
    systemctl start apache2
  SHELL
end
```

### 5.3 VM starten und verbinden

```bash
vagrant up
vagrant ssh
```

---

## 6. Apache überprüfen

### 6.1 Service-Status

```bash
systemctl status apache2
```

Erwartung: `Active: active (running)`

### 6.2 Test mit curl

```bash
curl http://localhost
```

### 6.3 Test im Browser

`http://127.0.0.1:8080`

Bild einfügen:
- `images/apache.png`

### 6.4 HTML-Titel anpassen

```bash
sudo nano /var/www/html/index.html
```

Beispiel Anpassung:

```html
<title>M300 – Apache Webserver</title>
<h1>Apache Webserver – Modul 300</h1>
<p>Erstellt von sara</p>
```

---

# LB 3 – Docker Hands-on

## 1. Umgebung funktionsfähig auf eigenem Notebook

Docker wurde nativ auf dem Notebook installiert (Docker Desktop). Die Funktionsfähigkeit wurde geprüft mit:

```bash
docker run hello-world
```

Bild einfügen:
- `images/docker-hello-world.png`

---

## 2. Bestehende Docker Container kombinieren (Frontend & Backend)

Ziel: Kombination eines Backends (Datenbank) mit einem Frontend (Web-Applikation).

Verwendete Container:
- Backend: `mysql:5.7`
- Frontend: `ghost:1-alpine`
- Datenbankverwaltung: `adminer`

### 2.1 Docker Netzwerk erstellen

```bash
docker network create lb3net
```

### 2.2 MySQL Container starten (Backend)

```bash
docker run -d --name ghost_mysql --network lb3net \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_USER=ghost \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DATABASE=ghost \
  mysql:5.7
```

### 2.3 Ghost Container starten (Frontend)

```bash
docker run -d --name ghost --network lb3net \
  -e database__client=mysql \
  -e database__connection__host=ghost_mysql \
  -e database__connection__user=ghost \
  -e database__connection__password=secret \
  -e database__connection__database=ghost \
  -p 2368:2368 \
  ghost:1-alpine
```

### 2.4 Adminer starten (Datenbankverwaltung)

```bash
docker run -d --name adminer --network lb3net -p 8082:8080 adminer
```

### 2.5 Funktionstest

Ghost:
- `http://localhost:2368`
- Bild: `images/ghost.png`

Adminer:
- `http://localhost:8082`
- Bild: `images/adminer.png`

Status:
```bash
docker ps
```

### 2.6 Aufgetretenes Problem und Lösung (Ghost Image / Parameter)

Problem: Beim Start wurde ein falsches Image verwendet (z. B. `sara:1-alpine`).  
Lösung: Korrektes Image `ghost:1-alpine` verwenden und darauf achten, dass `--network` als eigener Parameter geschrieben ist.

---

## 3. Eigener Docker Container erstellen (Dockerfile)

Ziel: Umwandlung der Apache-Installation aus dem Vagrantfile in ein Dockerfile.

### 3.1 Dockerfile (Dateiname ohne Endung: `Dockerfile`)

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get -y install apache2 curl && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

ENV APACHE_RUN_USER=www-data
ENV APACHE_RUN_GROUP=www-data
ENV APACHE_LOG_DIR=/var/log/apache2

RUN mkdir -p /var/lock/apache2 /var/run/apache2

EXPOSE 80
VOLUME /var/www/html

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl -fs http://localhost/ || exit 1

CMD ["/bin/bash", "-c", "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"]
```

### 3.2 Docker Image erstellen

```bash
docker build -t puh .
```

### 3.3 Container starten

```bash
docker run -d --name puh_container -p 8080:80 puh
```

### 3.4 Funktionstest

```bash
curl http://localhost:8080
```

Browser:
- `http://localhost:8080`

Bild einfügen:
- `images/apache-docker.png`

---

## 4. Container sichern und beschränken (Ressourcenlimits)

Ziel: Verhindern, dass Container unbeschränkt CPU/RAM beanspruchen.

### 4.1 Container mit Limits starten

Falls der Container bereits läuft, zuerst entfernen:

```bash
docker rm -f puh_container
```

Dann mit Limits starten:

```bash
docker run -d --name puh_container -p 8080:80 \
  --memory=256m --cpus=0.5 --pids-limit=100 \
  puh
```

### 4.2 Kontrolle der Limits

```bash
docker stats --no-stream
```

Bild einfügen:
- `images/docker-stats.png`

---

## 5. Health Check

Im Dockerfile wurde ein `HEALTHCHECK` definiert, welcher regelmässig prüft, ob Apache erreichbar ist (curl auf `http://localhost`).

---

# 35 Container-Sicherheit

Dieses Kapitel beschreibt Protokollierung, Überwachung, Absicherung und CI in Docker-Umgebungen.

## 1. Protokollieren und Überwachen

### 1.1 Logging

Logs anzeigen:
```bash
docker logs <container>
```

Logs live:
```bash
docker logs -f <container>
```

Beispiel (Apache Container):
```bash
docker logs puh_container
```

Hinweis: Die Meldung `AH00558` ist eine häufige Apache-Warnung (ServerName) und kein kritischer Fehler.

Bild einfügen:
- `images/docker-logs.png`

### 1.2 Monitoring

Ressourcen überwachen:
```bash
docker stats
```

### 1.3 cAdvisor einsetzen

Auf Windows (Docker Desktop + Git Bash) traten Pfad-/Permission-Probleme bei den Linux-Volume-Mounts (`/var/run`, `/sys`) auf.  
Lösung: cAdvisor ohne diese Mounts starten (für die LB ausreichend):

```bash
docker run -d --name cadvisor -p 8081:8080 google/cadvisor:latest
```

Browser:
- `http://localhost:8081`

Bild einfügen:
- `images/cadvisor.png`

---

## 2. Container sichern und beschränken

### 2.1 Container nicht als root betreiben (Dockerfile)

```dockerfile
RUN groupadd -r appuser && useradd -r -g appuser appuser
USER appuser
```

Begründung: Least Privilege reduziert das Risiko bei kompromittierten Containern.

### 2.2 Speicher begrenzen

```bash
docker run -m 128m nginx
```

### 2.3 CPU begrenzen

```bash
docker run --cpus=0.5 nginx
```

### 2.4 Restart-Policy setzen

```bash
docker run --restart=on-failure:5 nginx
```

### 2.5 Read-Only Filesystem

```bash
docker run --read-only ubuntu
```

### 2.6 Capabilities einschränken

```bash
docker run --cap-drop all ubuntu
```

### 2.7 ulimit setzen

```bash
docker run --ulimit nproc=100 nginx
```

---

## 3. Kontinuierliche Integration (CI)

CI automatisiert Builds und Tests, um Fehler frühzeitig zu erkennen.

### 3.1 Jenkins starten

Hinweis: Der Port 8082 war bereits belegt (z. B. durch Adminer).  
Lösung: Jenkins auf einen freien Port legen (Beispiel 8083):

```bash
docker rm -f jenkins 2>/dev/null
docker run -d --name jenkins -u root \
  -p 8083:8080 \
  -v jenkins-data:/var/jenkins_home \
  jenkinsci/blueocean
```

Browser:
- `http://localhost:8083`

Initial Admin Password:
```bash
docker logs jenkins
```

Bild einfügen:
- `images/jenkins-setup.png`

### 3.2 Blue Ocean Plugin Problem (Version)

Problem: Blue Ocean Plugins zeigten Abhängigkeitsfehler (z. B. Jenkins-Version zu alt).  
Lösung: Jenkins LTS verwenden und Blue Ocean über Plugin-Manager installieren.

Start (LTS):
```bash
docker rm -f jenkins
docker run -d --name jenkins -u root \
  -p 8083:8080 \
  -v jenkins-data:/var/jenkins_home \
  jenkins/jenkins:lts
```

Danach Plugin Installation im UI:
- Manage Jenkins
- Manage Plugins
- Blue Ocean installieren

Direktlink (wenn installiert):
- `http://localhost:8083/blue`

---

## Fazit

Erreichte Ziele:
- Vagrant Umgebung aufgebaut und Apache automatisiert bereitgestellt
- Docker lokal installiert und getestet
- Frontend/Backend Container im Netzwerk kombiniert (MySQL, Ghost, Adminer)
- Eigenes Docker Image mit Apache gebaut und gestartet
- Ressourcenlimits und Healthcheck eingesetzt
- Logging und Monitoring angewendet
- CI-Grundlagen mit Jenkins im Container umgesetzt

---

## Fragen

### Protokollieren und Überwachen

Warum sollten Container überwacht werden?
- Um Fehler und hohe Ressourcenbelastungen frühzeitig zu erkennen und eingreifen zu können.

Was ist syslog und wo ist es zu finden?
- Systemweites Log auf Linux Hosts, typischerweise unter `/var/log`.

Was sind stdout, stderr, stdin?
- Standard Output, Standard Error, Standard Input.

### Container sichern und beschränken

Wie kann `docker run -v /:/homeroot -it ubuntu bash` durch normale User verhindert werden?
- Docker-Zugriff einschränken (keine Docker-Gruppe), Rechteverwaltung (nur Admin/Root darf Container starten).

Wie können verschiedene Mandanten getrennt werden?
- Isolation über separate VMs oder getrennte Hosts/Cluster, zusätzlich Namespaces/Policies.

Wie kann der Ressourcenverbrauch von Containern eingeschränkt werden?
- CPU/RAM Limits und PIDs Limits (Docker Resource Constraints).

### Kontinuierliche Integration

Welche Funktionen kann Jenkins übernehmen?
- CI, Tests ausführen, Builds, Jobs automatisieren, Pipelines.

Wie baut man Modultests?
- Zum Beispiel über Scripts (Bash, PowerShell) oder Build-Tools (je nach Projekt).

Wie können Jenkins Jobs ausser manuell/zeitgesteuert gestartet werden?
- Über Webhooks (z. B. bei Änderungen in einem Git Repository).

---

## 80 – Kubernetes: Einführung und Cluster

Dieses Kapitel gibt eine Einführung in Kubernetes und zeigt, wie Container-Anwendungen zentral verwaltet, skaliert und hochverfügbar betrieben werden.

Grundbegriffe:
- Service Discovery
- Load Balancing
- Cluster

Wichtige Objekte:
- Pod
- ReplicaSet
- Deployment
- Service
- Ingress
