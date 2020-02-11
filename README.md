# tidb-px
Running tidb backed by Portworx volumes

## Installing portworx
Refer to https://docs.portworx.com/portworx-install-with-kubernetes/

For this doc, Portworx was installed on AKS using: https://docs.portworx.com/portworx-install-with-kubernetes/cloud/azure/aks/


## Installing tidb

### Install helm
```
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
kubectl apply -f https://raw.githubusercontent.com/pingcap/tidb-operator/master/manifests/tiller-rbac.yaml && helm init --service-account=tiller --upgrade
helm list
```

Verify:
```
kubectl get po -n kube-system -l name=tiller
```

Initialize pingcap helm repo:
```
helm init --upgrade
helm repo add pingcap https://charts.pingcap.org/
helm search pingcap -l
```


### Install TiDB Operator
Create the crd:
```
wget https://raw.githubusercontent.com/pingcap/tidb-operator/master/manifests/crd.yaml
kubectl apply -f crd.yaml
kubectl get crd tidbclusters.pingcap.com
```

Deploy the operator:
```
mkdir tidb-operator
cd tidb-operator/
helm inspect values pingcap/tidb-operator --version=v1.0.6  > values-tidb-operator.yaml
helm install pingcap/tidb-operator --name=tidb-operator --namespace=tidb-admin --version=v1.0.6 -f values-tidb-operator.yaml
kubectl -n tidb-admin get pods
```


### Create Portworx StorageClasses
Create the storageClass below:
```
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
 name: px-db
provisioner: kubernetes.io/portworx-volume
allowVolumeExpansion: true
parameters:
 repl: "3"
 priority_io: "high"
 io_profile: "db"
```

### Deploy the cluster


## Load test tidb




