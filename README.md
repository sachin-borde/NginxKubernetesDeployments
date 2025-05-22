#############################################################################################

✅ Kubernetes Multi-Node Cluster Setup Guide (WSL Ubuntu)
*Simulated production environment using Kind - Lightweight & WSL2 Compatible*

#############################################################################################

🔧 1. Prerequisites Setup

1.1 Docker Installation

sudo apt update
sudo apt install -y docker.io
sudo usermod -aG docker $USER
newgrp docker
sudo service docker start
systemctl status docker

1.2 Kubernetes Tools

# Install kubectl
sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install Kind
sudo curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
sudo chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

#############################################################################################

🔧 2. Cluster Creation

2.1 Cluster Configuration

Create cluster-config.yaml

2.2 Start Cluster

kind create cluster --name nginx-cluster --config cluster-config.yaml

kubectl get nodes

#############################################################################################

🌐 3. NGINX Deployment

3.1 Deployment File

Create nginx-deployment.yaml

3.2 Service Exposure

Create nginx-service.yaml

3.3 Apply Configuration

kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml

kubectl get deployments
kubectl get svc
kubectl get pods

kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml

3.4 Testing time (Delete pods and see eitherit will reappear or not)

kubectl delete pod <Pod name>

kubectl delete pods  <Pod name>  <Pod name>  <Pod name>

3.5 Access Application

kubectl port-forward service/nginx-service 8080:80

Visit http://localhost:8080

#############################################################################################

🚀 4. Kubernetes Dashboard

4.1 Dashboard Installation

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

4.2 Admin Account Setup

Create dashboard-admin.yaml

4.3 Access Dashboard

kubectl apply -f dashboard-admin.yaml
kubectl -n kubernetes-dashboard create token admin-user
kubectl proxy

Access via: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

#############################################################################################

🧼 5. Cleanup Procedures

5.1 Cluster Removal

kind delete cluster --name nginx-cluster

5.2 Full Uninstallation

# Remove Kubernetes
sudo rm -rf /usr/local/bin/kubectl /usr/local/bin/kind ~/.kube

# Remove Docker
sudo apt purge -y docker.io
sudo rm -rf /var/lib/docker /etc/docker
sudo groupdel docker

#############################################################################################

🧱 6. Architecture Components

Component				 Role
kube-apiserver			 - Cluster API endpoint
etcd					 - Distributed key-value store
kube-scheduler			 - Pod scheduling decisions
kube-controller-manager	 - Node/pod lifecycle management
kubelet					 - Node agent for pod management
kube-proxy				 - Network traffic routing
CoreDNS	Cluster 		 - DNS service
Container Runtime		 - Docker/containerd for container execution

#############################################################################################

Best Practices Implemented:

Dedicated NodePort range for services

Non-root user context for containers

Resource limits for pods

Namespace isolation for dashboard

RBAC for secure dashboard access

Multi-node cluster simulation

#############################################################################################

Troubleshooting Tips:

# Check cluster events
kubectl get events --sort-by=.metadata.creationTimestamp

# Inspect pod logs
kubectl logs <pod-name> -n <namespace>

# Verify container status
kubectl describe pod <pod-name>

#############################################################################################
