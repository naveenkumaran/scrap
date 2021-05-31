# scrap
Scrap artifacts required for demo

## Redis Cluster 
Clone the repository and change directory to redis folder.
```bash
git clone https://github.com/naveenkumaran/scrap.git && cd "$(basename $_ .git)"
```
Create symbolic link for redis server and redis-cli
```bash
ln -s redis/redis-server /usr/local/bin/redis-server
ln -s redis/redis-server/src/redis-cli /usr/local/bin/redis-cli
```

Run ./start-cluster.sh to start the redis cluster 
Create a cluster by running the following command
```bash
redis-cli --cluster create 127.0.0.1:8000 127.0.0.1:8001 \
127.0.0.1:8002 127.0.0.1:8003 127.0.0.1:8004 127.0.0.1:8005 \
--cluster-replicas 1
```


Check the health of the cluster with the redis-cli command
```bash
redis-cli --cluster -h 127.0.0.1 -p 8001 
$ CLUSTER INFO'
```

 
# Kubernetes - Authentication normal user using X509 Certificates

## Create private key
```bash
openssl genrsa -out myuser.key 2048
openssl req -new -key myuser.key -out myuser.csr
```

## Generate base64 format String for CSR
```bash
cat myuser.csr | base64 | tr -d "\n"
```

NOTE: Ouput BASE64 encoded string need to be passed in the request attribute of CSR object

## Logon to the server and Create CSR
```bash
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: myuser
spec:
  groups:
  - system:authenticated
  request: {{myuser.csr}}
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
```

## Approve the CSR
```bash
kubectl certificate approve myuser
```
## Get the BASE64 encoded signed certificate
```bash
kubectl get csr myuser -o jsonpath='{.status.certificate}'| base64 -d > myuser.crt
```

## Create role & role binding
```bash
kubectl create role developer --verb=create --verb=get --verb=list --verb=update --verb=delete --resource=pods 
kubectl create rolebinding developer-binding-myuser --role=developer --user=myuser
```

## Get the Kubernetes ca file
```bash
kubectl config view -o jsonpath='{.clusters[0].cluster.certificate-authority-data}' --raw | base64 --decode - > k8s-ca.crt
```
## Create cluster configuration using server name and write the configuration to a file.
Set REMOTE_SERVER
```bash
kubectl config set-cluster $(kubectl config view -o jsonpath='{.clusters[0].name}') --server=${REMOTE_SERVER} --certificate-authority=k8s-ca.crt --kubeconfig=myuser-config --embed-certs
```
## Add to kube config
```bash
kubectl config set-credentials myuser-la --client-certificate=myuser.crt --client-key=myuseruser.key --embed-certs --kubeconfig=myuser-config
```
## Set kube context
```bash
kubectl config set-context myuser-la --cluster=cluster.local --user=myuser --kubeconfig=myuser-config
```

## Switch context
```bash
kubectl config use-context myuser-la --kubeconfig=myuser-config
```
