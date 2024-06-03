```bash
kubectl create namespace media
```

# Create PV

```bash
kubectl apply -f `media-pv.yaml`
kubectl get pv
```

# Create PVC

```bash
kubectl apply -f `media-pvc.yaml`
kubectl get pvc -n media
```
