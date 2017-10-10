# openshift-first-steps
OpenShift origin 3.6+ playground

## Requierements
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
### Troubleshooting

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