# Install CSI driver on worker nodes
```bash
curl -skSL https://raw.githubusercontent.com/kubernetes-csi/csi-driver-smb/v1.13.0/deploy/install-driver.sh | bash -s v1.13.0 --
```
# Create SMB creds
```bash
kubectl create secret generic smbcreds --from-literal username=USERNAME --from-literal password="PASSWORD"
```
# Create storage class
```bash
kubectl create -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-smb/master/deploy/example/storageclass-smb.yaml
```
# Check status
```bash


