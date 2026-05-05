# Monitoring Stack

> Clé en main : Prometheus + Grafana + Node Exporter + cAdvisor déployés en une commande via Docker Compose.

Extrait de la stack de monitoring utilisée en production sur le projet [taiga-aws-infra](https://github.com/aberdjeghloul/taiga-aws-infra), adaptée pour un déploiement local ou sur n'importe quelle VM.

---

## Stack

| Service | Image | Port | Rôle |
|---|---|---|---|
| Prometheus | `prom/prometheus:v2.48.0` | 9090 | Collecte et stockage des métriques |
| Grafana | `grafana/grafana:10.2.0` | 3000 | Visualisation et dashboards |
| Node Exporter | `prom/node-exporter:v1.7.0` | 9100 | Métriques système (CPU, RAM, disque, réseau) |
| cAdvisor | `gcr.io/cadvisor/cadvisor:v0.47.2` | 8080 | Métriques des containers Docker |

---

## Démarrage rapide

```bash
git clone https://github.com/aberdjeghloul/monitoring-stack.git
cd monitoring-stack
docker compose up -d
```

Accès :
- **Grafana** : http://localhost:3000 (admin / admin)
- **Prometheus** : http://localhost:9090
- **Node Exporter** : http://localhost:9100/metrics
- **cAdvisor** : http://localhost:8080

---

## Structure

```
monitoring-stack/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml              # Config scraping (interval 15s, rétention 15j)
└── grafana/
    ├── dashboards/                 # Dashboards JSON (auto-provisionnés)
    └── provisioning/
        ├── datasources/
        │   └── datasource.yml      # Datasource Prometheus auto-configurée
        └── dashboards/
            └── dashboard.yml       # Provisionnement automatique des dashboards
```

---

## Ajouter des cibles à scraper

Édite `prometheus/prometheus.yml` et ajoute tes serveurs :

```yaml
scrape_configs:
  - job_name: my-servers
    static_configs:
      - targets: ['192.168.1.10:9100', '192.168.1.11:9100']
        labels:
          env: production
```

Recharge sans redémarrer :

```bash
curl -X POST http://localhost:9090/-/reload
```

---

## Ajouter un dashboard Grafana

Dépose un fichier `.json` dans `grafana/dashboards/` — il sera auto-provisionné au démarrage.

Des dashboards prêts à l'emploi sont disponibles sur [grafana.com/grafana/dashboards](https://grafana.com/grafana/dashboards) :
- **Node Exporter Full** : ID `1860`
- **Docker cAdvisor** : ID `14282`

---

## Contexte

Cette stack est dérivée du monitoring déployé sur le projet [taiga-aws-infra](https://github.com/aberdjeghloul/taiga-aws-infra) — infrastructure AWS Multi-AZ avec Docker Swarm, où Prometheus scrape 9 nœuds (3 masters + 6 workers) toutes les 15 secondes avec une rétention de 15 jours.

---

## Auteur

**Abdelrhamane Berdjeghloul** — DevOps & Cloud Engineer  
[GitHub](https://github.com/aberdjeghloul) · [LinkedIn](https://linkedin.com/in/abdelrhamane-berdjeghloul-66a84a53)
