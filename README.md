# Developing on Kubernetes

## Where to run your clusters ?

### Minikube

minikube start --driver docker

minikube status

docker ps

#### Point Docker to the daemon inside minikube

docker images

eval $(minikube docker-env)

docker images

docker build -t hello-app hello-app/

docker images

kubectl run hello-app --image hello-app --image-pull-policy=Never
### Kind

unset DOCKER_TLS_VERIFY
unset DOCKER_HOST
unset DOCKER_CERT_PATH
unset MINIKUBE_ACTIVE_DOCKERD

kind create cluster --name local-cluster

docker ps

kubectl get nodes

kubectl run nginx --image nginx

kubectl apply -f metalLB/LB.yaml

kubectl get pods -n metallb-system

### k3d

k3d cluster create --config k3d-config.yaml --registry-create mycluster-registry:0.0.0.0:5432

docker ps

#### Add a new node

k3d node create k3d-local-cluster-agent-2 --role agent --cluster local-cluster

### Skaffold

cd leeroy

skaffold dev --default-repo k3d-mycluster-registry.localhost:5432

k3d cluster delete local-cluster

### New kube Version

kubectl version

k3d cluster create --config k3d-config-1.27.yaml --registry-create 127-mycluster-registry:0.0.0.0:5433

kubectl version -->

### Deply to new version

kubectx

skaffold dev --default-repo k3d-127-mycluster-registry.localhost:5433

### Cleanup

docker network prune
cd ..

## Ingress

k3d cluster create --api-port 6550 -p "8081:80@loadbalancer" --agents 2

kubectl apply -f Ingress/apps.yaml
kubectl apply -f Ingress/ingress.yaml

kubectl get ing
curl --header "Host: devnexus.com" http://192.168.8.2/foo
curl --header "Host: devnexus.com" http://192.168.8.2/bar

<!-- ### Draft

cd ~/data/code/kubernetes-dev/hello-app
draft create
skaffold init -->

### Telepresence
gcloud container clusters get-credentials cosign-demo --zone europe-north1-a --project boredabdel-lab
kubectx cosign-cluster=gke_boredabdel-lab_europe-north1-a_cosign-demo
kubectx cosign-cluster
kubectl apply -f telepresence/deployment.yaml
telepresence connect
curl http://whereami.default:8080
curl http://details.default:9080

docker run  -p 0.0.0.0:80:80 nginx
telepresence intercept whereami --port 80:8080 --env-file whereami.env
telepresence leave whereami
