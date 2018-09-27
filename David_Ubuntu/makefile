SHELL := /bin/bash # using bash to make sourcing possible, see second to last command in recipe "setup_oc_client"
OPENSHIFT_VERSION=v3.10.0
OC_CLIENT_HASH_ADDENDUM = dd10d17
OC_CLIENT_PACKAGE = openshift-origin-client-tools-$(OPENSHIFT_VERSION)-$(OC_CLIENT_HASH_ADDENDUM)-linux-64bit
OC_CLIENT_PACKAGE_FILENAME = $(OC_CLIENT_PACKAGE).tar.gz

proxy_apt:
	echo "Acquire::http::proxy "http://10.171.251.30:8080";" > /etc/apt.conf
	echo "Acquire::https::proxy "http://10.171.251.30:8080";" >> /etc/apt.conf

#proxy__environment:
	

setup_docker-ce:
	echo "Docker-CE installation"
	apt-get update
	apt-get install -y apt-transport-https ca-certificates curl software-properties-common
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(shell lsb_release -cs) stable"
	apt-get update
	apt-get install -y docker-ce

setup_docker-compose:
	echo "Docker-Compose installation"
	apt-get update
	curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	chmod +x /usr/local/bin/docker-compose

setup_oc_client:
        wget -c -N -P /tmp/ https://github.com/openshift/origin/releases/download/$(OPENSHIFT_VERSION)/$(OC_CLIENT_PACKAGE_FILENAME)
        tar -C /tmp -xzf /tmp/$(OC_CLIENT_PACKAGE_FILENAME) $(OC_CLIENT_PACKAGE)/oc
        cp /tmp/$(OC_CLIENT_PACKAGE)/oc /usr/local/sbin
        rm -r /tmp/$(OC_CLIENT_PACKAGE)
        oc completion bash > oc_completion.sh
        source oc_completion.sh
        rm oc_completion.sh

setup_git:
	apt-get update
	apt-get install -y git
	echo parse_git_branch() { git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/' } if [ "$color_prompt" = yes ]; then PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[01;31m\] $(parse_git_branch)\[\033[00m\]\$ ' else PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w$(parse_git_branch)\$ 'fi
