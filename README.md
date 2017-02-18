# kubernetes

## 1. Install Certificate 
```
curl -s https://raw.githubusercontent.com/mrprince/kubernetes/master/make-ca-cert.sh| bash -s 192.168.0.164 IP:10.254.0.1,DNS:kubernetes,DNS:kubernetes.default,DNS:kubernetes.default.svc,DNS:kubernetes.default.svc.cluster.local
```

## 2. Install Kubernetes
```
yum install docker etcd kubernetes -y
```

## 3. Configuration
vi /etc/etcd/etcd.conf
```
```
