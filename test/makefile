env: $(eval PRE_INSTALL_PACKAGES : $(PRE_INSTALL_PACKAGES)) 

install: env test
	$(info $(PRE_INSTALL_PACKAGES))

ifdef PRE_INSTALL_PACKAGES
	$(info ---PRE_INSTALL---)
else
	$(info ---funktioniert_nicht--)
endif

test: 
	$(eval PRE_INSTALL_PACKAGES := $(PRE_INSTALL_PACKAGES) apt-transport-https)
