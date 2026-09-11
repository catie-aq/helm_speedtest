# Speedtest Helm Chart

Chart simple pour déployer [speedtest-exporter](https://github.com/MiguelNdeCarvalho/speedtest-exporter) en **DaemonSet** sur Kubernetes : un test de débit internet (download/upload/ping) est exécuté périodiquement sur chaque nœud du cluster, et les métriques sont exposées au format Prometheus.

## Installation

```bash
helm install speedtest .
```

## Configuration

Fichier `values.yaml` :

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.tag` | Version de l'image speedtest-exporter | `latest` |
| `env.SPEEDTEST_CACHE_FOR` | Intervalle entre deux tests réels (évite de saturer la bande passante à chaque scrape) | `30m` |
| `env.SPEEDTEST_SERVER` | ID du serveur Speedtest à cibler (optionnel, sinon auto) | `""` |
| `resources.limits.cpu` | CPU max | `300m` |
| `resources.limits.memory` | Memory max | `256Mi` |
| `tolerations` | Tolérations pour tourner aussi sur les nœuds control-plane | `[]` |

## Métriques exposées

- `speedtest_download_bits_per_second`
- `speedtest_upload_bits_per_second`
- `speedtest_ping_latency_milliseconds`
- `speedtest_jitter_latency_milliseconds`

Chaque pod tourne sur un nœud différent (DaemonSet), le label `node`/`instance` ajouté par Prometheus permet de distinguer les nœuds dans Grafana.

## Prometheus

Aucune configuration supplémentaire n'est nécessaire côté `helm_prometheus` : le job `kubernetes-pods` déjà en place scrape automatiquement tout pod portant les annotations `prometheus.io/scrape: "true"` (déjà positionnées dans ce chart).

## Grafana

Importer le dashboard communautaire [ID 13502](https://grafana.com/grafana/dashboards/13502-speedtest-exporter/) sur la datasource Prometheus existante.

## Documentation

- [speedtest-exporter (GitHub)](https://github.com/MiguelNdeCarvalho/speedtest-exporter)
- [Docker Image](https://hub.docker.com/r/miguelndecarvalho/speedtest-exporter)
