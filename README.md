## 
```shell
sudo apt-get install nano

sudo snap install kubectl --classic

sudo snap install helm --classic

sudo snap install microk8s --classic --channel=1.29
```

### add user to group microk8s
```shell
sudo usermod -a -G microk8s joachim
```
### allow .kube dir
```shell
sudo chown -f -R joachim ~/.kube
```

### reload user groups

```shell
newgrp microk8s
```

```shell
kubectl config view --raw >~/.kube/config
```

Copy over .kube/config and adjust ip address.

https://microk8s.io/docs/addon-dashboard
https://microk8s.io/docs/addon-dns
https://microk8s.io/docs/addon-ingress
https://microk8s.io/docs/addon-cert-manager

```shell
microk8s enable community
microk8s enable hostpath-storage
microk8s enable ingress
microk8s enable rbac
microk8s enable dashboard
```

## Load Balancing
https://microk8s.io/docs/addon-metallb
The IP address exposed by each instance is a virtual IP addresses provided by MetalLB, via the add-on we configured earlier. It’s difficult to overstate how useful this is. In a nutshell, it instantly provides a load-balancer/reverse proxy to front your replicas and assigns it an IP address from an address pool taken from your home network. This will be how you access your Pi-Hole DNS and web endpoints.

As a side note, you will notice that for each application we are deploying two load balancer services, one for TCP and one for UDP. The reason for this is that Kubernetes doesn’t allow mixed protocols for services of type LoadBalancer. However we can still use the same IP address for each service using an annotation.

```shell
microk8s enable metallb
```

10.69.60.100-10.69.60.240
````yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: custom-addresspool
  namespace: metallb-system
spec:
  addresses:
    - 10.69.60.100-10.69.60.240
````
```shell
helm repo add external-secrets https://charts.external-secrets.io
````
```shell
helm install external-secrets external-secrets/external-secrets -n external-secrets --create-namespace --set installCRDs=true
````
```shell
kubectl create ns akeyless-vault
kubectl apply -f .idea/secret.yaml
```
Recreate the apikey AuthMethod and insert id and key.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: akeyless-secret-creds
  namespace: akeyless-vault
type: Opaque
stringData:
  accessId: <the-access-id>
  accessType: api_key
  accessTypeParam: <the-api-key>
```

```shell
kubectl create ns external-dns
kubectl apply -k external-dns

kubectl apply -k argocd

kubectl apply -f kubernetes-dashboard\dashboard-rbac.yaml

kubectl -n kubernetes-dashboard create token admin-user
```

Login with this token on {ip}/dashboard


