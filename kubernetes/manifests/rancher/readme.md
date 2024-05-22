# Installation with helm and traefik allready set up
Make sure, that helm and cert-manager are installed. If not install them.
# Install helm
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
# Add Rancher Helm Repository
```bash
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
kubectl create namespace cattle-system
```

# Install Cert-Manager
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.2/cert-manager.crds.yaml
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager jetstack/cert-manager \
--namespace cert-manager \
--create-namespace \
--version v1.13.2
kubectl get pods --namespace cert-manager
```

# Create Certificate 
`rancher-cert.yaml`
```yaml
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: rancher-local-krlab-tech
  namespace: cattle-system
spec:
  commonName: rancher.local.krlab.tech
  dnsNames:
    - rancher.local.krlab.tech
  issuerRef:
    name: letsencrypt-production
    kind: ClusterIssuer
  secretName: tls-rancher-ingress
```
```bash
kubectl apply -f rancher-cert.yaml
```
Check Status of Certificate (`Ready` status of output must be `True`)
```bash
kubectl -n cattle-system get certificate rancher-local-krlab-tech
```
Create DNS entry for rancher.local.krlab.tech. Then install rancher.
```bash
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancher.local.krlab.tech \
  --set bootstrapPassword=admin \
  --set ingress.tls.source=secret
```
If you are doing this after already installing Rancher (with the default setting of `ingress.tls.source=rancher`), 
you can overwrite the self-generated `tls-rancher-ingress` secret with your own certificate, then update your 
deployment. You may want to get your current Rancher version using `helm ls -n cattle-system` and provide it 
in your `helm upgrade` command so you don't unexpectedly upgrade your Rancher version.
```bash
helm upgrade rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.local.krlab.tech \
  --set ingress.tls.source=secret \
  --version <DEPLOYED_RANCHER_VERSION>
```
## Ingress TLS Troubleshooting
You can validate the contents of your `tls-rancher-ingress` Secret using commands like this:
```bash
kubectl -n cattle-system get secret tls-rancher-ingress -o jsonpath='{.data}' | jq '."tls.crt"' | tr -d '"' | base64 --decode | openssl x509 -text
```

There are also helpful instructions covering a handful of situations in Rancher's documentation:
* [JimsGarage](https://github.com/JamesTurland/JimsGarage/blob/main/Kubernetes/Rancher-Deployment/readme.md)
* [Adding TLS Secrets](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/resources/add-tls-secrets)
* [Update Rancher Certificate](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/resources/update-rancher-certificate)
