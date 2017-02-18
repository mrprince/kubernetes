# kubernetes
Host IPAddress: 192.168.0.164

## 1. Install Certificate 
```
curl -s https://raw.githubusercontent.com/mrprince/kubernetes/master/make-ca-cert.sh| bash -s 192.168.0.164 IP:10.254.0.1,DNS:kubernetes,DNS:kubernetes.default,DNS:kubernetes.default.svc,DNS:kubernetes.default.svc.cluster.local
```

## 2. Install Kubernetes
```
yum install docker etcd kubernetes -y
```

## 3. Configuration
```
vi /etc/etcd/etcd.conf
#ETCD_LISTEN_CLIENT_URLS="http://localhost:2379"
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"
#ETCD_ADVERTISE_CLIENT_URLS="http://localhost:2379"
ETCD_ADVERTISE_CLIENT_URLS="http://0.0.0.0:2379"

vi /etc/kubernetes/apiserver
KUBE_API_ARGS="--client_ca_file=/srv/kubernetes/ca.crt  --tls-private-key-file=/srv/kubernetes/server.key --tls-cert-file=/srv/kubernetes/server.cert"

vi /etc/kubernetes/controller-manager
KUBE_CONTROLLER_MANAGER_ARGS="--service_account_private_key_file=/srv/kubernetes/server.key --root-ca-file=/srv/kubernetes/ca.crt"
```
