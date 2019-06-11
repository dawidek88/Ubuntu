HOST_MASTER=master
HOST_WORKER_1=node01
HOST_WORKER_2=node02

ADMIN_USER=david
RESOURCE_GROUP=david
OS_IMAGE=CentOS

LOCATION=eastus
SIZE=Standard_B2s

install_VMs: group_create create_master create_worker

create_master:
	az vm create --resource-group $(RESOURCE_GROUP)  --name $(HOST_MASTER)   --image $(OS_IMAGE)  --admin-username $(ADMIN_USER) --size $(SIZE)

create_worker:
	az vm create --resource-group $(RESOURCE_GROUP)  --name $(HOST_WORKER_1)   --image $(OS_IMAGE)  --admin-username $(ADMIN_USER) --size $(SIZE)
	az vm create --resource-group $(RESOURCE_GROUP)  --name $(HOST_WORKER_2)   --image $(OS_IMAGE)  --admin-username $(ADMIN_USER) --size $(SIZE)

group_create:
	az group create --location $(LOCATION) --name $(RESOURCE_GROUP)

group_delete:
	az group delete --name $(RESOURCE_GROUP)

vm_deallocate:
	az vm deallocate --name $(HOST_MASTER) --resource-group $(RESOURCE_GROUP)
	az vm deallocate --name $(HOST_WORKER_1) --resource-group $(RESOURCE_GROUP)
	az vm deallocate --name $(HOST_WORKER_2) --resource-group $(RESOURCE_GROUP)

setup_VM_master: setup_selinux setup_firewall_master setup_kubernetes setup_init_kubernetes setup_network_kubernetes

setup_VM_worker: setup_selinux setup_firewall_worker setup_kubernetes setup_init_kubernetes setup_network_kubernetes

setup_selinux:
	sudo su -
	setenforce 0
	sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux

setup_firewall_master:
	firewall-cmd --permanent --add-port=6443/tcp
	firewall-cmd --permanent --add-port=2379-2380/tcp
	firewall-cmd --permanent --add-port=10250/tcp
	firewall-cmd --permanent --add-port=10251/tcp
	firewall-cmd --permanent --add-port=10252/tcp
	firewall-cmd --permanent --add-port=10255/tcp
	firewall-cmd --reload
	modprobe br_netfilter
	echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables

setup_firewall_worker:
	firewall-cmd --permanent --add-port=30000-32767/tcp
	firewall-cmd --permanent --add-port=10250/tcp
	firewall-cmd --reload
	modprobe br_netfilter
	echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables

setup_kubernetes:
	sudo yum upgrade
	
	cat <<EOF > /etc/yum.repos.d/kubernetes.repo
	[kubernetes]
	name=Kubernetes
	baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
	enabled=1
	gpgcheck=1
	repo_gpgcheck=1
	gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
	https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
	EOF
	
	yum install kubeadm docker -y
 	systemctl enable docker
	systemctl start docker
	systemctl status docker
	systemctl enable kubelet.service
	swapoff -a

setup_init_kubernetes:
	kubeadm init --pod-network-cidr=192.168.0.0/16
	mkdir -p $HOME/.kube
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	sudo chown $(id -u):$(id -g) $HOME/.kube/config

setup_network_kubernetes:
	kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
	kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml