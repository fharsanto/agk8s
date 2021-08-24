# Step by Step Deployment Tyk Self Manage to k8s

## Issue in redis
```Error: failed to download "bitnami/redis" (hint: running `helm repo update` may help)```
Add repo bitname and tyk in helm
`helm repo add bitnami https://charts.bitnami.com/bitnami`
`helm repo add tyk-helm https://helm.tyk.io/public/helm/charts`

## Create Template for Redis, Redis-Cluster and Mongodb
`helm template --output-dir infra bitnami/redis`
`helm template --output-dir infra bitnami/redis-cluster`
`helm template --output-dir infra bitnami/mongodb`
`helm template --output-dir infra tyk-helm/tyk-pro`

### Export to single file
```
helm template tyk-redis \
--values base/redis-values.yaml \
--namespace tyk bitnami/redis \
> redis.yaml
```

## Install Redis & Mongodb
`helm install tyk-redis bitnami/redis --values base/redis-values.yaml -n tyk`
`helm install tyk-redis bitnami/redis-cluster -n tyk`
`helm install tyk-mongo bitnami/mongodb --values base/mongodb-values.yaml -n tyk`

`export REDIS_PASSWORD=$(kubectl get secret --namespace tyk tyk-redis -o jsonpath="{.data.redis-password}" | base64 --decode)`

`export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace tyk tyk-mongo-mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 --decode)`