# opsdeck-gitops

ArgoCD applications for the OpsDeck EKS environment.

## Prerequisites

1. EKS cluster (opsdeck-terra `aws-dev`)
2. ArgoCD installed in the cluster
3. Tailscale ACL tags and OAuth client (admin console)
4. HTTPS enabled on the tailnet
5. `terraform apply` in opsdeck-terra

## Bootstrap

```bash
kubectl create namespace tailscale

kubectl create secret generic operator-oauth -n tailscale \
  --from-literal=client_id=YOUR_CLIENT_ID \
  --from-literal=client_secret=YOUR_CLIENT_SECRET
```

Set in `helm-values/external-secrets.yaml`:
- `serviceAccount.annotations.eks.amazonaws.com/role-arn` from `terraform output eso_irsa_role_arn`

Set in `helm-values/opsdeck.yaml`:
- `serviceAccount.roleArn` from `terraform output backend_irsa_role_arn`
- `backend.env.S3_BUCKET` from `terraform output s3_backup_bucket_name`

Apply the root application once:

```bash
kubectl apply -f apps/root.yaml
```

## Sync order

```
-2  external-secrets (operator)
-1  external-secrets-config (ClusterSecretStore + ExternalSecret)
 0  tailscale-operator
 1  opsdeck
```

## Layout

```
apps/
  root.yaml
  external-secrets.yaml
  external-secrets-config.yaml
  tailscale-operator.yaml
  opsdeck.yaml
manifests/
  eso/
    cluster-secret-store.yaml
    external-secret-opsdeck.yaml
helm-values/
  external-secrets.yaml
  opsdeck.yaml
```
