# Proxmox
Configure GPU Passthrough in Proxmox
[See link](https://www.reddit.com/r/homelab/comments/b5xpua/the_ultimate_beginners_guide_to_gpu_passthrough/)

# Linux Guest

```bash
sudo apt-get update

sudo apt-get upgrade

sudo apt-get install build-essential # build-essential is required for nvidia drivers to compile

sudo apt install --no-install-recommends nvidia-cuda-toolkit nvidia-headless-550 nvidia-utils-550 libnvidia-encode-550
```
REBOOT

Install nvtop
```bash
sudo apt-get install nvtop
```
If you want to make sure everything is working, log back in and run nvidia-smi. This is an optional utility to see what's going on with your GPU(s):

```bash
nvidia-smi
```

# Installing the GPU Operator on K3S Cluster

Add Helm Repo:
```bash
helm repo add nvidia https://helm.ngc.nvidia.com/nvidia
helm repo update
```
Create Namespace:
```bash
kubectl create namespace gpu-operator
```
By default if you have only one GPU resource only one pod can use that resource. This custom config file that tells the GPU operator to treat each physical GPU as four logical GPUs.
```bash
kubectl apply -f time-slicing-config.yaml
```
Then, we can install the GPU Operator
```bash
helm install gpu-operator -n gpu-operator nvidia/gpu-operator --values values.yaml
```
# Making sure it all works
```bash
kubectl apply -f nvidia-smi.yaml
```
If nvidia-smi works you can delete the pod.
```bash
kubectl delete -f nvidia-smi.yaml
```
Alternative:
```bash
kubectl apply -f cuda-vectoradd.yaml
```
#
### **Important**
Make sure that `runtimeClassName: nvidia` and `resources: limits: nvidia.com/gpu: 1`are set in deployent which should use gpu. [See link](https://docs.k3s.io/advanced)

