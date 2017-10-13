# openshift-first-steps
OpenShift origin playground

# Running in a Container

## Requirements
- Docker 17.05.0-ce+

## Start
```sh
./start-openshift-origin
```

## Access openshift
```sh
https://localhost:8443
test,test
```
## Open console inside container
```sh
docker exec -it origin bash
```
## Troubleshooting

If you get the following error: 

```sh
 9065 node.go:282] failed to run Kubelet: failed to create kubelet: misconfiguration: kubelet cgroup driver: "systemd" is different from docker cgroup driver: "cgroupfs"
```
The issue is caused due to using cgrougfs as Cgroup Driver for Docker. You can verify your Docker config by runnin
```sh
docker info | grep Cgroup
Cgroup Driver: cgroupfs
```
Add the following arg to your (or create it) /etc/systemd/system/docker.service.d/docker-thinpool.conf:
```sh
 [Service]
 ExecStart=/usr/bin/dockerd  --exec-opt native.cgroupdriver=systemd
```

And restart docker-related services
```sh
systemctl daemon-reload
systemctl restart docker.service
```
You should see now the right Cgroup driver setup
```sh
docker info | grep Cgroup
Cgroup Driver: systemd
```

# Using Vagrant

## Requirements
- Vagrant 1.9+
- Go version 1.8+
- 10GB+ free space

## Install vagrant-openshift plugin

```sh
vagrant plugin install vagrant-openshift
```
## Clone openshift repo

This will take time as it needs to download 5GB
```sh
vagrant origin-local-checkout
```

## Initialize local vagrant conf
```sh
vagrant origin-init --stage inst --os rhel7 openshift3
```
## Start the machine
This will take time as it needs to download 5.5GB
```sh
vagrant up --provider=virtualbox
```
# Running openshift via minishift (Recommended setup)

## Download minishift

- Download latest [release](https://github.com/minishift/minishift/releases) and uncompress it

## Requirements for KVM
- docker machine [latest](https://github.com/docker/machine/releases)
- docker-machine-kvm [latest](https://github.com/dhiltgen/docker-machine-kvm/releases/)

### Installing KVM Driver
```sh
sudo curl -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.10.0/docker-machine-driver-kvm-ubuntu16.04 -o /usr/local/bin/docker-machine-driver-kvm
sudo chmod +x /usr/local/bin/docker-machine-driver-kvm
```
```sh
sudo apt install libvirt-bin qemu-kvm
sudo usermod -a -G libvirtd $USER
newgrp libvirtd
sudo /usr/sbin/kvm-ok # Check if KVM is running fine
``` 

## Starting Minishift
```sh
$ minishift> ./minishift start
```

## Requirements for virtualbox
- virtualbox

## Starting Minishift
```sh
$ minishift> ./minishift start --vm-driver virtualbox
```

## Using oc
```sh
minishift oc-env 
```

## More info about minishift
Checkout [here](https://docs.openshift.org/latest/minishift/using/managing-minishift.html)