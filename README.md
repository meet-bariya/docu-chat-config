# docu-chat-config

GitOps config repo for **docu-chat**, a 3-tier chat app (React, FastAPI, Postgres + pgvector), deployed to Kubernetes via Argo CD.

App code: https://github.com/meet-bariya/docu-chat-app

## Structure

```
docu-chat-config/
├── base/
│   ├── kustomization.yaml    # entrypoint: namespace, resources, image tags
│   ├── postgres/             # StatefulSet, Service, SealedSecret
│   ├── backend/              # Deployment, Service, ConfigMap
│   ├── frontend/             # Deployment, Service
│   └── ingress.yaml
└── argocd/
    └── docu-chat.yaml        # Argo CD Application
```

## Images

- `meetbariya/docu-chat-backend`
- `meetbariya/docu-chat-frontend`

Tags are set in `base/kustomization.yaml`. Never use `latest`.

## Deploy

Only manual step, done once:

```bash
kubectl apply -f argocd/docu-chat.yaml
```

After that, all changes go through `git push`. Argo CD auto-syncs with prune and selfHeal.

## Prerequisites

- Kubernetes cluster with Argo CD installed
- Sealed Secrets controller (`kube-system`)
- `kubeseal` CLI

## Adding a resource

1. Write the manifest under `base/`.
2. Add it to `resources:` in `base/kustomization.yaml`.
3. Commit and push. Watch Argo CD sync.

## Secrets

Never commit plain secrets. Seal them first:

```bash
kubeseal --format yaml < secret.yaml > base/postgres/sealed-secret.yaml
```