# navidrome-helm

Helm chart for deploying [Navidrome](https://www.navidrome.org) on k3s (Raspberry Pi cluster).

## Structure

```
helm/navidrome/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml   # navidrome deployment + ClusterIP service
    ├── storage.yaml      # PVC for app data (nfs-hdd)
    └── ingress.yaml      # optional Traefik ingress
```

Music is served from a hostPath (`/HDD/Media/Music`) — no PVC needed for the library itself.

## Deploy

```bash
helm upgrade --install navidrome ./helm/navidrome \
  --namespace navidrome \
  --create-namespace
```

## Enable Ingress (Cloudflare via Traefik)

```bash
helm upgrade --install navidrome ./helm/navidrome \
  --namespace navidrome \
  --set ingress.enabled=true \
  --set ingress.host=navidrome.tara-cloud.org
```

Then add a public hostname in Cloudflare Zero Trust:
- **Subdomain:** `navidrome`
- **Domain:** `tara-cloud.org`
- **Type:** `HTTP`
- **URL:** `navidrome.navidrome.svc.cluster.local:4533`

## Key values

| Key | Default | Description |
|-----|---------|-------------|
| `storage.music.hostPath` | `/HDD/Media/Music` | Music library path on Pi |
| `storage.data.storageClass` | `nfs-hdd` | StorageClass for app data PVC |
| `storage.data.size` | `5Gi` | App data PVC size |
| `ingress.enabled` | `false` | Expose via Traefik ingress |
| `ingress.host` | `navidrome.tara-cloud.org` | Public hostname |
| `nodeSelector` | `tara/node-role: pi` | Pin pod to Pi nodes |
