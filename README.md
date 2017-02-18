# Centos7 Install Kubernetes Cluster On One Host
Host IPAddress: 192.168.0.165
Disable firewall, disable selinux:
```
systemctl disable firewalld
systemctl stop firewalld
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
setenforce 0
```
## 1. Install Certificate 
```
curl -s https://raw.githubusercontent.com/mrprince/kubernetes/master/make-ca-cert.sh | bash -s 192.168.0.165 IP:10.254.0.1,DNS:kubernetes,DNS:kubernetes.default,DNS:kubernetes.default.svc,DNS:kubernetes.default.svc.cluster.local
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
#KUBE_API_ADDRESS="--insecure-bind-address=127.0.0.1"
KUBE_API_ADDRESS="--insecure-bind-address=0.0.0.0"
#KUBE_ETCD_SERVERS="--etcd-servers=http://127.0.0.1:2379"
KUBE_ETCD_SERVERS="--etcd-servers=http://192.168.0.165:2379"
#KUBE_API_ARGS=""
KUBE_API_ARGS="--client_ca_file=/srv/kubernetes/ca.crt  --tls-private-key-file=/srv/kubernetes/server.key --tls-cert-file=/srv/kubernetes/server.cert"

vi /etc/kubernetes/controller-manager
KUBE_CONTROLLER_MANAGER_ARGS="--service_account_private_key_file=/srv/kubernetes/server.key --root-ca-file=/srv/kubernetes/ca.crt"

vi /etc/kubernetes/kubelet 
#KUBELET_ADDRESS="--address=127.0.0.1"
KUBELET_ADDRESS="--address=0.0.0.0"
#KUBELET_HOSTNAME="--hostname-override=127.0.0.1"
KUBELET_HOSTNAME="--hostname-override=192.168.0.165"
#KUBELET_API_SERVER="--api-servers=http://127.0.0.1:8080"
KUBELET_API_SERVER="--api-servers=http://192.168.0.165:8080"
#KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=registry.access.redhat.com/rhel7/pod-infrastructure:latest"
KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=rhel7/pod-infrastructure:latest"

vi /usr/lib/systemd/system/docker.service
#ExecStart=/usr/bin/docker-current daemon \ExecStart=/usr/bin/docker-current daemon --registry-mirror=https://nexus.xxx.com \
```

## 4. Start Kubernetes
```
for SERVICE in docker etcd kube-apiserver kube-controller-manager kube-scheduler kube-proxy kubelet; do 
    systemctl restart $SERVICE
    systemctl enable $SERVICE
done
```

## 5. Install Cockpit
```
yum install cockpit cockpit-kubernetes -y
systemctl enable cockpit.socket
systemctl start cockpit.socket
```

## 6. Install DNS
```
kubectl crete -f https://github.com/mrprince/kubernetes/raw/master/kubedns-svc.yaml
# need to change - --kube-master-url=http://192.168.0.165:8080
kubectl crete -f https://github.com/mrprince/kubernetes/raw/master/kubedns-controller.yaml
```
## 7. Test
```
kubectl create -f https://github.com/mrprince/kubernetes/raw/master/test-pod.yml
```
Visit https://192.168.0.165:9090/


