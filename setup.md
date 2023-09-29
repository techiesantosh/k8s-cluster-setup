REf https://www.fosstechnix.com/how-to-install-kubernetes-cluster-on-ubuntu/ 
    https://projectcalico.docs.tigera.io/getting-started/kubernetes/quickstart

swapoff -a

sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

sudo reboot

sudo apt-get update

sudo apt-get install -y \
apt-transport-https \
ca-certificates \
curl \
gnupg \
lsb-release

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker-archive-keyring.gpg


echo   "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update -y

sudo apt-get install docker-ce docker-ce-cli containerd.io -y

cat <<EOF | sudo tee /etc/docker/daemon.json
{
"exec-opts": ["native.cgroupdriver=systemd"],
"log-driver": "json-file",
"log-opts": {
"max-size": "100m"
},
"storage-driver": "overlay2"
}
EOF

Add the docker user in group and give permission for docker.sock

sudo usermod -aG docker $USER
Change the docker.sock permission

sudo chmod 666 /var/run/docker.sock
Start the Docker service if not started

sudo systemctl start docker.service
To check the docker service status

sudo systemctl status docker.service
Enable Docker service at startup

sudo systemctl enable docker.service
Restart the Docker service

sudo systemctl restart docker


#1. Add Kubernetes GPG Key on All node
Add Kubernetes GPG key in all node.
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

#2. Add Kubernetes APT Repository on All node
Add Kubernetes apt repository on all node for Ubuntu.
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
update the system packages
sudo apt-get update

#3. Install Kubeadm,Kubelet and Kubectl on All Node
Install kubeadm,kubelet and kubectl using below command.
sudo apt-get install -y kubelet kubeadm kubectl

#4.Hold the packages to being upgrade

sudo apt-mark hold kubelet kubeadm kubectl

sudo kubeadm init --pod-network-cidr=192.168.0.0/16
[init] Using Kubernetes version: v1.25.2
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR CRI]: container runtime is not running: output: E1013 08:40:51.800195    1329 remote_runtime.go:948] "Status from runtime service failed" err="rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"


sudo vi /etc/containerd/config.toml- remove cri from disabled-plugins
sudo systemctl restart containerd

sudo kubeadm init --pod-network-cidr=192.168.0.0/16

sudo mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
	
	sudo kubeadm join 10.182.0.4:6443 --token orco6g.vv8vxgv14b3pm0hr \
     --discovery-token-ca-cert-hash sha256:968eae79d88d58129ab76b3a687f5a551ce5240b9b50c23b9d697417135d8a4a
		
		
		

		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
