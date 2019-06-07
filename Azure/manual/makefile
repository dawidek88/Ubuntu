HOST_MASTER=master
HOST_WORKER_1=node01
HOST_WORKER_2=node02

ADMIN_USER=david
RESOURCE_GROUP=david
OS_IMAGE=UbuntuLTS

SIZE=Standard_B2s_2


create master:
	az vm create   --resource-group $(RESOURCE_GROUP)  --name $(HOST_MASTER)   --image $(OS_IMAGE)  --admin-username $(ADMIN_USER)--size $(OS_IMAGE)

create worker:
	az vm create   --resource-group $(RESOURCE_GROUP)  --name $(HOST_WORKER_1)   --image $(OS_IMAGE)  --admin-username $(ADMIN_USER) --size $(OS_IMAGE)
	az vm create   --resource-group $(RESOURCE_GROUP)  --name $(HOST_WORKER_2)   --image $(OS_IMAGE)  --admin-username $(ADMIN_USER) --size $(OS_IMAGE)

group_delete:
	az vm group delete --name $(RESOURCE_GROUP)

vm_deallocate:
	az vm deollcate --name $(HOST_MASTER) --resource-group $(RESOURCE_GROUP)
	az vm deollcate --name $(HOST_WORKER_1) --resource-group $(RESOURCE_GROUP)
	az vm deollcate --name $(HOST_WORKER_2) --resource-group $(RESOURCE_GROUP)