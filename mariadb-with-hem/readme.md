add following in values.yaml
```
image:
  repository: wardviaene/node-demo-app
  tag: v0.0.1
  pullPolicy: IfNotPresent

##
## MariaDB chart configuration
##
mariadb:
  enabled: true
  replication:
    enabled: false
  db:
    name: app
    user: app-user
  master:
    persistence:
      enabled: true
      accessMode: ReadWriteOnce
      size: 8Gi    
```

```
qzhao-mbp:demo-chart qzhao$ cat requirements.yaml
dependencies:
- name: mariadb
  version: 4.x.x
  repository: https://kubernetes-charts.storage.googleapis.com/
  condition: mariadb.enabled
  tags:
    - node-app-database
```
```
qzhao-mbp:demo-chart qzhao$ helm dependency update
qzhao-mbp:demo-chart qzhao$ ls charts/
mariadb-4.4.2.tgz
qzhao-mbp:demo-chart qzhao$ helm install .
NAME:   soft-heron
LAST DEPLOYED: Thu Oct 17 13:08:41 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                             DATA  AGE
soft-heron-mariadb               1     0s
soft-heron-mariadb-init-scripts  1     0s
soft-heron-mariadb-tests         1     0s

==> v1/Deployment
NAME                   READY  UP-TO-DATE  AVAILABLE  AGE
soft-heron-demo-chart  0/1    1           0          0s

==> v1/Pod(related)
NAME                                    READY  STATUS             RESTARTS  AGE
soft-heron-demo-chart-5479c664b8-fc829  0/1    ContainerCreating  0         0s
soft-heron-mariadb-0                    0/1    Pending            0         0s

==> v1/Secret
NAME                TYPE    DATA  AGE
soft-heron-mariadb  Opaque  2     0s

==> v1/Service
NAME                   TYPE          CLUSTER-IP      EXTERNAL-IP  PORT(S)       AGE
soft-heron-demo-chart  LoadBalancer  10.104.193.244  <pending>    80:32316/TCP  0s
soft-heron-mariadb     ClusterIP     10.97.103.124   <none>       3306/TCP      0s

==> v1beta1/StatefulSet
NAME                READY  AGE
soft-heron-mariadb  0/1    0s


NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get --namespace default svc -w soft-heron-demo-chart'
  export SERVICE_IP=$(kubectl get svc --namespace default soft-heron-demo-chart -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  echo http://$SERVICE_IP:80
```


## Download dependencies
```
helm dependency update
```
## Install Chart 
```
helm install .
```
## Upgrade Chart 
```
helm upgrade --set image.tag=v0.0.2,mariadb.db.password=$DB_APP_PASS <soft-heron> .
```
```
kubectl get pods -w

helm rollback --help
helm history <soft-heron>
helm rollback <soft-heron> 1
kubectl get pods
curl ip
```
```
qzhao-mbp:demo-chart qzhao$ helm list
NAME      	REVISION	UPDATED                 	STATUS  	CHART           	APP VERSION	NAMESPACE
soft-heron	1       	Thu Oct 17 13:08:41 2019	DEPLOYED	demo-chart-0.1.0	1.0        	default
qzhao-mbp:demo-chart qzhao$ helm delete --purge soft-heron
release "soft-heron" deleted
qzhao-mbp:demo-chart qzhao$ helm list --all
```
