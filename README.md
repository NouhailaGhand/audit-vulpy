# 🔐 Audit de Sécurité — vulpy DevSecOps

## 📋 Description
Audit de sécurité complet sur vulpy, application Flask/Python intentionnellement vulnérable.
Réalisé dans le cadre du module Cybersécurité et Audit de Sécurité — Bachelor Cybersécurité AU 2025/2026.
Projet réalisé en binôme — 4 semaines.

---

## 🎯 Objectifs
- Construire un pipeline DevSecOps automatisé sur GitHub Actions
- Intégrer 5 outils de sécurité (SAST, SCA, DAST, Secrets, Container)
- Réaliser un audit de conformité OWASP Top 10 2021
- Produire un rapport d'audit professionnel avec recommandations P1/P2/P3

---

## 🏗️ Architecture Pipeline
```
git push → GitHub Actions
    ↓
GitLeaks → Semgrep → Trivy → OWASP DepCheck → OWASP ZAP
    ↓
Security Gates (bloque si CRITICAL > 5)
    ↓
Rapports JSON sauvegardés comme artifacts
```

---

## 🛠️ Outils de Sécurité

| Outil | Catégorie | Findings |
|-------|-----------|----------|
| GitLeaks | Secrets Detection | 4 secrets hardcodés |
| Semgrep | SAST | 8 failles code source |
| OWASP ZAP | DAST | 15 vulnérabilités runtime |
| Trivy | Container Scan | 83 CVE image Docker |
| OWASP DepCheck | SCA | Dépendances vulnérables |

---

## 📊 Résultats de l'Audit

| Sévérité | Nombre |
|----------|--------|
| 🔴 CRITICAL | 2 |
| 🟠 HIGH | 7 |
| 🟡 MEDIUM | 20 |
| 🟢 LOW | 67 |
| **TOTAL** | **110** |

---

## 🚀 Installation & Utilisation

### Prérequis
```bash
sudo apt install python3 python3-pip docker.io
pip3 install flask pyotp cryptography bcrypt qrcode jsonschema
```

### Lancer vulpy
```bash
git clone https://github.com/fportantier/vulpy.git
cd vulpy/bad
python3 db_init.py
FLASK_APP=vulpy.py flask run --host=0.0.0.0 --port=5000
```
Application accessible sur : http://127.0.0.1:5000

### Lancer GitLeaks
```bash
docker run --rm -v $(pwd):/repo \
  zricethezav/gitleaks:latest detect \
  --source /repo --report-format json \
  --report-path /repo/reports/gitleaks.json
```

### Lancer Semgrep
```bash
semgrep --config=p/python --config=p/flask \
  --config=p/owasp-top-ten \
  --json --output reports/semgrep.json bad/
```

### Lancer OWASP ZAP
```bash
docker run --rm --network host \
  -v $(pwd)/reports:/zap/wrk \
  ghcr.io/zaproxy/zaproxy:stable \
  zap-baseline.py -t http://127.0.0.1:5000 \
  -J zap-report.json -r zap-report.html
```

### Lancer Trivy
```bash
docker build -t vulpy .
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $(pwd)/reports:/reports \
  aquasec/trivy:latest image \
  --format json --output /reports/trivy-image.json vulpy:latest
```

---

## 📁 Structure du Projet
```
audit-vulpy/
├── .github/
│   └── workflows/
│       └── audit.yml        ← Pipeline CI/CD GitHub Actions
├── reports/
│   ├── gitleaks.json        ← 4 secrets trouvés
│   ├── semgrep.json         ← 8 failles SAST
│   ├── zap-report.json      ← 15 vulnérabilités DAST
│   ├── trivy-image.json     ← 83 CVE conteneur
│   └── docker-bench.txt     ← Évaluation CIS Docker
├── Dockerfile               ← Image Docker vulpy
└── README.md                ← Ce fichier
```

---

## 📚 Standards de Référence

| Standard | Version | Utilisation |
|----------|---------|-------------|
| OWASP Top 10 | 2021 | Classification des vulnérabilités |
| CVSS | v3.1 | Scoring de sévérité |
| CIS Docker Benchmark | v1.6 | Sécurité des conteneurs |
| NIST SP 800-53 | Rev.5 | Recommandations |
| ISO/IEC 27001 | 2022 | Structure du rapport |

---

## ⚠️ Avertissement

> Ce projet est uniquement à des fins éducatives.
> vulpy est une application intentionnellement vulnérable.
> Ne jamais déployer en production.

---

## 👥 Équipe
Projet réalisé en binôme — Bachelor Cybersécurité AU 2025/2026
Module : Cybersécurité et Audit de Sécurité
