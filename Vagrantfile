# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 2
  end

  config.vm.provision "shell", inline: <<-SHELL
    # add docker repo
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable"

    # and golang 1.9 support
    # add-apt-repository ppa:gophers/archive
    add-apt-repository ppa:longsleep/golang-backports

    # install base requirements
    apt-get update
    apt-get install -y --no-install-recommends wget curl jq \
        make shellcheck bsdmainutils psmisc
    apt-get install -y docker-ce golang-1.9-go

    # needed for go
    apt-get install -y git
    # needed for docker
    usermod -a -G docker ubuntu

    # use "EOF" not EOF to avoid variable substitution of $PATH
    cat << "EOF" >> /home/ubuntu/.bash_profile
export PATH=$PATH:/usr/lib/go-1.9/bin:/home/ubuntu/go/bin
export GOPATH=/home/ubuntu/go
export LC_ALL=en_US.UTF-8
cd go/src/github.com/tendermint/tendermint
EOF

    mkdir -p /home/ubuntu/go/bin
    mkdir -p /home/ubuntu/go/src/github.com/tendermint
    ln -s /vagrant /home/ubuntu/go/src/github.com/tendermint/tendermint

    chown -R ubuntu:ubuntu /home/ubuntu/go
    chown ubuntu:ubuntu /home/ubuntu/.bash_profile

    # get all deps and tools, ready to install/test
    su - ubuntu -c 'cd /home/ubuntu/go/src/github.com/tendermint/tendermint && make get_vendor_deps && make tools'
  SHELL
end
