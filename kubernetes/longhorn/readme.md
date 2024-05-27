# Longhorn 🚀

To achieve HA, three VMs must be created.

```bash
qm clone 8010 307 --name k3s-lnghrn-01 -full -storage local-zfs
qm set 307 --ipconfig0 ip=172.16.50.17/24,gw=172.16.50.254
qm resize 307 virtio0 +224G
qm set 307 --core 4 --memory 4096 --balloon 0
qm start 307

qm clone 8010 308 --name k3s-lnghrn-02 -full -storage local-zfs
qm set 308 --ipconfig0 ip=172.16.50.18/24,gw=172.16.50.254
qm resize 308 virtio0 +224G
qm set 308 --core 4 --memory 4096 --balloon 0
qm start 308

qm clone 8010 309 --name k3s-lnghrn-03 -full -storage local-zfs
qm set 309 --ipconfig0 ip=172.16.50.19/24,gw=172.16.50.254
qm resize 309 virtio0 +224G
qm set 309 --core 4 --memory 4096 --balloon 0
qm start 309
```

# Installation

For Debian and Ubuntu, please install Linux kernel extra modules before loading the kernel modules

```bash
sudo apt update && sudo apt install -y linux-modules-extra-`uname -r`
```

In order to assign deployment/pods to longhorn volume the workernodes need to have the label `longhorn=true` so that the requiring dependencies are installed on the worker nodes.

```bash
kubectl label --overwrite nodes k3s-wkr-01 longhorn=true
kubectl label --overwrite nodes k3s-wkr-02 longhorn=true
kubectl label --overwrite nodes k3s-lnghrn-01 storage=true
```

**Remember to deattach the worker nodes in the Longhorn GUI!**

`longhorn.yaml`

```bash
kubectl apply -f longhorn.yaml
```

## Ingress

```bash
kubectl apply -f ingress.yaml
```

# Verifiy

```bash
kubectl get pods --namespace longhorn-system
```
