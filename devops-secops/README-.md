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
- **Reprodukbilitás**: A teljes környezet **egy `terraform apply` / `git clone + make up` / `argocd app sync` után** működjön.

---

# **MID-LEVEL FELADATOK**  
*(Proxmox VE fókusz, Kubernetes opcionális – modern, de gyakorlati stack)*

---

### **1. Infrastruktúra alapozás**

- Hozz létre **Proxmox VE** környezetet (legalább 1 node).  
- Telepíts **Debian 12 (Bookworm)** VM-et vagy LXC konténert **Terraform + Proxmox provider** segítségével.  
- **EXTRA**: Használj **Cloud-Init** konfigurációt (user, SSH kulcs, hálózat).  
- Készíts **saját felhasználót** `sudo` jogosultsággal, tiltsd a root SSH belépést.  
- **EXTRA**: `/opt` és `/tmp` külön **ZFS dataset** vagy **LVM partíció**.  
- Telepítsd: `htop`, `tmux`, `curl`, `jq`, `git`, `unzip`.  
- **EXTRA**: Használj **Podman** Docker helyett (rootless).  

---

### **2. Alkalmazás stack (opcionálisan konténerben)**

- **NGINX** (80-as port): főoldalon „Hello UDEMX!”  
  - **EXTRA**: **Let’s Encrypt** certifikát (Cert-Manager vagy `certbot` + cron).  
- **PostgreSQL** (MariaDB helyett – iparági standard)  
  - **EXTRA**: `udemx` user + `udemx_db`, backup script `pg_dump` → S3-kompatibilis tár (pl. MinIO).  
- **Podman/Docker**: `hello-world` futtatása.  
  - **EXTRA1**: NGINX + PostgreSQL **Podman quadlet** vagy **systemd-nspawn** konténerben.  
  - **EXTRA2**: Automatikus restart systemd vagy Podman auto-update (`podman auto-update`).  
- **Git**: `udemx@udemx.eu`, SSH deploy key GitHub-hoz.  

---

### **3. Biztonság & Megfigyelhetőség**

- **UFW** vagy **NFTables**: csak szükséges portok (22/80/443).  
- **Fail2ban** → SSH + NGINX.  
- **Observability**:  
  - Telepíts **Prometheus Node Exporter** + **Grafana** (Loki opcionális).  
  - Dashboard: CPU, memória, disk I/O, NGINX status.  
- **Scripting (Bash vagy Python)**:  
  - `backup-db.sh` → `pg_dump` + dátum, tömörítés, MinIO feltöltés, cron 02:00.  
  - `log-analyze.sh` → utolsó 3 módosított log `/var/log`, `last_five_days.log`, `loadavg.log`.  
  - NGINX config módosítás `sed` vagy `yq` (ha YAML).  
- **EXTRA**: **Trivy** vagy **Grype** konténer image scan CI-ben.

---

### **4. CI/CD – GitHub Actions vagy GitLab CI**

- **Privát GitHub repo** + **Dockerfile** (pl. Node.js "Hello World" vagy Python Flask).  
- **GitHub Actions workflow**:  
  - Build → Docker image (Kaniko vagy Buildah).  
  - Push → **GitHub Container Registry (GHCR)** vagy **Harbor** (önállóan futtatva).  
  - Deploy → Proxmox VM-re (SSH + `podman run`).  
- **EXTRA**: **Dependabot** vagy **Renovate** dependency update.  
- **EXTRA+**: **Semantic Release** verziókezelés.

---

### **5. Automatizáció – IaC + Config Management**

- **Terraform** → Proxmox VM/LXC + hálózat + Cloud-Init.  
- **Ansible** → konfigurációk (NGINX, PostgreSQL, script-ek, systemd unitok).  
- **Podman Quadlets** vagy **Docker Compose** → konténerek deklaratív futtatása.  
- **Cél**: `git clone && make up` → teljes stack él.  
- **EXTRA**: **Vagrant + libvirt/Proxmox provider** helyi fejlesztéshez.

---

# **SENIOR FELADATOK**  
*(Kötelező Kubernetes, GitOps, Cloud-Native, Zero-Trust)*

---

## **Környezet előkészítése**

- **Proxmox VE** + **Terraform** → 3 node-os **k3s** vagy **kubeadm** klaszter (1 CP, 2 worker).  
- **CNI**: **Cilium** (eBPF, NetworkPolicy, Hubble).  
- **Storage**: **Longhorn** (RWX/RWO).  
- **GitOps**: **ArgoCD** vagy **Flux v2** (kötelező).  
- **Observability**: **OpenTelemetry** → **Jaeger** + **Prometheus** + **Grafana**.  
- **Security**: **Kyverno** vagy **Gatekeeper**, **Trivy**, **Falco**.

---

### **1. Kubernetes alapozás**

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
- **PostgreSQL**: **CloudNativePG** operator vagy **Zalando** operator.  
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

- **CI**: **GitHub Actions** vagy **Tekton**  
  - Kaniko build → GHCR/Harbor.  
  - Trivy scan → fail fast.  
- **CD**: **ArgoCD**  
  - `Application` → Helm/Kustomize.  
  - Auto-sync + Prune.  
- **Image Updater**: **ArgoCD Image Updater** vagy **Keel**.  
- **EXTRA**: **Crossplane** vagy **Terraform Cloud** → infra sync.

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
github.com/tulaj/neved-udemx-feladat
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
    ├── backup.sh
    ├── analyze-logs.py
    └── ...
```

---

## **Technológiai stack ajánlás**

| Réteg              | Mid-level                  | Senior                          |
|--------------------|----------------------------|---------------------------------|
| OS                 | Debian 12                  | Debian 11                       |
| Konténer           | Podman                     | containerd + nerdctl            |
| CI/CD              | GitHub Actions             | Tekton / Argo Workflows         |
| CD                 | SSH deploy                 | ArgoCD / Flux                   |
| IaC                | Terraform                  | Terraform + Crossplane          |
| Config             | Ansible                    | Helm + Kustomize                |
| GitOps             | –                          | ArgoCD                          |
| Security           | Fail2ban, UFW              | Kyverno, Falco, Trivy           |
| Observability      | Prometheus + Grafana       | OTEL + Jaeger + Loki            |

---

**Sok sikert!**  
*Udemx Kft. – Platform Engineering Team*
