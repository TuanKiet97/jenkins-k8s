## Running Jenkins in Kubernetes

## Command to start minikube

minikube --driver docker \
--nodes 1 \
--cpus=no-limit \
--memory=no-limit \
--kubernetes-version=v1.30.0-rc.2 \
--container-runtime=docker \
--profile=manifest \
start

## List profile

minikube profile list

## Install kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl


kubectl config set-context minikube --namespace jenkins-ns

kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml

kubectl port-forward pod/jenkins-0 8080:8080

# Install helm

helm repo ls

helm repo add jenkins https://charts.jenkins.io 

helm repo update

helm search repo

helm install jenkins1 jenkins/jenkins

1. Get your 'admin' user password by running:
  kubectl exec --namespace default -it svc/jenkins1 -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password && echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  echo http://127.0.0.1:8080
  kubectl --namespace default port-forward svc/jenkins1 8080:8080

helm uninstall jenkins1

helm list -A

helm install jenkins1 jenkins/jenkins -n jenkins-ns --create-namespace

# Add new plugin by upgrading the new value into chart
helm upgrade -n jenkins-ns jenkins1 jenkins/jenkins --values values/additional-plugins.yaml

Change the configmap: kubectl get -n jenkins-ns cm/jenkins1 -o yaml

Change the password: kubectl get -n jenkins-ns secret/jenkins1 -o yaml


# Check password
kubectl get -n jenkins-ns secret/jenkins1 -o yaml | grep jenkins-admin-password | cut -d ':' -f2

echo $PW | base64 -d

# Restart sts for loading the configuration

kubectl rollout restart -n jenkins-ns sts/jenkins1