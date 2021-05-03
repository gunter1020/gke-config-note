# GKE K8S Config Note

## Create GCP service account

```bash
GCP_PROJECT=
GSA_ACCOUNT=k8s-gsa
K8S_NAMESPACE=prod-system
KSA_ACCOUNT=my-ksa

gcloud iam service-accounts create ${GSA_ACCOUNT}
gcloud iam service-accounts add-iam-policy-binding \
  --role roles/iam.workloadIdentityUser \
  --member "serviceAccount:${GCP_PROJECT}.svc.id.goog[${K8S_NAMESPACE}/${KSA_ACCOUNT}]" \
  ${GSA_ACCOUNT}@${GCP_PROJECT}.iam.gserviceaccount.com
```

## Create GCP public IP

```bash
GCP_PROJECT=
GCP_REGION=asia-east1

gcloud compute addresses create k8s-ingress \
  --project=${GCP_PROJECT} \
  --region=${GCP_REGION}
```

## Apply k8s config

[kustomization](https://kubernetes.io/zh/docs/tasks/manage-kubernetes-objects/kustomization/)

```bash
# Display config
kustomize build kustomize/example > run.yaml

# Apply config
kubectl apply -k kustomize/example
```

## Rollout deployment

```bash
K8S_NAMESPACE=prod-system
K8S_DEPLOYMENT=backend-deploy
K8S_CONTAINER=backend-app
NEW_IMAGE_NAME=
NEW_IMAGE_TAG=

# Change image
kubectl -n ${K8S_NAMESPACE} set image deployment ${K8S_DEPLOYMENT} ${K8S_CONTAINER}=${NEW_IMAGE_NAME}:${NEW_IMAGE_TAG}

# Restart deployment
kubectl -n ${K8S_NAMESPACE} rollout restart deployment ${K8S_DEPLOYMENT}
kubectl -n ${K8S_NAMESPACE} rollout status deployment ${K8S_DEPLOYMENT}
```

## Attach exec container

```bash
K8S_NAMESPACE=prod-system
K8S_POD=backend-deploy-<uid>
K8S_CONTAINER=backend-app

kubectl -n ${K8S_NAMESPACE} get pod
kubectl -n ${K8S_NAMESPACE} exec ${K8S_POD} -c ${K8S_CONTAINER} -it -- /bin/sh
```

## (Option) Dashboard UI

[dashboard docs](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

```bash
# Deploy dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml

# Create account
kubectl create serviceaccount dashboard-admin-user

# Bind role
kubectl create clusterrolebinding dashboard-admin-user \
  --clusterrole=cluster-admin \
  --serviceaccount=default:dashboard-admin-user

# Get token
kubectl get secret $(kubectl get sa/dashboard-admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"

# Start service
kubectl proxy
```

## (Option) Install ingress-nginx by helm

[helm](https://helm.sh/docs/intro/install/)
[ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/)

```bash
K8S_NAMESPACE=prod-system

# Install ingress-nginx
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm -n ${K8S_NAMESPACE} install ingress-nginx ingress-nginx/ingress-nginx
```
