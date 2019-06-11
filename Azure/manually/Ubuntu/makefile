HOST_MASTER=master
HOST_WORKER_1=node01
HOST_WORKER_2=node02

ADMIN_USER=david
RESOURCE_GROUP=david
OS_IMAGE=UbuntuLTS

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