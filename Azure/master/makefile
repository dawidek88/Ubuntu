#SHELL := '/bin/bash'
HOST_MASTER=master
HOST_WORKER_1=node01
HOST_WORKER_2=node02
#CIDR_NETWORK=172.168.10.0/24
CIDR_NETWORK=10.244.0.0/16
API_SERVER=$$(hostname -i)
#DOCKER_CE=18.06.2~ce~3-0~ubuntu
#KUBERNETES_VERSION="1.14.0"
FSTAB=/etc/fstab
SWAP_IMG=swap.img


check:
	@echo ""
	@echo What do you want to install?
	@echo ""
	@echo \ [1] $(HOST_MASTER) 
	@echo \ [2] $(HOST_WORKER_1) 
	@echo \ [3] $(HOST_WORKER_2)
	@echo \ [N] Abort
	@echo \ ""
	@echo \Please insert number or letter:
	read lin;
	test=$(lin)
	@echo "line $(test)"

ifeq ("$(test)", "1")
	@echo "$$lin is equal"
else
	@echo "$$lin is not equal" 
endif


kubernetes_master: set_hostname install_docker pre_config swap install_kubernetes init_kubernetes network_kubernetes
#kubernetes_master: set_hostname install_docker pre_config install_kubernetes init_kubernetes

set_hostname:
	@echo "--- SET Hostname - $(HOST_MASTER) for Kubernetes-Master ---"
	@echo ""
	sudo hostnamectl set-hostname $(HOST_MASTER)
	@echo "-----------------------------------------------------------"

install_docker:
	@echo "--- Docker installation ---"
	@echo ""
	sudo apt-get remove docker docker-engine docker.io containerd runc -y
#	sudo apt-get update && apt-get install apt-transport-https ca-certificates curl software-properties-common -y
#	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
#	sudo add-apt-repository "deb [arch=amd64] \
#	https://download.docker.com/linux/ubuntu \
#	$$(lsb_release -cs) \
#	stable"
#	sudo apt-get update && apt-get install docker-ce=$(DOCKER_CE)
	sudo apt-get install docker.io -y
	sudo usermod -aG docker $$(whoami)
	sudo systemctl unmask docker.service
	sudo systemctl start docker
	sudo systemctl enable docker.service
	docker --version
	@echo "-----------------------------------------------------------"

install_kubernetes:
	@echo "--- Kubernetes requirements - installation ---"
	@echo ""
	sudo swapon -s
	sudo swapoff -a
	sudo apt-get install apt-transport-https curl -y
	curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
	sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
	sudo apt-get update && apt-get install kubelet kubeadm kubectl kubernetes-cni -y
	@echo "-----------------------------------------------------------"

init_kubernetes:
	@echo "--- Initialization Kubernetes - $(HOST_MASTER) ---"
	@echo ""
	kubeadm version
#	sudo kubeadm init --pod-network-cidr=$(CIDR_NETWORK) --apiserver-advertise-address=$(API_SERVER) --kubernetes-version $(KUBERNETES_VERSION)
	sudo kubeadm init --pod-network-cidr=$(CIDR_NETWORK) --apiserver-advertise-address=$(API_SERVER)
	mkdir -p $$HOME/.kube
	sudo cp /etc/kubernetes/admin.conf $$HOME/.kube/config
	sudo chown $$(id -u):$$(id -g) $$HOME/.kube/config
	@echo "-----------------------------------------------------------"
	
network_kubernetes:
	echo "--- Apply Network for Kubernetes"
	echo ""
	kubectl get nodes
	kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
	echo "-----------------------------------------------------------"

kubernetes_worker:
	echo "Kubernetes installation - $(HOST_MASTER)"
	sudo hostnamectl set-hostname $(HOST_WORKER_1)
	sudo apt-get install docker.io -y
	sudo systemctl start docker
	docker --version

clean:
	sudo kubeadm reset -f
#	sudo rm -Rf /var/lib/kubelet /etc/kubernetes /var/lib/etcd
#	sudo docker rm -f echo "$(docker ps -a -q -y)"

.ONESHELL:
pre_config:
	echo "--- Pre-Config Docker ---"
	echo ""
	sudo cat > /etc/docker/daemon.json <<EOF
	{
	"exec-opts": ["native.cgroupdriver=systemd"],
	"log-driver": "json-file",
	"log-opts": {
	"max-size": "100m"
	},
	"storage-driver": "overlay2"
	}
	EOF

	sudo mkdir -p /etc/systemd/system/docker.service.d

	# Restart docker.
	sudo systemctl daemon-reload
	sudo systemctl restart docker
	echo "-----------------------------------------------------------"

swap:
	echo "--- Disable SWAP  ---"
	echo ""
	if ! grep "#/$(SWAP_IMG)" $(FSTAB); then
	sudo sed -i 's/\/$(SWAP_IMG)/\#\/$(SWAP_IMG)/g' /etc/fstab
	echo "SWAP is now disabled"
	else
	echo "SWAP is still disabled"
	fi	
	echo "-----------------------------------------------------------"

.ONESHELL:
Dockerfile: 
	cat <<- EOF > $@
	FROM scratch
	ADD bin/$(EXECUTABLE) /bin/$(EXECUTABLE)
	CMD ["/bin/$(EXECUTABLE)"]
	EOF

#.ONESHELL:
test:
#	echo TEST
#	mkdir -p $$HOME/.kube
#	sudo cp -i /etc/kubernetes/admin.conf $$HOME/.kube/config
#	sudo chown $$(id -u):$$(id -g) $$HOME/.kube/config
#	sed -i 's/swap/#swap/g' 
#	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $$(lsb_release -cs) stable"
#	sudo add-apt-repository \
#	"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
#	$$(lsb_release -cs) \
#	stable"
#	sudo usermod -aG docker $$(whoami)
#	sudo systemctl daemon-reload
#	systemctl restart docker.service
#	docker info | grep Cgroup

#	sudo systemctl daemon-reload
#	systemctl restart kubelet.service
	@echo $(API_SERVER)
