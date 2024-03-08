# ActionrunnerK3d
https://medium.com/simform-engineering/how-to-setup-self-hosted-github-action-runner-on-kubernetes-c8825ccbb63c
https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/quickstart-for-actions-runner-controller

#Preriqisite
Kubecluster (in example is in K3D)
create Access Token with Repo , admin:org


```
kubectl create secret generic pre-defined-secret \
   --namespace=arc-runners \
   --from-literal=github_token='YOUR-Accesstoken'
```
your copy of the values.yaml file, pass the secret name as a reference.
```
githubConfigSecret: pre-defined-secret
```

## Install CRD Controller
```
NAMESPACE="arc-systems"
helm install arc \
    --namespace "${NAMESPACE}" \
    --create-namespace \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller
    -f secretvalues.yaml
```
