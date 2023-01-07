## 
microk8s enable storage
microk8s enable dashboard
https://microk8s.io/docs/addon-dashboard
microk8s enable dns
https://microk8s.io/docs/addon-dns
microk8s enable community

sudo apt-get install nano

## add: alias kubectl="microk8s kubectl"
nano ~/.bashrc

### add user to group microk8s
sudo usermod -a -G microk8s joachim
### allow .kube dir
sudo chown -f -R joachim ~/.kube

### reload user groups
newgrp microks

microk8s enable rbac

sudo snap install kubectl --classic

microk8s enable ingress
https://microk8s.io/docs/addon-ingress

microk8s config

## Load Balancing

microk8s enable metallb
192.168.1.200-192.168.1.240
````yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: custom-addresspool
  namespace: metallb-system
spec: 
  addresses:
  - 192.168.1.200-192.168.1.240
````

https://microk8s.io/docs/addon-metallb

The IP address exposed by each instance is a virtual IP addresses provided by MetalLB, via the add-on we configured earlier. It’s difficult to overstate how useful this is. In a nutshell, it instantly provides a load-balancer/reverse proxy to front your replicas and assigns it an IP address from an address pool taken from your home network. This will be how you access your Pi-Hole DNS and web endpoints.

As a side note, you will notice that for each application we are deploying two load balancer services, one for TCP and one for UDP. The reason for this is that Kubernetes doesn’t allow mixed protocols for services of type LoadBalancer. However we can still use the same IP address for each service using an annotation.


microk8s enable cert-manager
https://microk8s.io/docs/addon-cert-manager

helm repo add external-secrets https://charts.external-secrets.io

helm install external-secrets external-secrets/external-secrets -n external-secrets --create-namespace --set installCRDs=true

kubect apply -f secret.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: akeyless-secret-creds
  namespace: akeyless-vault
type: Opaque
stringData:
  accessId: <the-access-id>
  accessType:  api_key
  accessTypeParam:  <the-api-key>
```


kubectl apply -k argo...




