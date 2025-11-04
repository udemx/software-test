# **Udemx Kft. – DevOps Engineer Felvételi Feladat**  
**Pozíció:** *DevOps Engineer – Mid-level* **vagy** *Senior*  
**Határidő:** A kiosztást követő **11. munkanap 23:59:59 (CEST)**  
**Beküldés:** Nyilvános vagy privát GitHub repo link (hozzáférés biztosítása)

---

## **Általános követelmények mindkét szinthez**

- **Virtualizáció & Orchestráció**:  
  - **Mid-level**: **Proxmox VE** (LXC + VM) **vagy** **Kubernetes** (opcionális, de előny).  
  - **Senior**: **Kötelező Kubernetes** (legalább 3 node-os klaszter, Proxmox-on vagy cloud-native).  
- **Modern stack**: GitOps, IaC, Observability, Security-by-default.  
- **Dokumentáció**:  
  - GitHub **Gist** → scriptek, config snippetek, parancsok.  
  - GitHub **Repo** → Terraform, Helm, ArgoCD, Flux, CI/CD pipeline, README (lépésről lépésre).  
- **Reprodukálhatóság**: A teljes környezet **egy `terraform apply` / `git clone + make up` / `argocd app sync` után** működjön.

---

# **MID-LEVEL FELADATOK**  
*(Proxmox VE, Kubernetes ajánlott)*

---

### **1. Infrastruktúra**

- Hozz létre **Proxmox VE** környezetet (legalább 1 node).  
- Telepíts **Debian 11** VM-et vagy LXC konténert **Terraform + Proxmox provider** segítségével.  
- **EXTRA**: Használj **Cloud-Init** konfigurációt (user, SSH kulcs, hálózat).  
- Készíts `udemx` felhasználót `sudo` jogosultsággal, tiltsd a root SSH belépést.
- **EXTRA**: a `udemx` user home mappája legyen a `/opt/udemx` mappában.
- **EXTRA**: `/opt` és `/tmp` külön **ZFS dataset** vagy **LVM partíció**.  
- Telepítsd az alábbiakat: `htop`, `mc`, `tmux`, `curl`, `jq`, `git`, `unzip`, `OpenJDK 8`, `OpenJDK 11`.
- **EXTRA**: Állítsd a javac verzióját OpenJDK 8-ra.
- **EXTRA**: Használj **Podman** Docker helyett.  

---

### **2. Alkalmazás stack** (konténerben előny)

- **Podman/Docker**: `hello-world` futtatása.  
  - **EXTRA1**: A lenti szolgáltatások futtatása konténerben.  
  - **EXTRA2**: Szolgáltatás problémák esetén automatikus restart.  
- **NGINX** (80-as port): főoldalon „Hello UDEMX!”  
  - **EXTRA**: **Let’s Encrypt** certifikát (Cert-Manager vagy `certbot` + cron).  
- **PostgreSQL**
  - **EXTRA**: `udemx` user + `udemx_db` létrehozása
- **MinIO**:
  - Adatbázis metnések tárolására
- **Git**: `udemx@udemx.eu`, SSH deploy key GitHub-hoz.  

---

### **3. Biztonság & Megfigyelhetőség & Scripting**

- **Iptables** / **UFW** / **NFTables**: csak szükséges portok (22/80/443) engedélyezése → teszt.  
- **Fail2ban** → SSH + NGINX szolgáltatásokra.  
- **Observability**:  
  - Telepíts **Prometheus Node Exporter** + **Grafana** (Loki opcionális).  
  - Dashboard: CPU, memória, disk I/O, NGINX status.  
- **Scripting (Bash vagy Python)**:  
  - `db-backup.sh` → db dump-ot készít az aktuális dátummal elnevezett mappába, cron-olva hajnal 2-kor automatikus mentés készítése, az mentett állomány legyen tömörítve és enkriptálva. Az elkészült állomány kerüljön mentésre a korábban elkészült MinIO szolgáltatásba.
  - `db-restore.sh` → paraméterben kapott (fájl név) törömrített és enkriptált dump állományt töltse vissza az adatbázisba
  - `last-three-mod.sh` → a 3 legutoljára módosított fájl listázása a `/var/log` mappából a `last-three-mod-files-<DATE>.out` fájlba
  - `last-five-day-mod-files.sh` → 5 napon belül módosított fájlok listázása a `/var/log` mappából a `last-five-day-mod-files-<DATE>.out` állományba
  - `loadavg.sh` → 15 percenként load érték kiíratása a `/var/log/loadavg-<DATE>.out` fájlba  
  - `nginx-mod.sh` → Az NGINX default konfigurációs fájljában az alábbi sztringben <title>Welcome to nginx!</title> cseréljük le a<title>-t Title: -ra és a </title>-t ne bántsuk. 

---

### **4. CI/CD – Jenkins**

- **Jenkins szerver** telepítése, beállítása.
- **Pivát Docker registry** létrehozása webes ui felülettel.
- **Privát Docker registry telepítése dockerben**
  - Két docker konténert kell indítani: Docker Registry, Docker Registry UI
- **Githubon egy privát repó és egy új projekt létrehozása**
  - A Repository-ba egy projekt létrehozása - Hello World http server / tetszőleges más webes alkalmazás
  - A projekt tartalmazzon egy dockerfile-t, amivel elkészíthető a szükséges docker image
- **Jenkins job létrehozása**
  - A feladathoz tetszőlegesen készíthető jenkins job:
    - Követelmények:
      - scm: itt lehet beállítani a privát github repó-t
      - build: a Dockerfile alapján az alkalmazásból készüljön egy docker image, amit a létrehozott registry-be pusholunk fel
      - deploy: kiteszi az alkalmazást a szerverre a registry-be felpusholt image-ből
    - Ötletek:
      - A feladat végrehajtható akár egy job-ban, amiben a post build sikeres build esetén deploy-olja az alkalmazást, vagy akár két job-ban is Bármilyen techonlógia használható, a jenkins nagy szabadságot ad (ansible, shell script stb.)
    - **EXTRA**: a feladat megoldása pipeline vagy jenkinsfile segítségével, nem sima freestyle job-ként

---

### **5. Automatizáció – IaC + Config Management** (opcionális)

- **Terraform** → Proxmox VM/LXC + hálózat + Cloud-Init.  
- **Ansible** → konfigurációk (NGINX, PostgreSQL, script-ek, systemd unitok).  
- **Podman Quadlets** / **Docker Compose** → konténerek deklaratív futtatása.  
- **Cél**: `git clone && make up` → teljes stack indítása.  
- **EXTRA**: **Vagrant + libvirt/Proxmox provider** helyi futtatáshoz.

---

# **SENIOR FELADATOK**  (mid-level feladatok megvalósítása az alábbi tech-stack-el)
*(Kötelező Kubernetes, GitOps, Cloud-Native, Zero-Trust)*

---

## **Környezet előkészítése**

- **Proxmox VE** + **Terraform** → 3 node-os **k3s** / **kubeadm** klaszter (1 CP, 2 worker).  
- **CNI**: **Cilium** (eBPF, NetworkPolicy, Hubble).  
- **Storage**: **Longhorn** (RWX/RWO).  
- **GitOps**: **ArgoCD** vagy **Flux v2** (kötelező).  
- **Observability**: **OpenTelemetry** → **Jaeger** + **Prometheus** + **Grafana**.  
- **Security**: **Kyverno** / **Gatekeeper**, **Trivy**, **Falco**.

---

### **1. Kubernetes**

- **Terraform** → Proxmox VM-ek + k3s bootstrap.  
- **Node-ok**: Debian 12, **containerd**, **nerdctl**.  
- **EXTRA**: `/opt` és `/tmp` → **Longhorn PV-k**.  
- **Namespace**: `udemx`.  
- **RBAC**: `udemx-sa` csak szükséges jogosultságokkal.  
- **SSH helyett**: `kubectl debug` vagy `telepresence`.

---

### **2. Alkalmazás stack – Cloud-Native**

- **NGINX Ingress Controller** + **ExternalDNS** (opcionális).  
- **HTTPS**: **cert-manager** + **Let’s Encrypt** (staging → prod).  
- **Percona PostgreSQL cluster**: **Percona PostgreSQL** operator.  
  - `udemx_db`, secret-ek **Sealed Secrets** vagy **External Secrets Operator (AWS/GCP/Vault)**.  
- **App**: Helm chart (pl. Bitnami Node.js vagy saját).  
- **Kaniko** vagy **Buildpacks** → image build CI-ben.  
- **Git**: **SOPS** + **age** titkosítás.

---

### **3. Nehezebb feladatok – Cloud-Native módon**

- **NetworkPolicy**: csak szükséges kommunikáció (app ↔ DB, ingress).  
- **CronJob-ok** (Kubernetes):  
  - `pg_dump` → MinIO (S3).  
  - Log analízis → ConfigMap + InitContainer.  
  - Loadavg → Prometheus metric.  
- **Observability**:  
  - **eBPF** (Cilium Hubble) → hálózati flow.  
  - **Loki + Promtail** → logok.  
- **Laravel demo**: saját Helm chart + ArgoCD Application, Kustomize overlay `dev`/`prod`.

---

### **4. CI/CD – GitOps + Argo Workflows / Tekton**

- **CI**: **Jenkins** / **GitHub Actions**  
  - Kaniko build → GHCR/Harbor.  
  - Trivy scan → fail fast.  
- **CD**: **ArgoCD**  
  - `Application` → Helm/Kustomize.  
  - Auto-sync + Prune.  
- **Image Updater**: **ArgoCD Image Updater** vagy **Keel**.  
- **EXTRA**: **Crossplane** vagy **Terraform Cloud** → infra sync.
- **EXTRA**: **Dependabot** vagy **Renovate** dependency update.
- **EXTRA**: **Trivy** vagy **Grype** konténer image scan CI-ben.
- **EXTRA+**: **Semantic Release** verziókezelés.
---

### **5. Automatizáció – Full IaC + GitOps**

- **Terraform** → Proxmox + k3s + Longhorn.  
- **Helmfile** vagy **ArgoCD ApplicationSet**.  
- **Bootstrap**: `make bootstrap` → `terraform apply && argocd app sync all`.  
- **Local dev**: **kind** vagy **minikube** + **Tilt**.  
- **Policy as Code**: Kyverno policy-k tiltják a `latest` tag-et, root konténert.

---

## **Beküldendő (mindkét szint)**

```bash
github.com/<tulaj>/<neved>-udemx-feladat
├── mid-level/                  # vagy senior/
│   ├── terraform/              # Proxmox + VM/LXC
│   ├── ansible/                # vagy nincs (opcionális)
│   ├── podman/                 # quadlets
│   ├── github-actions/
│   ├── scripts/
│   └── README.md
├── senior/
│   ├── terraform/              # Proxmox + k8s
│   ├── helm/
│   ├── argocd/
│   ├── kyverno/
│   ├── tekton/                 # vagy github-actions
│   └── Makefile
└── gist/
    ├── db-backup.sh
    ├── analyze-logs.py
    └── ...
```

---

## **Technológiai stack ajánlás**

| Réteg              | Mid-level                  | Senior                          |
|--------------------|----------------------------|---------------------------------|
| OS                 | Debian 12                  | Debian 11                       |
| Konténer           | Docker                     | containerd + nerdctl            |
| CI/CD              | Jenkins                    | Tekton / Argo Workflows         |
| CD                 | SSH deploy                 | ArgoCD / Flux                   |
| IaC                | Terraform                  | Terraform + Crossplane          |
| Config             | Ansible                    | Helm + Kustomize                |
| GitOps             | –                          | ArgoCD                          |
| Security           | Fail2ban, UFW              | Kyverno, Falco, Trivy           |
| Observability      | Prometheus + Grafana       | OTEL + Jaeger + Loki            |

---

**Sok sikert!**  
*Udemx Kft. – Platform Engineering Team*
