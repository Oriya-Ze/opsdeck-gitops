# opsdeck-gitops

ArgoCD applications for the OpsDeck EKS environment.

## Prerequisites

1. EKS cluster (opsdeck-terra `aws-dev`)
2. ArgoCD installed in the cluster
3. Tailscale ACL tags and OAuth client (admin console)
4. HTTPS enabled on the tailnet

## Bootstrap

```bash
kubectl create namespace tailscale

kubectl create secret generic operator-oauth -n tailscale \
  --from-literal=client_id=YOUR_CLIENT_ID \
  --from-literal=client_secret=YOUR_CLIENT_SECRET

kubectl create namespace opsdeck

kubectl create secret generic opsdeck-secrets -n opsdeck \
  --from-literal=database-url=YOUR_DATABASE_URL \
  --from-literal=encryption-key=YOUR_ENCRYPTION_KEY
```

Edit `helm-values/opsdeck.yaml` with your ECR URLs, IRSA role ARN, and S3 bucket name.

Apply the root application once:

```bash
kubectl apply -f apps/root.yaml
```

## Layout

```
apps/
  root.yaml                 bootstrap app-of-apps
  tailscale-operator.yaml
  opsdeck.yaml
helm-values/
  opsdeck.yaml              env-specific Helm overrides
```
