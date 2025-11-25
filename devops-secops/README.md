# **DevOps felvételi feladat**

* ⏰ Határidő: A feladat kiosztását követő 11-ik munkanap 23:59:59 (CEST)  
* A projektet egy publikus github repository-ba várjuk tőled.

A feladatot tetszőleges virtualizációs megoldással tudod elkészíteni:

* Linux KVM \- preferált  
* VirtualBox  
* Vmware Workstation  
* Proxmox  
* stb.

A feladat megoldások dokumentálása és feltöltése:

* Githubon egy `gist` létrehozása és oda külön-külön a megírt scriptek, config snippetek, illetve a kiadott parancsok feltöltése (kézi telepítésnél)  
* Githubon egy repó készítése és oda a szükséges file-ok feltöltése (vagrantfile, ansible playbook, helm chart, stb.)

## Junior-level feladat elvárások

A feladatok megvalósításához elvárt technológiák:

- Docker Engine  
- Docker Compose

## Mid-level feladat elvárások

A feladatok megvalósításához elvárt technológiák:

- Ansible  
- Kubernetes  
- Helm

## Senior-level feladat elvárások

A feladatok megvalósításához elvárt technológiák:

- Ansible  
- IaC  
- GitOps

# Megvalósítandó feladatok:

## Linux rendszer telepítése

A feladat egy debian OS telepítése, majd egy user felvétele, alap programok telepítése és a folyamat dokumentálása.

### Feladatok:

* Debian 11 telepítése  
  * root jelszó: Alma1234  
  * saját user létrehozása  
  * a `/opt` és a `/tmp` külön partícióra kerüljön  
* Linux beállítása  
  * `sudo` telepítése  
  * Midnight Commander telepítése  
  * `htop` telepítése  
  * OpenJDK 8 és OpenJDK 11 telepítése  
  * Állítsd a `javac` verzióját OpenJDK 8-ra.  
  * `udemx` user létrehozása  
    * a `udemx` user home mappája legyen a `/opt/udemx` mappában.  
  * `ssh` elérés beállítása  
    * az SSH portja ne a default port legyen  
    * PEM alapú belépés jelszó helyett  
  * `fail2ban` telepítése  
    * SSH-ra való konfigurálása  
    * Nginx-re való konfigurálása  
  * `Iptables / ufw` beállítása  
    * csak az alkalmazásokhoz szükséges portok legyenek nyitva  
  * Git telepítése  
    * udemx@udemx.eu legyen a default user  
    * ssh-kulcs alapú git elérés beállítása	

## Kiegészítő szolgáltatások telepítése

### Junior: Docker-compose 

* Docker Engine telepítése  
* Compose létrehozása a szolgáltatásokkal  
  * a service-k automatikusan induljanak el reboot során

### Medior/senior: Kubernetes (helm chart)

* Kubernetes cluster (minikube/k3s) telepítése  
  * EXTRA: A cluster legyen HA, 3 node-os  
* App-ok telepítése helm chart-tal külön namespace-be

A feladat, hogy telepítsük az alábbi szolgáltatásokat szinttől függően:

### Feladatok:

* NGINX webszerver telepítése (kubernetes esetén Ingress Controller)  
  * 80-as porton  
    * HTTPS beállítása, self signed cert-el  
  * `Hello UDEMX!` legyen a főoldalon kiírva.  
* DB engine telepítése  
  * default porton  
  * `udemx` user és hozzá `udemx-db` létrehozása arra a `udemx` user jogosultságának beállítása.  
* Demo applikáció (Laravel, Flask, egyéb.)  
  * Nginx-en keresztül elérhető  
  * DB-re legyen kötve 

## Scripting & tooling feladatok

Írj egy-egy scriptet / job-ot, amivel az alábbi feladatokat meg tudod oldani

* `db-backup` → db dump-ot készít az aktuális dátummal elnevezett mappába, cron-olva hajnal 2-kor automatikus mentés készítése  
* `last-three-mod` → a 3 legutoljára módosított fájl listázása `/var/log` mappából a `last-three-mod-files-<DATE>.out` fájlba  
* `last-five-day-mod-files` → 5 napon belül módosított fájlok listázása a `/var/log` mappából rekurzívan a `last-five-day-mod-files-<DATE>.out` állományba  
* `loadavg` → 15 percenként load érték kiíratása a `/var/log/loadavg-<DATE>.out` fájlba  
* \+1: vim-ből kilépés parancsa :) :) :)

## CI-CD feladat

* Jenkins szerver telepítése és beállítása (szinttől függően)  
* Privát Docker registry létrehozása webes UI felülettel  
* A kapott privát Github repository használható a projekthez / vagy egy másikat is létre lehet hozni tetszés szerint

### Githubon egy privát repó és egy új projekt létrehozása

* A Repository-ba egy projekt létrehozása \- Hello World http server / tetszőleges más webes alkalmazás  
* A projekt tartalmazzon egy dockerfile-t, amivel elkészíthető a szükséges docker image

### Jenkins job létrehozása

* A feladathoz tetszőlegesen készíthető jenkins job:  
  Követelmények:  
  * scm: itt lehet beállítani a privát github repó-t  
  * build: a Dockerfile alapján az alkalmazásból készüljön egy docker image, amit a létrehozott registry-be pusholunk fel  
  * deploy: kiteszi az alkalmazást a megfelelő környezetbe (docker / kubernetes) a registry-be felpusholt image-ből  
* Ötletek  
  A feladat végrehajtható akár egy job-ban, amiben a post build sikeres build esetén deploy-olja az alkalmazást, vagy akár két job-ban is. Bármilyen technológia használható, a jenkins nagy szabadságot ad (ansible, shell script, tetszőleges plugin, stb.)  
* a feladat megoldása pipeline vagy jenkinsfile segítségével, nem sima freestyle job-ként

## Automatizáció feladat

A fentebb leírtak elkészítése tetszőlegesen kiválasztott `configuration management` / IaC tool segítségével.

1. A Server telepítés automatizálása pl. `Vagrant, Terraform` segítségével  
2. A különböző alkalmazások telepítése, konfigurálás, illetve a scriptek létrehozása `Ansible` segítségével  
3. A CI/CD feladat megoldása `Ansible` segítésével EXTRA: Jenkins automatizált configurálása `Ansible` segítségével a manuálisan elkészített config alapján

A cél, hogy a repository klónozása után egy `vagrant up` / `terraform apply` , vagy egyéb parancs kiadását követően a fenti feladatok automatikusan valósuljanak meg az alábbi formában:

* OS, alkalmazások és a szükséges dependenciák telepítése  
* szükséges konfigurációk beállítása (sshd, fail2ban, mysql, nginx, stb)  
* scriptek futtatása és a .out file-ok létrehozása a /opt/scripts mappába  
* CI/CD környezet telepítése (+ EXTRA: automatizált config)

Feladat beadása: Az elkészült `vagrantfile`, `ansible` playbook és egyéb configok feltöltése a git repository-ba.  
