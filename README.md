# Action-Runner-101
-[Action-Runner for Community Edition](https://github.com/newkung6/actionrunner-selfhost?tab=readme-ov-file#actionrunner-for-community-edition)  
-[Action-Runner Official-Edition](https://github.com/newkung6/actionrunner-selfhost?tab=readme-ov-file#actionrunnerk3d-for-github-official)  
-[Github-Action](-)

# Action-Runner for Community Edition
FOR Simple Runner :https://actions-runner-controller.github.io/actions-runner-controller/  
Full Ref: https://github.com/actions/actions-runner-controller/tree/master
### Preriqisite
Kubecluster (in example is in K3D)  
Create [Access Token](https://github.com/settings/tokens/new)
with Check box (repo , admin:org) 

## Install Cert manager on Kube
Don't Forget to check latest version and install  
https://github.com/cert-manager/cert-manager/releases
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.4/cert-manager.yaml
```

## Install ARC Controller
** Replace PAT(Personal Access Token)
```
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
helm repo update

kubectl create ns actions-runner-system

helm upgrade --install --namespace actions-runner-system \
--set=authSecret.create=true \
--set=authSecret.github_token="<***YOUR-PersonalAccessToken***>" \
--wait actions-runner-controller actions-runner-controller/actions-runner-controller
```

## Apply runner-deployment to any namespace you want
kubectl create ns actionrunner  
kubectl apply -f github-runner-community/runner-deployment.yaml -n actionrunner 

runner-deployment.yaml
```
apiVersion: actions.summerwind.dev/v1alpha1
kind: RunnerDeployment
metadata:
  name: runner-deploy-jimmy #can change name
spec:
  replicas: 1
  template:
    spec:
      repository: newkung6/actionrunner-selfhost #Github repo url user/repo
      labels:
      - for-community-only #tag for run-on
```

after pod running. Check Repo runner  
![alt text](ImageforReadme/runner-community.png)

# Actionrunner For Github Official 
Ref 1: https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/quickstart-for-actions-runner-controller  
Ref 2: https://medium.com/simform-engineering/how-to-setup-self-hosted-github-action-runner-on-kubernetes-c8825ccbb63c

### Preriqisite
Kubecluster (in example is in K3D)  
Create [Access Token](https://github.com/settings/tokens/new)
with Check box (repo , admin:org) 

Create runner Namespace and create secret
```
kubectl create ns arc-runners
kubectl create secret generic pre-defined-secret \
   --namespace=arc-runners \
   --from-literal=github_token='<***YOUR-PersonalAccessToken***>'
```
your copy of the values.yaml file, pass the secret name as a reference.  
secretvalues.yaml
```
githubConfigSecret: pre-defined-secret
```

## Install CRD Controller
```
NAMESPACE="arc-systems"
helm install arc \
    --namespace "${NAMESPACE}" \
    --create-namespace \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller \
    -f github-runner-official/secretvalues.yaml
```

## Config Runner Scale set
INSTALLATION_NAME = "Name-You-Want-To-Design
```
INSTALLATION_NAME="arc-runner-set"
NAMESPACE="arc-runners"
GITHUB_CONFIG_URL="https://github.com/newkung6/actionrunner-selfhost" #<user>/<repo>
GITHUB_PAT="<PAT>"
helm install "${INSTALLATION_NAME}" \
    --namespace "${NAMESPACE}" \
    --create-namespace \
    --set githubConfigUrl="${GITHUB_CONFIG_URL}" \
    --set githubConfigSecret.github_token="${GITHUB_PAT}" \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
```
IF pod Created. You will see runner in repo
![alt text](ImageforReadme/runner-scale-set.png)