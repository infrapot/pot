## Pot

Combine all necessary cloud management components in one single package.

### Installation

The installation provides an easy way to configure the server as well as CLI with basic configuration to enable infrastructure management.

#### Server installation

Make sure you have [helmfile](https://helmfile.readthedocs.io/en/stable/) installed in your env.

Install required dependencies using init:

```bash
helmfile init
```

Sync your Kubernetes cluster state to the desired one by running:
```bash
helmfile apply
```
#### High Availability

Refer to [ArgoCD Helm chart documentation](https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd#high-availability) in HA config.

#### ArgoCD CLI installation

In order to use ArgoCD CLI, have it installed in your local env with command ([documentation](https://argo-cd.readthedocs.io/en/stable/cli_installation/)):
```bash
brew install argocd
```

#### Simple access to ArgoCD

Forward the service locally:
```bash
kubectl port-forward service/argo-cd-argocd-server -n argo 8080:443
```

To retrieve initial admin password, invoke command (ensure no additional characters are copied):
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Go to https://localhost:8080/ and login with username `admin` and the retrieved password.

#### Simple access to Argo Workflow

Forward the service locally:
```bash
kubectl -n argo port-forward service/argo-workflows-server 2746:2746
```

Go to http://localhost:2746/ and login with e.g. [access token](https://argo-workflows.readthedocs.io/en/release-3.5/access-token/#access-token).

### Installing caveats

Pot is based on CRDs, which [cannot be updated with Helm](https://helm.sh/docs/chart_best_practices/custom_resource_definitions/#some-caveats-and-explanations).

All updates should be performed manually for now.

### Installing providers

Visit `https://marketplace.upbound.io/providers` to get install provider you want to have. Make sure you don't install too much of them to avoid CRD overload.

#### Example - AWS

Add a below helm definition to `helmfile.yaml`:
```yaml
releases:
  - name: provider-aws-s3
    chart: ./helm/crossplane-provider
    installed: true
    values:
      - version: 1.6.0
```
