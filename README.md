# M1IL-Sopra-Ansible-2025 Luca Franchini

# 📊 Projet Ansible – Supervision avec Grafana, Prometheus, Loki et Promtail

## 🎯 Objectif

Déployer automatiquement sur une machine virtuelle :
- ✅ **Grafana** pour la visualisation
- ✅ **Prometheus** pour la collecte de métriques
- ✅ **Loki** pour la gestion des logs
- ✅ **Promtail** pour la collecte des fichiers `/var/log/*.log`
- ✅ Un dashboard automatique dans Grafana

---

## 🧩 Structure du projet

```
M1IL‑Sopra‑Ansible‑2025/
├── inventories
├── ansible.cfg
├── playbooks
└── roles/
    ├── grafana/
    │   └── tasks/main.yml
    ├── prometheus/
    │   └── tasks/main.yml
    ├── loki/
    │   └── tasks/main.yml
    └── promtail/
        └── tasks/main.yml
```

---

## ⚙️ Prérequis

- Une VM (Linux Debian/Ubuntu) accessible via SSH.
- Python & Ansible installés sur ta machine de déploiement.
- Adresse IP de la VM (`52.212.198.230`).
- L’utilisateur `luca` avec mot de passe `student`.

---

## 📄 Fichiers d’inventaire et de configuration

### `inventory.yml`

```yaml
local:
  hosts:
    vm:
  vars:
    ansible_connection: ssh
    ansible_user: luca
    external_ip: 52.212.198.230  # Remplacer l'ip si besoin
```

### `ansible.cfg`

```[defaults]
inventory = inventories/inventory.yml
roles_path = roles/
host_key_checking = False
timeout = 30
gathering = smart
fact_caching = memory
```

---

## 🌀 install_grafana.yml

```yaml
- name: Install Grafana on remote servers
  hosts: all
  become: true
  gather_facts: false
  roles:
    - grafana
    - prometheus
    - loki
    - promtail
```

---

## 💡 Rôles et leurs tâches essentielles

### 1. **prometheus**
- Téléchargement, extraction, configuration du service systemd
- Port : `9090`

### 2. **grafana**
- Installation via apt
- Provision automatique de :
  - Datasource Loki
  - Dashboard JSON logs `/var/log`

### 3. **loki**
- Téléchargement binaire, configuration YAML, service systemd
- Port : `3100`

### 4. **promtail**
- Lit les fichiers `/var/log/*.log`
- Envoie à Loki
- Service systemd

---

## 🚀 Lancer le déploiement

```bash
ansible-playbook install_grafana.yml
```

Mot de passe sudo : `student`

---

## ✅ Vérifications post-déploiement

- **Promtail** : `systemctl status promtail`
- **Loki** : `curl http://52.212.198.230:3100/ready`
- **Prometheus** : http://52.212.198.230:9090
- **Grafana** :
  - http://52.212.198.230:3000 (login `admin / student`)
  - Dashboards → Manage → System Logs (/var/log)

---

## 🔄 Débogage rapide

- **Pas de dashboard ?**  
  Vérifie les fichiers :
  ```bash
  ls /etc/grafana/provisioning/dashboards/
  ls /var/lib/grafana/dashboards/
  sudo systemctl restart grafana-server
  ```

- **Prometheus inaccessible ?**
  ```bash
  sudo ss -tulpn | grep 9090
  ```

---

## 🔐 Sécurisation / pistes d’amélioration

- HTTPS pour Grafana
- Authentification Promtail/Loki
- Alertes Prometheus
- Stockage persistant pour Loki

---

## 🔚 Conclusion

Déploiement complet avec Ansible pour une stack de supervision. Exécute un seul playbook, et tout est prêt !

