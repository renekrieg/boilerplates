# Install CSI driver
curl -skSL https://raw.githubusercontent.com/kubernetes-csi/csi-driver-smb/v1.13.0/deploy/install-driver.sh | bash -s v1.13.0 --
```
# Create SMB creds
```bash
kubectl create secret generic smbcreds --from-literal username=k3s --from-literal password="password"
```
# Create storage class
```bash
kubectl create -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-smb/master/deploy/example/storageclass-smb.yaml
```
# Check status
```bash


