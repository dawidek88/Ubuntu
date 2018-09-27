   $(eval TERRAFORM_VERSION := 0.11.8)
   $(eval LINUX_DISTRIBUTION := $(shell lsb_release -is))
   $(eval LINUX_DISTRIBUTION_CODENAME := $(shell lsb_release -cs))
   $(eval LINUX_DISTRIBUTION_CODENAME_OLD := xenial)
   $(eval LINUX_DISTRIBUTION_VERSION := $(shell lsb_release -rs))

   $(eval PRE_INSTALL_PACKAGES :=)
   $(eval INSTALL_PACKAGES :=)

   $(eval MSSQL_DOWNLOADPATH := http://packages.microsoft.com/config/$(shell echo $(LINUX_DISTRIBUTION) | tr A-Z a-z)/$(LINUX_DISTRIBUTION_VERSION)/prod.list)
   $(eval MSSQL_DOWNLOADPATH_HTTP_CODE := $(shell curl -s -o /dev/null -w "%{http_code}" $(MSSQL_DOWNLOADPATH)))
   $(eval SQLCMD_VERSION :=)

   $(eval JP_VERSION := 0.1.3)


install-all: print mssql azurecli kubectl jp
	$(info ---------------------------------)
	$(info Installation started)
	$(info Requirements for Installation $(PRE_INSTALL_PACKAGES))
	$(info Installation Packages $(INSTALL_PACKAGES))

ifndef PRE_INSTALL_PACKAGES
	$(info ---PRE_INSTALL---)
ifndef INSTALL_PACKAGES
	$(info ---INSTALL---)
	# update package manager db
	sudo apt-get update

	# install pre_install_packages
	sudo ACCEPT_EULA=Y apt-get install -y $(PRE_INSTALL_PACKAGES)

	# install managed packages
	sudo ACCEPT_EULA=Y apt-get install -y $(INSTALL_PACKAGES)
else
	$(info No packages to install - just downloading terraform)
endif
else
	$(info No packages to install - just downloading terraform)
endif

	$(info Download Terraform Version $(TERRAFORM_VERSION))
	# get terraform
	wget -c -N -P /tmp/ https://releases.hashicorp.com/terraform/$(TERRAFORM_VERSION)/terraform_$(TERRAFORM_VERSION)_linux_amd64.zip
	sudo unzip -u -d /usr/local/sbin/ /tmp/terraform_$(TERRAFORM_VERSION)_linux_amd64.zip

	$(info Installation complete...)
	$(info ---------------------------------)


print:
	$(info ---------------------------------)
	$(info Terraform Version: $(TERRAFORM_VERSION))
	$(info Linux Distrubution: $(LINUX_DISTRIBUTION))
	$(info Linux Distrubution Codename: $(LINUX_DISTRIBUTION_CODENAME))
	$(info Linux Distrubution Version: $(LINUX_DISTRIBUTION_VERSION))
	$(info MSSQL Downloadpath: $(MSSQL_DOWNLOADPATH))
	$(info MSSQL Downloadpath HTTP Code: $(MSSQL_DOWNLOADPATH_HTTP_CODE))
	$(info ---------------------------------)

mssql:
	# check if mssql tools are already present
	$(eval SQLCMD_VERSION := $(shell sqlcmd --version))
	
ifeq (,$(findstring "Command not found",$(SQLCMD_VERSION)))
	# mssql tools not present - check if they are available for this distribution

ifeq ($(MSSQL_DOWNLOADPATH_HTTP_CODE),"404")
	# mssql tools not available for this linux distribution

	$(info Tried to open URL, but received error: $(MSSQL_DOWNLOADPATH))
	$(error MSSQL tools are not available for your linux distribution - $(LINUX_DISTRIBUTION) $(LINUX_DISTRIBUTION_VERSION) $(LINUX_DISTRIBUTION_CODENAME) - error code was $(MSSQL_DOWNLOADPATH_HTTP_CODE))
else
	# mssql tools not present - adding them to installation list
	$(eval PRE_INSTALL_PACKAGES := $(PRE_INSTALL_PACKAGES) apt-transport-https)
	$(eval INSTALL_PACKAGES := $(INSTALL_PACKAGES) msodbcsql17 mssql-tools unixodbc-dev)

	# add microsoft key to apt ke management
	sudo wget -qO- 'https://packages.microsoft.com/keys/microsoft.asc' | sudo apt-key add -

	# add mssql tools repo to apt repo list
	curl $(MSSQL_DOWNLOADPATH) | sudo tee /etc/apt/sources.list.d/msprod.list
	
endif
endif


kubectl:
	# check if kubectl is already present
	$(eval KUBECTL_VERSION := $(shell kubectl version))

ifeq (,$(findstring "Command not found",$(KUBECTL_VERSION)))
	# kubectl not present - adding kubectl to installation list
	$(eval PRE_INSTALL_PACKAGES := $(PRE_INSTALL_PACKAGES) apt-transport-https)
	$(eval INSTALL_PACKAGES := $(INSTALL_PACKAGES) kubectl)

	# add kubernetes key to apt key management
	curl -s 'https://packages.cloud.google.com/apt/doc/apt-key.gpg' | sudo apt-key add -

	# add kubernetes repo to apt repo list
	sudo touch /etc/apt/sources.list.d/kubernetes.list
	echo "deb http://apt.kubernetes.io/ kubernetes-$(LINUX_DISTRIBUTION_CODENAME_OLD) main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
endif


azurecli:
	# check if azure-cli is already present
	$(eval AZ_VERSION = $(shell az --version))

ifeq (,$(findstring "Command not found",$(AZ_VERSION)))
	# azure-cli not present - adding azure-cli packages to installation list
	$(eval PRE_INSTALL_PACKAGES := $(PRE_INSTALL_PACKAGES) apt-transport-https)
	$(eval INSTALL_PACKAGES := $(INSTALL_PACKAGES) azure-cli)

	# add microsoft key to apt key management
	sudo wget -qO- 'https://packages.microsoft.com/keys/microsoft.asc' | sudo apt-key add -

	# add azure repo to apt repo list
	echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $(LINUX_DISTRIBUTION_CODENAME) main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
endif

jp:
	# get jp client and change rights
	sudo wget https://github.com/jmespath/jp/releases/download/$(JP_VERSION)/jp-linux-amd64 -O /usr/local/bin/jp \
	&& sudo chmod +x /usr/local/bin/jp
