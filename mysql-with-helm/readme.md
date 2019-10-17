Install mysql
```
qzhao-mbp:helm-test qzhao$ helm search mysql

qzhao-mbp:helm-test qzhao$ helm install --name my-mysql --set mysqlLRootPassword=secrectpassword,mysqlUser=my-user,mysqlPassword=my-password,mysqlDatabase=my-database stable/mysql
NAME:   my-mysql
LAST DEPLOYED: Thu Oct 17 14:56:47 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME           DATA  AGE
my-mysql-test  1     1s

==> v1/Deployment
NAME      READY  UP-TO-DATE  AVAILABLE  AGE
my-mysql  0/1    1           0          1s

==> v1/PersistentVolumeClaim
NAME      STATUS  VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
my-mysql  Bound   pvc-88e48fc5-c201-41a0-b32e-08f872600444  8Gi       RWO           standard      1s

==> v1/Pod(related)
NAME                       READY  STATUS   RESTARTS  AGE
my-mysql-6f475dbb57-mxwf6  0/1    Pending  0         0s

==> v1/Secret
NAME      TYPE    DATA  AGE
my-mysql  Opaque  2     1s

==> v1/Service
NAME      TYPE       CLUSTER-IP    EXTERNAL-IP  PORT(S)   AGE
my-mysql  ClusterIP  10.110.103.5  <none>       3306/TCP  1s


NOTES:
MySQL can be accessed via port 3306 on the following DNS name from within your cluster:
my-mysql.default.svc.cluster.local

To get your root password run:

    MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default my-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode; echo)

To connect to your database:

1. Run an Ubuntu pod that you can use as a client:

    kubectl run -i --tty ubuntu --image=ubuntu:16.04 --restart=Never -- bash -il

2. Install the mysql client:

    $ apt-get update && apt-get install mysql-client -y

3. Connect using the mysql cli, then provide your password:
    $ mysql -h my-mysql -p

To connect to your database directly from outside the K8s cluster:
    MYSQL_HOST=127.0.0.1
    MYSQL_PORT=3306

    # Execute the following command to route the connection:
    kubectl port-forward svc/my-mysql 3306

    mysql -h ${MYSQL_HOST} -P${MYSQL_PORT} -u root -p${MYSQL_ROOT_PASSWORD}
```
