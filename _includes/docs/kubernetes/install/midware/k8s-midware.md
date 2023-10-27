# K8S - 实战篇 --- 中间件（K8S）



* TOC
{:toc}



## 一、概述



使用Operator、Helm在k8s集群中部署中间件。

**常用中间件包括：**

- PostgreSQL
- TimescaleDB（Helm）
- Redis
- RabbitMQ
- MySQL





## 二、环境部署



### 1.PostgreSQL

#### 1.1.安装方式

```shell
# 安装
PostgreSQL #2	zalando-incubator/postgres-operator
https://github.com/zalando/postgres-operator


https://postgres-operator.readthedocs.io/en/latest/

Quickstart
https://github.com/zalando/postgres-operator/blob/master/docs/quickstart.md#deployment-options
```

```shell
# 1.下载源码

git clone https://github.com/zalando/postgres-operator.git
cd postgres-operator
```



#### 1.2.安装Operator

```shell
# 安装步骤


# apply the manifests in the following order
kubectl create -f manifests/configmap.yaml  # configuration
kubectl create -f manifests/operator-service-account-rbac.yaml  # identity and permissions
kubectl create -f manifests/postgres-operator.yaml  # deployment
kubectl create -f manifests/api-service.yaml  # operator API to be used by UI
```

```shell
# 创建Operator

[root@k8s-master postgres-operator]# kubectl create -f manifests/configmap.yaml
configmap/postgres-operator created
[root@k8s-master postgres-operator]# kubectl create -f manifests/operator-service-account-rbac.yaml
serviceaccount/postgres-operator created
clusterrole.rbac.authorization.k8s.io/postgres-operator created
clusterrolebinding.rbac.authorization.k8s.io/postgres-operator created
clusterrole.rbac.authorization.k8s.io/postgres-pod created
[root@k8s-master postgres-operator]# kubectl create -f manifests/postgres-operator.yaml
deployment.apps/postgres-operator created
[root@k8s-master postgres-operator]# kubectl create -f manifests/api-service.yaml
service/postgres-operator created


[root@k8s-master postgres-operator]# kubectl get all
NAME                                     READY   STATUS    RESTARTS   AGE
pod/postgres-operator-569b58b8c6-6qdlw   1/1     Running   0          53s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/kubernetes          ClusterIP   10.96.0.1        <none>        443/TCP          114d
service/pg-svc-master       NodePort    10.104.70.22     <none>        5432:31669/TCP   39m
service/pg-svc-replica      NodePort    10.105.246.108   <none>        5432:30871/TCP   36m
service/postgres-operator   ClusterIP   10.98.87.50      <none>        8080/TCP         42s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres-operator   1/1     1            1           53s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/postgres-operator-569b58b8c6   1         1         1       53s
```

```shell
# 无法下载镜像  registry.opensource.zalan.do/acid/postgres-operator:v1.7.1
# 通过阿里云镜像服务拉取镜像
# 详细信息参考拉取墙外镜像文档

# 登录阿里云Docker Registry
# 公有仓库可以不登录

$ docker login --username=hollysyshs registry.cn-beijing.aliyuncs.com
# 用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。
# 您可以在访问凭证页面修改凭证密码。



# 拉取镜像
# docker pull registry.cn-beijing.aliyuncs.com/k8s-middleware/zalando-postgres-operator:[镜像版本号]
docker pull registry.cn-beijing.aliyuncs.com/k8s-middleware/zalando-postgres-operator:v1.7.1


# 打标签
docker tag registry.cn-beijing.aliyuncs.com/k8s-middleware/zalando-postgres-operator:v1.7.1 registry.opensource.zalan.do/acid/postgres-operator:v1.7.1
```



#### 1.3.修改配置

```shell
# vim complete-postgres-manifest.yaml 

  volume:
    size: 20Gi
    storageClass: "rook-ceph-block"

```



#### 1.4.安装Postgres集群

```shell
# create a Postgres cluster
kubectl create -f manifests/complete-postgres-manifest.yaml

# check the deployed cluster
kubectl get postgresql

# check created database pods
kubectl get pods -l application=spilo -L spilo-role

# check created service resources
kubectl get svc -l application=spilo -L spilo-role
```

```shell
[root@k8s-master postgres-operator]# kubectl create -f manifests/complete-postgres-manifest.yaml
postgresql.acid.zalan.do/acid-test-cluster created


# 查看
[root@k8s-master postgres-operator]# kubectl get all
NAME                                     READY   STATUS    RESTARTS   AGE
pod/acid-test-cluster-0                  1/1     Running   0          80s
pod/acid-test-cluster-1                  1/1     Running   0          41s
pod/postgres-operator-569b58b8c6-6qdlw   1/1     Running   0          10m

NAME                               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/acid-test-cluster          ClusterIP   10.105.111.206   <none>        5432/TCP         81s
service/acid-test-cluster-config   ClusterIP   None             <none>        <none>           38s
service/acid-test-cluster-repl     ClusterIP   10.103.24.96     <none>        5432/TCP         81s
service/postgres-operator          ClusterIP   10.98.87.50      <none>        8080/TCP         10m

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres-operator   1/1     1            1           10m

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/postgres-operator-569b58b8c6   1         1         1       10m

NAME                                 READY   AGE
statefulset.apps/acid-test-cluster   2/2     81s

NAME                                         TEAM   VERSION   PODS   VOLUME   CPU-REQUEST   MEMORY-REQUEST   AGE   STATUS
postgresql.acid.zalan.do/acid-test-cluster   acid   14        2      20Gi     10m           100Mi            82s   Running


[root@k8s-master postgres-operator]# kubectl get pvc
NAME                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
pgdata-acid-test-cluster-0   Bound    pvc-6b5cdb31-d549-4bf7-9742-c7ba5a9f5c37   20Gi       RWO            rook-ceph-block   109s
pgdata-acid-test-cluster-1   Bound    pvc-4bb98bf7-b58e-4368-93f4-7fa2ac2c5f17   20Gi       RWO            rook-ceph-block   70s
```

```shell
# 无法下载镜像  registry.opensource.zalan.do/acid/spilo-14:2.1-p3
# 通过阿里云镜像服务拉取镜像


# 拉取镜像
# docker pull registry.cn-beijing.aliyuncs.com/k8s-middleware/zalando-spilo-14:[镜像版本号]
docker pull registry.cn-beijing.aliyuncs.com/k8s-middleware/zalando-spilo-14:2.1-p3


# 打标签
docker tag  registry.cn-beijing.aliyuncs.com/k8s-middleware/zalando-spilo-14:2.1-p3 registry.opensource.zalan.do/acid/spilo-14:2.1-p3
```



#### 1.5.外部访问数据库

**1.新建service**

```shell
# 新建service


# pg-svc-master.yaml

apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: pg-svc-master
  labels:
    spilo-role: master
spec:
  type: NodePort
  ports:
    - port: 5432
      name: pg-svc-master
      targetPort: 5432
  selector:
    spilo-role: master


# pg-svc-replica.yaml

apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: pg-svc-replica
  labels:
    spilo-role: replica
spec:
  type: NodePort
  ports:
    - port: 5432
      name: pg-svc-replica
      targetPort: 5432
  selector:
    spilo-role: replica
    


[root@k8s-master postgres-operator]# kubectl get svc
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
acid-test-cluster          ClusterIP   10.105.111.206   <none>        5432/TCP         7m54s
acid-test-cluster-config   ClusterIP   None             <none>        <none>           7m11s
acid-test-cluster-repl     ClusterIP   10.103.24.96     <none>        5432/TCP         7m54s

pg-svc-master              NodePort    10.104.70.22     <none>        5432:31669/TCP   55m
pg-svc-replica             NodePort    10.105.246.108   <none>        5432:30871/TCP   52m
```



```shell
# 修改主数据库密码 postgres

[root@k8s-master ~]#  kubectl get pod
NAME                                 READY   STATUS    RESTARTS   AGE
acid-minimal-cluster-0               1/1     Running   0          17h
acid-minimal-cluster-1               1/1     Running   0          16h
postgres-operator-569b58b8c6-dqfj2   1/1     Running   0          19h


[root@k8s-master operator]# kubectl exec -it acid-test-cluster-0 -- bash

 ____        _ _
/ ___| _ __ (_) | ___
\___ \| '_ \| | |/ _ \
 ___) | |_) | | | (_) |
|____/| .__/|_|_|\___/
      |_|

This container is managed by runit, when stopping/starting services use sv

Examples:

sv stop cron
sv restart patroni

Current status: (sv status /etc/service/*)

run: /etc/service/patroni: (pid 33) 552s
run: /etc/service/pgqd: (pid 34) 552s
root@acid-test-cluster-0:/home/postgres# su - postgres
postgres@acid-minimal-cluster-0:~$ psql
psql (14.0 (Ubuntu 14.0-1.pgdg18.04+1))
Type "help" for help.

postgres=# ALTER USER postgres with encrypted password 'postgres';
ALTER ROLE
postgres=# \d
                      List of relations
 Schema |          Name           |     Type      |  Owner   
--------+-------------------------+---------------+----------
 public | failed_authentication_0 | view          | postgres
 public | failed_authentication_1 | view          | postgres
 public | failed_authentication_2 | view          | postgres
 public | failed_authentication_3 | view          | postgres
 public | failed_authentication_4 | view          | postgres
 public | failed_authentication_5 | view          | postgres
 public | failed_authentication_6 | view          | postgres
 public | failed_authentication_7 | view          | postgres
 public | pg_auth_mon             | view          | postgres
 public | pg_stat_kcache          | view          | postgres
 public | pg_stat_kcache_detail   | view          | postgres
 public | pg_stat_statements      | view          | postgres
 public | pg_stat_statements_info | view          | postgres
 public | postgres_log            | table         | postgres
 public | postgres_log_0          | foreign table | postgres
 public | postgres_log_1          | foreign table | postgres
 public | postgres_log_2          | foreign table | postgres
 public | postgres_log_3          | foreign table | postgres
 public | postgres_log_4          | foreign table | postgres
 public | postgres_log_5          | foreign table | postgres
 public | postgres_log_6          | foreign table | postgres
 public | postgres_log_7          | foreign table | postgres
(22 rows)

postgres=# \du
                                                     List of roles
    Role name    |                         Attributes                         |               Member of                
-----------------+------------------------------------------------------------+----------------------------------------
 admin           | Create DB, Cannot login                                    | {bar_owner,zalando,foo_user}
 bar_data_owner  | Cannot login                                               | {bar_data_reader,bar_data_writer}
 bar_data_reader | Cannot login                                               | {}
 bar_data_writer | Cannot login                                               | {bar_data_reader}
 bar_owner       | Cannot login                                               | {bar_reader,bar_writer,bar_data_owner}
 bar_reader      | Cannot login                                               | {}
 bar_writer      | Cannot login                                               | {bar_reader}
 foo_user        |                                                            | {}
 postgres        | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 robot_zmon      | Cannot login                                               | {}
 standby         | Replication                                                | {}
 zalando         | Superuser, Create DB                                       | {}
 zalandos        | Create DB, Cannot login                                    | {}

postgres=# \l
                                  List of databases
   Name    |   Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
-----------+-----------+----------+-------------+-------------+-----------------------
 bar       | bar_owner | UTF8     | en_US.utf-8 | en_US.utf-8 | 
 foo       | zalando   | UTF8     | en_US.utf-8 | en_US.utf-8 | 
 postgres  | postgres  | UTF8     | en_US.utf-8 | en_US.utf-8 | 
 template0 | postgres  | UTF8     | en_US.utf-8 | en_US.utf-8 | =c/postgres          +
           |           |          |             |             | postgres=CTc/postgres
 template1 | postgres  | UTF8     | en_US.utf-8 | en_US.utf-8 | =c/postgres          +
           |           |          |             |             | postgres=CTc/postgres
(5 rows)

postgres=# \q
postgres@acid-minimal-cluster-0:~$ 

```



**2.访问数据库**

```shell
# 访问地址


# 主
172.51.216.81
5432:31162
postgres
postgres

# 从
172.51.216.81
5432:31363
postgres
postgres
```





### 2.TimescaleDB

#### 2.1.安装方式

```shell
# timescale/timescaledb-kubernetes
https://github.com/timescale/timescaledb-kubernetes


# 安装
# TimescaleDB Single
https://github.com/timescale/timescaledb-kubernetes/tree/master/charts/timescaledb-single


# 官方
# TimescaleDB Multinode
https://github.com/timescale/timescaledb-kubernetes/blob/master/charts/timescaledb-multinode

```

```shell
[root@k8s-master timescale]# helm repo add timescale 'https://charts.timescale.com'
"timescale" has been added to your repositories


[root@k8s-master timescale]# helm repo list
NAME         	URL                                                   
aliyun       	https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
bitnami      	https://charts.bitnami.com/bitnami                    
ingress-nginx	https://kubernetes.github.io/ingress-nginx            
gitlab       	https://charts.gitlab.io                              
elastic      	https://helm.elastic.co                               
harbor       	http://172.51.216.85:8888/chartrepo/charts            
chartmuseum  	http://172.51.216.85:9999                             
presslabs    	https://presslabs.github.io/charts                    
timescale    	https://charts.timescale.com


[root@k8s-master timescale]# helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "chartmuseum" chart repository
...Successfully got an update from the "harbor" chart repository
...Successfully got an update from the "elastic" chart repository
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "aliyun" chart repository
...Successfully got an update from the "presslabs" chart repository
...Successfully got an update from the "timescale" chart repository
...Successfully got an update from the "gitlab" chart repository
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈


[root@k8s-master timescale]# helm search repo timescaledb
NAME                           	CHART VERSION	APP VERSION	DESCRIPTION                      
timescale/timescaledb-multinode	0.8.0        	           	TimescaleDB Multinode Deployment.
timescale/timescaledb-single   	0.8.2        	           	TimescaleDB HA Deployment. 
```



| Recipe                                                       | TimescaleDB    | Description                                                  |
| ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ |
| [TimescaleDB Single](https://github.com/timescale/timescaledb-kubernetes/blob/master/charts/timescaledb-single) | Based on 1.x   | TimescaleDB Single allows you to deploy a highly-available TimescaleDB database configuration. |
| [TimescaleDB Multinode](https://github.com/timescale/timescaledb-kubernetes/blob/master/charts/timescaledb-multinode) | In Development | TimescaleDB Multinode allows you to deploy a multi-node, distributed version of TimescaleDB. |



```shell
# 部署单实例

[root@k8s-master timescale]# helm search repo timescaledb
NAME                           	CHART VERSION	APP VERSION	DESCRIPTION                      
timescale/timescaledb-single   	0.8.2        	           	TimescaleDB HA Deployment. 



# TimescaleDB Single
https://github.com/timescale/timescaledb-kubernetes/tree/master/charts/timescaledb-single
```



#### 2.2.下载安装包

```shell
[root@k8s-master timescale]# helm fetch timescale/timescaledb-single

[root@k8s-master timescale]# ll
total 56
-rw-r--r-- 1 root root 55788 Dec  7 16:53 timescaledb-single-0.8.2.tgz
[root@k8s-master timescale]# tar -zxf timescaledb-single-0.8.2.tgz 
[root@k8s-master timescale]# ll
total 56
drwxr-xr-x 5 root root   257 Dec  7 16:53 timescaledb-single
-rw-r--r-- 1 root root 55788 Dec  7 16:53 timescaledb-single-0.8.2.tgz
[root@k8s-master timescale]# cd timescaledb-single
[root@k8s-master timescaledb-single]# ll
total 120
-rw-r--r-- 1 root root 44242 Jan 13  2021 admin-guide.md
-rw-r--r-- 1 root root   370 Jan 13  2021 Chart.yaml
-rw-r--r-- 1 root root  4432 Jan 13  2021 generate_kustomization.sh
drwxr-xr-x 4 root root    55 Dec  7 16:53 kustomize
-rw-r--r-- 1 root root  6875 Jan 13  2021 README.md
-rw-r--r-- 1 root root   188 Jan 13  2021 requirements.yaml
drwxr-xr-x 2 root root  4096 Dec  7 16:53 templates
-rw-r--r-- 1 root root  5693 Jan 13  2021 upgrade-guide.md
drwxr-xr-x 2 root root   141 Dec  7 16:53 values
-rw-r--r-- 1 root root 13355 Jan 13  2021 values.schema.yaml
-rw-r--r-- 1 root root 21571 Jan 13  2021 values.yaml
```



#### 2.3.修改配置

```shell
# values.yaml


persistentVolumes:
  # For sanity reasons, the actual PGDATA and wal directory will be subdirectories of the Volume mounts,
  # this allows Patroni/a human/an automated operator to move directories during bootstrap, which cannot
  # be done if we did not use subdirectories
  # https://www.postgresql.org/docs/current/creating-cluster.html#CREATING-CLUSTER-MOUNT-POINTS
  data:
    enabled: true
    size: 20Gi
    ## database data Persistent Volume Storage Class
    ## If defined, storageClassName: <storageClass>
    ## If set to "-", storageClassName: "", which disables dynamic provisioning
    ## If undefined (the default) or set to null, no storageClassName spec is
    ##   set, choosing the default provisioner.  (gp2 on AWS, standard on
    ##   GKE, AWS & OpenStack)
    ##
    storageClass: "rook-ceph-block"
    subPath: ""
    mountPath: "/var/lib/postgresql"
    annotations: {}
    accessModes:
      - ReadWriteOnce
  # WAL will be a subdirectory of the data volume, which means enabling a separate
  # volume for the WAL files should just work for new pods.
  wal:
    enabled: true
    size: 1Gi
    subPath: ""
    storageClass: "rook-ceph-block"
    # When changing this mountPath ensure you also change the following key to reflect this:
    # patroni.postgresql.basebackup.[].waldir
    mountPath: "/var/lib/postgresql/wal"
    annotations: {}
    accessModes:
      - ReadWriteOnce
  # Any tablespace mentioned here requires a volume that will be associated with it.
  # tablespaces:
    # example1:
    #   size: 5Gi
    #   storageClass: gp2
    # example2:
    #   size: 5Gi
    #   storageClass: gp2



# 修改存储
# 修改内容
storageClass: "rook-ceph-block"
size: 20Gi
```



#### 2.4.安装

```shell
# 安装证书

[root@k8s-master timescaledb-single]# pwd
/k8s/middleware/timescale/timescaledb-single
[root@k8s-master timescaledb-single]# ll
total 120
-rw-r--r-- 1 root root 44051 Jan 13  2021 admin-guide.md
-rw-r--r-- 1 root root   370 Jan 13  2021 Chart.yaml

# 证书相关文件
-rw-r--r-- 1 root root  4432 Jan 13  2021 generate_kustomization.sh
drwxr-xr-x 4 root root    55 Dec  8 10:07 kustomize


-rw-r--r-- 1 root root  6875 Jan 13  2021 README.md
-rw-r--r-- 1 root root   188 Jan 13  2021 requirements.yaml
drwxr-xr-x 2 root root  4096 Dec  8 10:28 templates
-rw-r--r-- 1 root root  5693 Jan 13  2021 upgrade-guide.md
drwxr-xr-x 2 root root   141 Dec  8 10:07 values
-rw-r--r-- 1 root root 13287 Jan 13  2021 values.schema.yaml
-rw-r--r-- 1 root root 21428 Dec  8 11:03 values.yaml



[root@k8s-master timescaledb-single]# chmod +x generate_kustomization.sh 


./generate_kustomization.sh tsdb
[root@k8s-master timescaledb-single]# ./generate_kustomization.sh tsdb
Generating a 4096 bit RSA private key
..........................++
.......................................................................................................................++
writing new private key to './kustomize/tsdb/tls.key'
-----
Do you want to configure the backup of your database to S3 (compatible) storage? (y/n)
n
./generate_kustomization.sh: line 62: read: `-r': not a valid identifier

Generated a kustomization named tsdb in directory ./kustomize/tsdb.


WARNING: The generated certificate in this directory is self-signed and is only
         fit for development and demonstration purposes.
         The certificate should be replaced by a signed certificate, signed by
         a Certificate Authority (CA) that you trust.


You may now wish to (p)review the files that have been created and further edit
them before deployment.


To preview the deployment of the secrets:

    kubectl kustomize "./kustomize/tsdb"

Or you may want to install the secrets directly? (y/n)
y
Installing secrets...
secret/tsdb-certificate created
secret/tsdb-credentials created
secret/tsdb-pgbackrest created


# 查看
[root@k8s-master timescaledb-single]# cd kustomize/
[root@k8s-master kustomize]# ll
total 0
drwxr-xr-x 2 root root 109 Dec  8 11:00 example
drwxr-xr-x 2 root root 109 Dec  8 10:07 example2
drwxr-xr-x 2 root root 109 Dec  8 11:12 tsdb
[root@k8s-master kustomize]# cd tsdb/
[root@k8s-master tsdb]# ll
total 16
-rw-r--r-- 1 root root  178 Dec  8 11:12 credentials.conf
-rw-r--r-- 1 root root  564 Dec  8 11:12 kustomization.yaml
-rw-r--r-- 1 root root    0 Dec  8 11:12 pgbackrest.conf
-rw-r--r-- 1 root root 1773 Dec  8 11:12 tls.crt
-rw-r--r-- 1 root root 3272 Dec  8 11:12 tls.key



[root@k8s-master tsdb]# kubectl kustomize ./

apiVersion: v1
data:
  tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUU4VENDQXRtZ0F3SUJBZ0lKQU5KOUs5TUZ1aUlHTUEwR0NTcUdTSWIzRFFFQkN3VUFNQTh4RFRBTEJnTlYKQkFNTUJIUnpaR0l3SGhjTk1qRXhNakl5TURjeU5qUTFXaGNOTWpFeE1qSXpNRGN5TmpRMVdqQVBNUTB3Q3dZRApWUVFEREFSMGMyUmlNSUlDSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQWc4QU1JSUNDZ0tDQWdFQXlHSE1ncDFXCjNPMUNsMlJzeWRsa0haT2ltd1VVdXJZNkVHQUxrTnhpZDIwNEJNTDExNU94NHhOOTVEbnVOU0tnWE1RTXh6T2wKSSthSktsNjFuWHpaMythMDdGc0pQc1gzY3RhMllUNWpNME81VDFZSjJXT083eUJFSjY3YXhOS0QrRVZFV3hpUApIQTl5VVJja2VTc09abjJXbktBVjdBdFB5NkptZkVMdmNqcGdRaW53b0ZlbmM0SHgra0t6b1NBRUVLRktzRzFPCkxBMVlPdGQ1eC9rbXllOE9rWU5iNXFBeW9WV1N1c0Fnb3pnclpMcVdnT0VzOXY3dkdoL2xOWDh6ZjJSVXA0bUcKVzNZdkx3T3UzYWV6WElJSjBTR2ZmMEVUWVJQREg0a1h4UWRVTmhxanZtdUFtVks4cVNpZWI1bmlvYnNXNCsxawpWemF1bHg5RjZCQU9UZC80MjZ6ejQzSWkwTkZDU1ozYmZmNXg5Rk5qYW5wREw1bW5RNHJrNkpBNmJ3WnZHajZQCkdKK0dVQjJLb2F5cEo0OTBkQ3B4UzNVS3poYlE0ZlJMRE9MSS9ZZzM1Mm42K3BOcDZuTHZYay9pNVM0K0tRa0kKeHE3dGF5NDR6ZVVpMHQveml5dmlBQUJSMW52b0NpZzVScnlhOEJwYzFaOXFIMkZuTTAzcW4zSDNNdDVSZU81awpncUFKWHEzUTlHeTJnY2prUmVoVEFlRXZTcmE4dUt6cWQ3QnlVUHEzZlh4WkNSU0VRRDhvNGp2QVk5Q1BnZHRQClRIK0YwL2NhaUpDejVLcGRxV0JjTU1zTm1wQyt6UzRSUnVWUk43a2FRUHZyNElNelgxZHVOL2FkUkhudjBkdlUKQktpYUxuRjJBdGx6RGxBK25rWG54VXNOTzh2b1dwTmt1MU1DQXdFQUFhTlFNRTR3SFFZRFZSME9CQllFRk82SAo1dEFrbGtmNUE5eEwvOHEvZjdIclRJNllNQjhHQTFVZEl3UVlNQmFBRk82SDV0QWtsa2Y1QTl4TC84cS9mN0hyClRJNllNQXdHQTFVZEV3UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dJQkFLVGJUSG5wUXkzZ2lJL1UKbXh2Kzg5c0FuVkNFMC92cHozWDhaNzZBZjQyT2FKYU5YNy9ZSUNtVGlCaWI0dTg1ZisyUU9vbGVjNTJhNkEwQgozcGh3cU1MeGVyYmRwUUxYK045WFNGUDRKZ3N6UGlMM3I2Q2tNQ3M5ZWdzM0FlaFY3alBydlJ1K0R6ZWhzK1hyCk03WmczYTU0MnkzdkdNSGNIZERLM2JHQTIxZUZJeVpiRmtGNnoreFVKNk85UWcwNTJ1OEozQkRkUXJjSHdWeTkKNE11aFVZQS9PTHhSK0Jqbm5BM2dQTXRhbGtMQ2xMSEN5cjBQdDYwc0xJUGs1S2pOQVZndzFWUzJoYVduVmhMLwpsNnFTQThLNGVnWjF6RFhiUTFRTThha0dXNEVOVnhTRkxOelQ2T3FLMVFXRzZmYU9sdE9ZMnlrOUZFRWx3YzhWClBCY2g3QURQUVd6UExKNUhzMVNKbVZ4SVViNGJNK0lWUWdOWVR6Z0dOSE1mVHZ4MmxsdFJBaG1zUGJkTjcvdkUKMkx2Q3FZWEVrd1FCd0tZQXc1YXRLSk9USGlETkZYMWd2bWErNERhVUwxSzZ2YlAyaDgwYy8vRWJNSkY1Si9hWAp5K01FaXgya210VFhGVkdxUjhhSWlxMUUyRmNwVXFrYmg0bEcvMlJLclJJUms5SFpZWHFncStXUnQ4d1ZxTFRyCnNZKzAyUENPR0ROZDh6ZTB2VjN4RlF5YXhnY2w0Y0JPZzhuckVOeHhNS3ZBYnFiK1dnQWNId2MwUG41MkdGZHMKZ1FEdWw4ZCtLeGZOSUhzVlFER3V5UzVRNWhaVzF5ZDd4RkVoN3B2VUZ1b0N5MU5VdmRyQVRYRGwrYmZvOFVXVAp5THR2cEdJb3NGcE9uRVpGeXl2Zm9KazVzNWhNCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  tls.key: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCk1JSUpRZ0lCQURBTkJna3Foa2lHOXcwQkFRRUZBQVNDQ1N3d2dna29BZ0VBQW9JQ0FRRElZY3lDblZiYzdVS1gKWkd6SjJXUWRrNktiQlJTNnRqb1FZQXVRM0dKM2JUZ0V3dlhYazdIakUzM2tPZTQxSXFCY3hBekhNNlVqNW9rcQpYcldkZk5uZjVyVHNXd2sreGZkeTFyWmhQbU16UTdsUFZnblpZNDd2SUVRbnJ0ckUwb1A0UlVSYkdJOGNEM0pSCkZ5UjVLdzVtZlphY29CWHNDMC9Mb21aOFF1OXlPbUJDS2ZDZ1Y2ZHpnZkg2UXJPaElBUVFvVXF3YlU0c0RWZzYKMTNuSCtTYko3dzZSZzF2bW9ES2hWWks2d0NDak9DdGt1cGFBNFN6Mi91OGFIK1UxZnpOL1pGU25pWVpiZGk4dgpBNjdkcDdOY2dnblJJWjkvUVJOaEU4TWZpUmZGQjFRMkdxTythNENaVXJ5cEtKNXZtZUtodXhiajdXUlhOcTZYCkgwWG9FQTVOMy9qYnJQUGpjaUxRMFVKSm5kdDkvbkgwVTJOcWVrTXZtYWREaXVUb2tEcHZCbThhUG84WW40WlEKSFlxaHJLa25qM1IwS25GTGRRck9GdERoOUVzTTRzajlpRGZuYWZyNmsybnFjdTllVCtMbExqNHBDUWpHcnUxcgpMampONVNMUzMvT0xLK0lBQUZIV2UrZ0tLRGxHdkpyd0dselZuMm9mWVdjelRlcWZjZmN5M2xGNDdtU0NvQWxlCnJkRDBiTGFCeU9SRjZGTUI0UzlLdHJ5NHJPcDNzSEpRK3JkOWZGa0pGSVJBUHlqaU84QmowSStCMjA5TWY0WFQKOXhxSWtMUGtxbDJwWUZ3d3l3MmFrTDdOTGhGRzVWRTN1UnBBKyt2Z2d6TmZWMjQzOXAxRWVlL1IyOVFFcUpvdQpjWFlDMlhNT1VENmVSZWZGU3cwN3kraGFrMlM3VXdJREFRQUJBb0lDQUEzU3VWSDFXcTJvN0dRWE9HNEFRaWpNCkszWjROa0xmR1VoUjU5cFphYTJGYWt6aHlpWFIrWDZKdExDTzBvRDEzNHdtdGg3ejBCdVc1clYyalI3Tkl4YVEKQ3NFWFVwN3k5eXdENWRiMWY5QmtocDhUZDJCNHZyNStRbFZlZVpjRVVyaEl4dnRseVZHTk96eWUxUlJLeFJhSwo2VjNxcVRoOFcwZlg3eXY1VGgxYUs1UEU0dVdjeGw5d2dtbmFPaHdPWWxsblZ3aXVzYXJXVE5UYVVudGFFN1B3CmV0Zk04UVVLM2hORkhQY25FOWxPb2FlME8zZXVrUFNGQjZlTXRib29DVHhyaG05OFREbDVBSzVFbWNhT3NBL2MKcEtLNXFCQVdSQ2o1UFFlcTVHbmlKSXdLOEdyTmJiU21BWC9GM3BBaVZJRUZzQUdQT2RIT1l1TG45R0dhNGZHYwpGdjloMmpqWE45Z3M5QjRlZFRDUDNybFJpYWNwQjRVV1pxU1BaOXZCUUs0VytMVUpmeW5ZQWtXcVBwbm4yVjJxClVhYkorM25BRGFMd3VodTc1YmdvMzVNdmpMRnYydGhGZXh6TGQ5MnNhdjlEcUM0L3hzdEpPb1JReGNpTFZIVXIKTW5oTzNOTmVlSXFJWEw4Nmw1ZUdlL0t5a1lGTXd0dGZUeTBHU3dDR2tkcmJpSFF5SU0wUDZtSFBHaTQ4eU40bgpJNU9SRmlRUm96N2ZMeWhhWkVUNktGYTd1Z016ZlRkazkzWW1zYXd5RXNjcWpxQVlVNkFVMWNKN3Z0TjR2RVBRCjlRZWRMWTRRSVQ1Z0plb29vaGdYTjdsM2ZZblZ0UW5ZbGVPUjZjdHk4MVoyZUFpL3FJeC91T0NXYjloWC9KVTYKcGRiZnMrTlRIZDhPeThJS1JIZVpBb0lCQVFEamNzWk40TnU5aGJHYkRkMzlQREZNMjFtRGlCc3c1RkpCdTVmMwp4dG9ud1cvRUVFdHFzeTMzcnd6NVdRNzNyQ3N2Qkg1cUJtZW1iUHk5WHM1dlFYQms5c1IvU3h3UXFIdDYyWG0vCjdtMEJGZUM0cVJaMm9JQmkrWVpSK2FTWU43RzlpazZnMTU2TUZ6Q1duK1ljakNGMDlDK1FGNm1wWjdIbGxqcnUKRm00eHF6WUMyWGxEVjNxZ2IybkpzMTd3V0M1TjNnMVJ0UHVIaEdQYjdjY1RxRzNEMjB1NktrcW1TNStLdGF4SgpqbFdzdENoRlcrWlNJL3Blb1NLWlVIUmxlSkZUME5CQk5qd1lkYzZJMlF3eHpybDNReFdoMzNTNndOdi9WNGZDCkExZ1BlYlVKWXBsanJaSGxiZ3JpSjREWEJCS0FIUVMwNnI4UGV5clZMNlFLSUU5M0FvSUJBUURoaVRwTWxKcksKbWhrNGtQOWltYzVOMnlxZnE3U2JKNWVrbXduM0Y4TUl6eHFTY3ZjKyswUnoybGFseXRqejRnRGVMc2RsazU0OQorYW5PQ0hINElUaDFkS3p3UGxXbTRjNW9uQmJjRTJUclZIdEhBdng5bjZaakxoTEVPM1o1TXVhM296RUhqUEdwCkFpTHRwWHM2ZFYwYmc1UHlnVE9JbWcrWE1Rc1hMWTVzUmNxYitaSThoUkNRbDRtUFdWdzlnbGZJbllucm9KN28KMm42SEhaQ2dUUURJandycE1iSi9EYVUxMXp3Z01nYTJaMFY5bjV4R1Q3VVZFVm9GeHZPL2J1K2NDWTlUK0RDQwoxMDBjNDRjeWZmZWoyK3pMZHpLc3RKV05WSUFjRzFwbGV3V2dVSkF5eEtZTWlXSFVRRUE0b0tHWXpoc0pSY2tUCjdCYnRuWXJnamNJRkFvSUJBRFJxVzliUXJmTWtIMFRyVWpBc3NmUFRUUEtwNkJKQlc4OTRLdEpZQ2loRlJMdDcKUWRZS0N0cmNoWEhsR3pUcWdWMHBmUFIwRzJqWUR2cVpJWnUwQ2ZIS2lJZ0pTQ055b0ZvMFNnRjRNYmloVVJOZApMQ2NVWCtIdlBRd2hLdFJGYVhtVHFRRWFENWliTTRCU3d4WHJHVDY1azBoeW00L0ZyTktLNTNPOHlaSTZzWXpBCmoxaDhqVzd4bmdCMGpMbDRxTnNiQkJqRFMzLzBlNHJRWmlOYW1ra2JmWDBlaCt1QTIvaDhXNExzQVVSMmxCMC8KeTNrOGYxTlZjUUxCN3NEL293WWN4aEZ4TFRJNTIrbmZreGJiWEJSbTZsSk9pN2tKL3VqK1EvUHJEMTBwb0JYVQptaUxGZWl6VVNqL0orTUFVV1NzYkJOMm9oM1ZLM2hrWkRJV2s0b3NDZ2dFQkFMWlZVZnVOZkdMbEdCVENMS1dUClFOVnlwVS8yNmdreGhnZytpMXpuS2ZjYU1CcEx0WldHWC8zbGUzMkhzOFBmWitJNElWMytiTVVmN1dhekx5aHgKK3dvQ0xMb0JPdyt5cUVPc1JWTGdud3NkL3BnWFV2ZGd0WXlqTityTFErbVIvREprVFlRVUwxNzZhakNFUTA2cwppWHh2OEpEeVlTNURsdTBkYWlEdjVKK21BTG4rbDNvei9ZTlg3NDhqcUUzVjdaQXp4TWZveisvaWpMNUJhYVllCitzNHB6cUZlV3pjYVdnRmdJNnpIcE9PY00vTHVzZEdxS1BTQ1Zhd3IvdTA2QzU2em45czc0RVEzT1pGc1pPV3UKTHlHYThDSkNHSWJGYTg2WmpRU3NISFhFY25UOERNZnVjV3ZiT1dyMkVyVjFMNCt3dU96VExVL2M0MkJ3cUZFSQphZDBDZ2dFQU9kL093YTBFczdYcWdMK2hvSEE4SG5kR1Y5c2JibS9LQzFNdWIyVnNtRlJCTFV5T0VZY1BtbVN4Ck03VUdKR1NSVlNxZlBLWS9oS25TbXJrM2o2a21kM1pUZ0JWRkVHTU9laTZ0L0pSbXhnaFpXQW42Y3NnNDlsd2oKOGt1b3g0alNCdCt0TXZMbzB3NDdGRjFnaXNMZWV3QTVTMmlpUzlwUEJaTmI0UGNTV09IOUlqanA2Vm10TUdVUgpOekg0QUdYb3lQUjN2L0FEeng1TzJzWVdEVmJvdHBrY3E2bHFFZVNhdUxnVmxjQmU5dElBaTlmODd6VmFCYmk2Cjlwa1RaU1FlcTlGeStIZDkwSW41dkp3TkxkWHRIRHJ2a3hqeEwxd2xEei9tVjdoL1NEMXZ1dk9CWWwwdGI0c3AKZldyZUZud1BaN09aSGh2VWM4NUpIblZtaDI0Tmd3PT0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQo=
kind: Secret
metadata:
  labels:
    app: tsdb-timescaledb
    cluster-name: tsdb
  name: tsdb-certificate
type: kubernetes.io/tls
---
apiVersion: v1
data:
  PATRONI_REPLICATION_PASSWORD: RjhoZFhFQ1pNWG5WSktwVkRZUHFzWnJab3NjOUx5MGc=
  PATRONI_SUPERUSER_PASSWORD: TE1CVHhZVG9IeEkyZThZNVFkaFlaaDM2cXpTSUMyelg=
  PATRONI_admin_PASSWORD: QzBEbzE5NUFnU1BIYVNrOUVoMTRpWHlZaG5JOG5wQmo=
kind: Secret
metadata:
  labels:
    app: tsdb-timescaledb
    cluster-name: tsdb
  name: tsdb-credentials
type: Opaque
---
apiVersion: v1
kind: Secret
metadata:
  labels:
    app: tsdb-timescaledb
    cluster-name: tsdb
  name: tsdb-pgbackrest
type: Opaque


# 上述内容保存为文件：timescaledbMap.yaml


[root@k8s-master tsdb]# kubectl apply -f timescaledbMap.yaml
secret/tsdb-certificate unchanged
secret/tsdb-credentials unchanged
secret/tsdb-pgbackrest unchanged
```



```shell
[root@k8s-master1 timescale]# helm install tsdb  timescaledb-single
NAME: tsdb
LAST DEPLOYED: Wed Dec 22 15:32:56 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
TimescaleDB can be accessed via port 5432 on the following DNS name from within your cluster:
tsdb.default.svc.cluster.local

To get your password for superuser run:

    # superuser password
    PGPASSWORD_POSTGRES=$(kubectl get secret --namespace default tsdb-credentials -o jsonpath="{.data.PATRONI_SUPERUSER_PASSWORD}" | base64 --decode)

    # admin password
    PGPASSWORD_ADMIN=$(kubectl get secret --namespace default tsdb-credentials -o jsonpath="{.data.PATRONI_admin_PASSWORD}" | base64 --decode)

To connect to your database, chose one of these options:

1. Run a postgres pod and connect using the psql cli:
    # login as superuser
    kubectl run -i --tty --rm psql --image=postgres \
      --env "PGPASSWORD=$PGPASSWORD_POSTGRES" \
      --command -- psql -U postgres \
      -h tsdb.default.svc.cluster.local postgres

    # login as admin
    kubectl run -i --tty --rm psql --image=postgres \
      --env "PGPASSWORD=$PGPASSWORD_ADMIN" \
      --command -- psql -U admin \
      -h tsdb.default.svc.cluster.local postgres

2. Directly execute a psql session on the master node

   MASTERPOD="$(kubectl get pod -o name --namespace default -l release=tsdb,role=master)"
   kubectl exec -i --tty --namespace default ${MASTERPOD} -- psql -U postgres



[root@k8s-master timescale]# helm list
NAME	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART                   	APP VERSION
tsdb	default  	1       	2021-12-08 11:24:45.002796972 +0800 CST	deployed	timescaledb-single-0.8.2


[root@k8s-master timescale]# kubectl get all
NAME                     READY   STATUS    RESTARTS   AGE
pod/tsdb-timescaledb-0   1/1     Running   0          2m30s
pod/tsdb-timescaledb-1   1/1     Running   0          2m30s
pod/tsdb-timescaledb-2   1/1     Running   0          2m30s

NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1        <none>        443/TCP          112d
service/tsdb           LoadBalancer   10.99.100.99     <pending>     5432:30134/TCP   2m30s
service/tsdb-config    ClusterIP      None             <none>        8008/TCP         2m30s
service/tsdb-replica   ClusterIP      10.100.214.105   <none>        5432/TCP         2m30s

NAME                                READY   AGE
statefulset.apps/tsdb-timescaledb   3/3     2m30s


[root@k8s-master timescale]# kubectl get pvc
NAME                                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
storage-volume-tsdb-timescaledb-0   Bound    pvc-fd3d36f0-922f-462b-9d21-d40822c4aa75   20Gi       RWO            rook-ceph-block   2m37s
storage-volume-tsdb-timescaledb-1   Bound    pvc-ca97fa0b-c56e-4b90-84c4-8686ab1dd0f4   20Gi       RWO            rook-ceph-block   2m37s
storage-volume-tsdb-timescaledb-2   Bound    pvc-f8764025-5d07-4040-a14c-60a7fb621ce9   20Gi       RWO            rook-ceph-block   2m37s
wal-volume-tsdb-timescaledb-0       Bound    pvc-0277a264-13d7-43e9-93d5-2c0c3c062b0d   1Gi        RWO            rook-ceph-block   2m37s
wal-volume-tsdb-timescaledb-1       Bound    pvc-ee810352-cdd0-4e4d-bf6c-6eeccf4bcf6a   1Gi        RWO            rook-ceph-block   2m37s
wal-volume-tsdb-timescaledb-2       Bound    pvc-5296b42d-8fc1-417d-a7a6-427fbee632d7   1Gi        RWO            rook-ceph-block   2m37s
```

```shell
# 查看
[root@k8s-master helm]# kubectl get all
NAME                     READY   STATUS    RESTARTS   AGE
pod/my-pg-postgresql-0   1/1     Running   0          53s

NAME                                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP    111d
service/my-pg-postgresql            ClusterIP   10.110.139.14   <none>        5432/TCP   54s
service/my-pg-postgresql-headless   ClusterIP   None            <none>        5432/TCP   54s

NAME                                READY   AGE
statefulset.apps/my-pg-postgresql   1/1     54s


[root@k8s-master1 timescale]# kubectl get all | grep tsdb
pod/tsdb-timescaledb-0                        1/1     Running   0          10m
pod/tsdb-timescaledb-1                        1/1     Running   0          6m5s
pod/tsdb-timescaledb-2                        1/1     Running   0          73s
service/tsdb                                LoadBalancer   10.1.226.117   <pending>     5432:30508/TCP                   10m
service/tsdb-config                         ClusterIP      None           <none>        8008/TCP                         10m
service/tsdb-replica                        ClusterIP      10.1.102.69    <none>        5432/TCP                         10m
statefulset.apps/tsdb-timescaledb                        3/3     10m
```



#### 2.5.测试

```shell
# 测试数据库


# superuser password
export PGPASSWORD_POSTGRES=$(kubectl get secret --namespace default tsdb-credentials -o jsonpath="{.data.PATRONI_SUPERUSER_PASSWORD}" | base64 --decode)

    # admin password
export PGPASSWORD_ADMIN=$(kubectl get secret --namespace default tsdb-credentials -o jsonpath="{.data.PATRONI_admin_PASSWORD}" | base64 --decode)



# 1.获取密码（用户postgres）
[root@k8s-master ~]# export PGPASSWORD_POSTGRES=$(kubectl get secret --namespace default tsdb-credentials -o jsonpath="{.data.PATRONI_SUPERUSER_PASSWORD}" | base64 --decode)

[root@k8s-master ~]#  echo $PGPASSWORD_POSTGRES
LMBTxYToHxI2e8Y5QdhYZh36qzSIC2zX

# 获取密码（用户admin）
[root@k8s-master ~]# export PGPASSWORD_ADMIN=$(kubectl get secret --namespace default tsdb-credentials -o jsonpath="{.data.PATRONI_admin_PASSWORD}" | base64 --decode)

[root@k8s-master ~]# echo $PGPASSWORD_ADMIN
C0Do195AgSPHaSk9Eh14iXyYhnI8npBj



# 2.数据库连接信息
tsdb.default.svc.cluster.local
5432:30508
postgres
LMBTxYToHxI2e8Y5QdhYZh36qzSIC2zX



# 3.连接数据库
kubectl run -i --tty --rm psql --image=postgres \
--env "PGPASSWORD=$PGPASSWORD_POSTGRES" \
--command -- psql -U postgres \
-h tsdb.default.svc.cluster.local postgres



[root@k8s-master pro]# kubectl run -i --tty --rm psql --image=postgres \
> --env "PGPASSWORD=$PGPASSWORD_POSTGRES" \
> --command -- psql -U postgres \
> -h tsdb.default.svc.cluster.local postgres
If you don't see a command prompt, try pressing enter.
postgres=# 



[root@k8s-master pro]# kubectl run -i --tty --rm psql --image=postgres \
> --env "PGPASSWORD=$PGPASSWORD_POSTGRES" \
> --command -- psql -U postgres \
> -h tsdb.default.svc.cluster.local postgres
If you don't see a command prompt, try pressing enter.
postgres=# \d
Did not find any relations.
postgres=# \du
                                   List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 admin     | Create role, Create DB                                     | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 standby   | Replication                                                | {}

postgres=# \l
                              List of databases
   Name    |  Owner   | Encoding | Collate |  Ctype  |   Access privileges   
-----------+----------+----------+---------+---------+-----------------------
 postgres  | postgres | UTF8     | C.UTF-8 | C.UTF-8 | 
 template0 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
 template1 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
(3 rows)

# 修改密码
postgres=# ALTER USER postgres with encrypted password 'postgres';
ALTER ROLE
postgres=# 
postgres=# 
postgres=# \q
Session ended, resume using 'kubectl attach psql -c psql -i -t' command when the pod is running
pod "psql" deleted



[root@k8s-master ~]# kubectl get pod
NAME                 READY   STATUS    RESTARTS   AGE
psql                 1/1     Running   0          19m
tsdb-timescaledb-0   1/1     Running   0          30m
tsdb-timescaledb-1   1/1     Running   0          30m
tsdb-timescaledb-2   1/1     Running   0          30m
```

```shell
# Pod
# 区分主从：role=master； role=replica；

[root@k8s-master ~]# kubectl get pod --show-labels
NAME                 READY   STATUS    RESTARTS   AGE   LABELS
# 从
tsdb-timescaledb-0   1/1     Running   0          40m   app=tsdb-timescaledb,cluster-name=tsdb,controller-revision-hash=tsdb-timescaledb-56fdb9b9cf,release=tsdb,role=replica,statefulset.kubernetes.io/pod-name=tsdb-timescaledb-0

# 从
tsdb-timescaledb-1   1/1     Running   0          40m   app=tsdb-timescaledb,cluster-name=tsdb,controller-revision-hash=tsdb-timescaledb-56fdb9b9cf,release=tsdb,role=replica,statefulset.kubernetes.io/pod-name=tsdb-timescaledb-1

# 主
tsdb-timescaledb-2   1/1     Running   0          40m   app=tsdb-timescaledb,cluster-name=tsdb,controller-revision-hash=tsdb-timescaledb-56fdb9b9cf,release=tsdb,role=master,statefulset.kubernetes.io/pod-name=tsdb-timescaledb-2
```

```shell
# 查看service


[root@k8s-master ~]# kubectl get pod -o wide
NAME                 READY   STATUS    RESTARTS   AGE   IP               NODE        NOMINATED NODE   READINESS GATES
psql                 1/1     Running   0          32m   10.244.36.121    k8s-node1   <none>           <none>
tsdb-timescaledb-0   1/1     Running   0          43m   10.244.36.80     k8s-node1   <none>           <none>
tsdb-timescaledb-1   1/1     Running   0          43m   10.244.169.133   k8s-node2   <none>           <none>
tsdb-timescaledb-2   1/1     Running   0          43m   10.244.107.208   k8s-node3   <none>           <none>


[root@k8s-master ~]# kubectl get svc
NAME           TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
tsdb           LoadBalancer   10.99.100.99     <pending>     5432:30134/TCP   43m
tsdb-config    ClusterIP      None             <none>        8008/TCP         43m
tsdb-replica   ClusterIP      10.100.214.105   <none>        5432/TCP         43m


# 主
[root@k8s-master ~]# kubectl describe svc tsdb
Name:                     tsdb
Namespace:                default
Labels:                   app=tsdb-timescaledb
                          app.kubernetes.io/managed-by=Helm
                          chart=timescaledb-single-0.8.1
                          cluster-name=tsdb
                          heritage=Helm
                          release=tsdb
                          
                          
                          role=master
                          
                          
Annotations:              meta.helm.sh/release-name: tsdb
                          meta.helm.sh/release-namespace: default
                          service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout: 4000
Selector:                 <none>
Type:                     LoadBalancer
IP Families:              <none>
IP:                       10.99.100.99
IPs:                      10.99.100.99
Port:                     postgresql  5432/TCP
TargetPort:               postgresql/TCP
NodePort:                 postgresql  30134/TCP
Endpoints:                10.244.107.208:5432
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>


# 从
[root@k8s-master ~]# kubectl describe svc tsdb-replica
Name:              tsdb-replica
Namespace:         default
Labels:            app=tsdb-timescaledb
                   app.kubernetes.io/managed-by=Helm
                   chart=timescaledb-single-0.8.1
                   cluster-name=tsdb
                   component=postgres
                   heritage=Helm
                   release=tsdb
                   
                   
                   role=replica
                   
                   
Annotations:       meta.helm.sh/release-name: tsdb
                   meta.helm.sh/release-namespace: default
                   service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout: 4000
Selector:          app=tsdb-timescaledb,cluster-name=tsdb,role=replica
Type:              ClusterIP
IP Families:       <none>
IP:                10.100.214.105
IPs:               10.100.214.105
Port:              postgresql  5432/TCP
TargetPort:        postgresql/TCP
Endpoints:         10.244.169.133:5432,10.244.36.80:5432
Session Affinity:  None
Events:            <none>
```



#### 2.6.外部访问数据库

```shell
# 查看
[root@k8s-master1 rabbitmq]# kubectl get svc
NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                          AGE
tsdb                                LoadBalancer   10.1.226.117   <pending>     5432:30508/TCP                   21m
tsdb-config                         ClusterIP      None           <none>        8008/TCP                         21m
tsdb-replica                        ClusterIP      10.1.102.69    <none>        5432/TCP                         21m



# 修改从service类型
[root@k8s-master ~]# kubectl edit svc tsdb-replica
service/tsdb-replica edited

spec:
......
  type: NodePort


[root@k8s-master1 rabbitmq]# kubectl get svc
NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                          AGE
tsdb                                LoadBalancer   10.111.84.227    <pending>     5432:31353/TCP                   34m
tsdb-config                         ClusterIP      None             <none>        8008/TCP                         34m
tsdb-replica                        NodePort       10.102.158.86    <none>        5432:30634/TCP                   34m



# 外部访问信息
# 主
172.51.216.81
tsdb.default.svc.cluster.local
5432:31353
postgres
postgres


# 从
172.51.216.81
tsdb-replica.default.svc.cluster.local
5432:30634
postgres
postgres
```

![](md\middleware\timescale\timescale-1.png)

![](md\middleware\timescale\timescale-2.png)





### 3.Redis

#### 3.1.安装方式

```shell
# 安装

#Redis Cluster #2	ucloud/redis-cluster-operator
https://github.com/ucloud/redis-cluster-operator
```



**下载redis-cluster-operator**

```shell
# git  clone  https://github.com/ucloud/redis-cluster-operator.git


[root@k8s-master1 redis]# git  clone  https://github.com/ucloud/redis-cluster-operator.git
Cloning into 'redis-cluster-operator'...
remote: Enumerating objects: 2024, done.
remote: Counting objects: 100% (51/51), done.
remote: Compressing objects: 100% (30/30), done.
remote: Total 2024 (delta 15), reused 38 (delta 11), pack-reused 1973
Receiving objects: 100% (2024/2024), 944.24 KiB | 0 bytes/s, done.
Resolving deltas: 100% (1288/1288), done.
```



#### 3.2.创建Operator

**1.创建自定义资源（CRD）**

```bash
$ kubectl create -f deploy/crds/redis.kun_distributedredisclusters_crd.yaml
$ kubectl create -f deploy/crds/redis.kun_redisclusterbackups_crd.yaml



[root@k8s-master1 redis-cluster-operator]# pwd
/k8s/middleware/redis/redis-cluster-operator
[root@k8s-master1 redis-cluster-operator]# ll
total 144
drwxr-xr-x.  3 root root     35 Dec 22 14:45 build
drwxr-xr-x.  3 root root     36 Dec 22 14:45 charts
drwxr-xr-x.  3 root root     21 Dec 22 14:45 cmd
drwxr-xr-x.  6 root root    108 Dec 22 14:45 deploy
drwxr-xr-x.  3 root root     20 Dec 22 14:45 doc
-rw-r--r--.  1 root root    993 Dec 22 14:45 Dockerfile
-rw-r--r--.  1 root root   2939 Dec 22 14:45 go.mod
-rw-r--r--.  1 root root 107647 Dec 22 14:45 go.sum
drwxr-xr-x.  5 root root     60 Dec 22 14:45 hack
-rw-r--r--.  1 root root  11333 Dec 22 14:45 LICENSE
-rw-r--r--.  1 root root   1797 Dec 22 14:45 Makefile
drwxr-xr-x. 12 root root    148 Dec 22 14:45 pkg
-rw-r--r--.  1 root root   7871 Dec 22 14:45 README.md
drwxr-xr-x.  2 root root     31 Dec 22 14:45 static
drwxr-xr-x.  4 root root     35 Dec 22 14:45 test
-rw-r--r--.  1 root root    149 Dec 22 14:45 tools.go
drwxr-xr-x.  2 root root     24 Dec 22 14:45 version


[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/crds/redis.kun_distributedredisclusters_crd.yaml
Warning: apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
customresourcedefinition.apiextensions.k8s.io/distributedredisclusters.redis.kun created


[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/crds/redis.kun_redisclusterbackups_crd.yaml
Warning: apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
customresourcedefinition.apiextensions.k8s.io/redisclusterbackups.redis.kun created
```



**3.创建operator**

```shell
// cluster-scoped
$ kubectl create -f deploy/service_account.yaml
$ kubectl create -f deploy/cluster/cluster_role.yaml
$ kubectl create -f deploy/cluster/cluster_role_binding.yaml
$ kubectl create -f deploy/cluster/operator.yaml


# 创建
[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/service_account.yaml
serviceaccount/redis-cluster-operator created

[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/cluster/cluster_role.yaml
clusterrole.rbac.authorization.k8s.io/redis-cluster-operator created

[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/cluster/cluster_role_binding.yaml
clusterrolebinding.rbac.authorization.k8s.io/redis-cluster-operator created

[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/cluster/operator.yaml
deployment.apps/redis-cluster-operator created
configmap/redis-admin created


[root@k8s-master redis-cluster-operator]# kubectl get all
NAME                                          READY   STATUS    RESTARTS   AGE
pod/redis-cluster-operator-6669898858-775vj   1/1     Running   0          11s

NAME                                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/kubernetes                       ClusterIP   10.96.0.1        <none>        443/TCP             103d
service/redis-cluster-operator-metrics   ClusterIP   10.100.160.214   <none>        8383/TCP,8686/TCP   1s

NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/redis-cluster-operator   1/1     1            1           11s

NAME                                                DESIRED   CURRENT   READY   AGE
replicaset.apps/redis-cluster-operator-6669898858   1         1         1       11s
```



#### 3.3.持久卷部署

**使用持久性存储方式部署**

**配置：class: rook-ceph-block  # Rook的块存储**

```yaml
# deploy\example\persistent.yaml


[root@k8s-master example]# vim persistent.yaml 

apiVersion: redis.kun/v1alpha1
kind: DistributedRedisCluster
metadata:
  annotations:
    # if your operator run as cluster-scoped, add this annotations
    redis.kun/scope: cluster-scoped
  name: example-distributedrediscluster
spec:
  image: redis:5.0.4-alpine
  masterSize: 3
  clusterReplicas: 1
  storage:
    type: persistent-claim
    size: 2Gi
    class: rook-ceph-block  # Rook的块存储
    deleteClaim: true
```

```shell
$ kubectl create -f deploy/example/persistent.yaml


[root@k8s-master redis-cluster-operator]# kubectl create -f deploy/example/persistent.yaml
distributedrediscluster.redis.kun/example-distributedrediscluster created



[root@k8s-master redis-cluster-operator]# kubectl get distributedrediscluster
NAME                              MASTERSIZE   STATUS    AGE
example-distributedrediscluster   3            Scaling   103s


[root@k8s-master redis-cluster-operator]# kubectl get all -l redis.kun/name=example-distributedrediscluster
NAME                                          READY   STATUS    RESTARTS   AGE
pod/drc-example-distributedrediscluster-0-0   1/1     Running   0          119s
pod/drc-example-distributedrediscluster-0-1   1/1     Running   0          74s
pod/drc-example-distributedrediscluster-1-0   1/1     Running   0          119s
pod/drc-example-distributedrediscluster-1-1   1/1     Running   0          66s
pod/drc-example-distributedrediscluster-2-0   1/1     Running   0          119s
pod/drc-example-distributedrediscluster-2-1   1/1     Running   0          75s

NAME                                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)              AGE
[root@k8s-master1 redis-cluster-operator]# kubectl get all -l redis.kun/name=example-distributedrediscluster
NAME                                          READY   STATUS    RESTARTS   AGE
pod/drc-example-distributedrediscluster-0-0   1/1     Running   0          3m55s
pod/drc-example-distributedrediscluster-0-1   1/1     Running   0          2m37s
pod/drc-example-distributedrediscluster-1-0   1/1     Running   0          3m55s
pod/drc-example-distributedrediscluster-1-1   1/1     Running   0          2m39s
pod/drc-example-distributedrediscluster-2-0   1/1     Running   0          3m55s
pod/drc-example-distributedrediscluster-2-1   1/1     Running   0          86s

NAME                                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
service/example-distributedrediscluster     ClusterIP   10.1.123.77   <none>        6379/TCP,16379/TCP   3m54s
service/example-distributedrediscluster-0   ClusterIP   None          <none>        6379/TCP,16379/TCP   3m54s
service/example-distributedrediscluster-1   ClusterIP   None          <none>        6379/TCP,16379/TCP   3m54s
service/example-distributedrediscluster-2   ClusterIP   None          <none>        6379/TCP,16379/TCP   3m54s

NAME                                                     READY   AGE
statefulset.apps/drc-example-distributedrediscluster-0   2/2     3m55s
statefulset.apps/drc-example-distributedrediscluster-1   2/2     3m55s
statefulset.apps/drc-example-distributedrediscluster-2   2/2     3m55s


[root@k8s-master redis-cluster-operator]# kubectl get distributedrediscluster
NAME                              MASTERSIZE   STATUS    AGE
example-distributedrediscluster   3            Healthy   3m7s
```

```shell
# 查看存储

[root@k8s-master example]# kubectl get pvc
NAME                                                 STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
redis-data-drc-example-distributedrediscluster-0-0   Bound    pvc-689ff6e5-7756-4a56-ba77-db1b6951af49   3Gi        RWO            rook-ceph-block   4m39s
redis-data-drc-example-distributedrediscluster-0-1   Bound    pvc-d1ef97dc-2e7e-41c2-b6aa-b9429c7add79   3Gi        RWO            rook-ceph-block   3m21s
redis-data-drc-example-distributedrediscluster-1-0   Bound    pvc-e441d5ba-5a64-4680-b52f-c22f5d23ca18   3Gi        RWO            rook-ceph-block   4m39s
redis-data-drc-example-distributedrediscluster-1-1   Bound    pvc-847a7eee-2cb8-4ae3-bd29-ae23275f3550   3Gi        RWO            rook-ceph-block   3m23s
redis-data-drc-example-distributedrediscluster-2-0   Bound    pvc-4cccf90e-63da-4dc5-9f19-be8e942bbd01   3Gi        RWO            rook-ceph-block   4m39s
redis-data-drc-example-distributedrediscluster-2-1   Bound    pvc-4a580424-4041-4491-a869-e386ceb903b6   3Gi        RWO            rook-ceph-block   2m10s


[root@k8s-master example]# kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                                        STORAGECLASS      REASON   AGE
pvc-06248e41-f5c7-4a93-b842-d32424e439a5   2Gi        RWO            Delete           Bound    default/redis-data-drc-example-distributedrediscluster-1-0   rook-ceph-block            8m22s
pvc-2062915a-9008-4940-ab7c-ce025f923d19   2Gi        RWO            Delete           Bound    default/redis-data-drc-example-distributedrediscluster-1-1   rook-ceph-block            7m29s
pvc-43b60e7c-b526-401d-b016-912b18d0d531   2Gi        RWO            Delete           Bound    default/redis-data-drc-example-distributedrediscluster-2-0   rook-ceph-block            8m22s
pvc-54d02c21-bacb-469a-8937-f42e694a1beb   2Gi        RWO            Delete           Bound    default/redis-data-drc-example-distributedrediscluster-0-0   rook-ceph-block            8m22s
pvc-88b9530b-8ecc-4445-a2d2-66c602c972af   2Gi        RWO            Delete           Bound    default/redis-data-drc-example-distributedrediscluster-2-1   rook-ceph-block            7m39s
pvc-d876ae49-0d43-4c27-8b61-ef65f9985555   2Gi        RWO            Delete           Bound    default/redis-data-drc-example-distributedrediscluster-0-1   rook-ceph-block            7m38s



[root@k8s-master example]# kubectl exec -it drc-example-distributedrediscluster-0-0 -- sh
/data # ls
dump.rdb        lost+found      nodes.conf      redis_password
/data # 
/data # 
/data # df -Th
Filesystem           Type            Size      Used Available Use% Mounted on
overlay              overlay        50.0G     17.1G     32.8G  34% /
tmpfs                tmpfs          64.0M         0     64.0M   0% /dev
tmpfs                tmpfs           7.7G         0      7.7G   0% /sys/fs/cgroup


/dev/rbd0            ext4            1.9G      6.0M      1.9G   0% /data


/dev/mapper/centos-root
                     xfs            50.0G     17.1G     32.8G  34% /conf
/dev/mapper/centos-root
                     xfs            50.0G     17.1G     32.8G  34% /dev/termination-log
/dev/mapper/centos-root
                     xfs            50.0G     17.1G     32.8G  34% /etc/resolv.conf
/dev/mapper/centos-root
                     xfs            50.0G     17.1G     32.8G  34% /etc/hostname
/dev/mapper/centos-root
                     xfs            50.0G     17.1G     32.8G  34% /etc/hosts
shm                  tmpfs          64.0M         0     64.0M   0% /dev/shm
tmpfs                tmpfs           7.7G     12.0K      7.7G   0% /run/secrets/kubernetes.io/serviceaccount
tmpfs                tmpfs           7.7G         0      7.7G   0% /proc/acpi
tmpfs                tmpfs          64.0M         0     64.0M   0% /proc/kcore
tmpfs                tmpfs          64.0M         0     64.0M   0% /proc/keys
tmpfs                tmpfs          64.0M         0     64.0M   0% /proc/timer_list
tmpfs                tmpfs          64.0M         0     64.0M   0% /proc/timer_stats
tmpfs                tmpfs          64.0M         0     64.0M   0% /proc/sched_debug
tmpfs                tmpfs           7.7G         0      7.7G   0% /proc/scsi
tmpfs                tmpfs           7.7G         0      7.7G   0% /sys/firmware
/data # 
```



#### 3.4.新建Service

redis-svc-1.yaml

```yaml
# redis-svc-1.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: redis-svc-1
  labels:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-0-0
spec:
  type: NodePort
  ports:
    - port: 6379
      name: redis-svc-1
      targetPort: 6379
      nodePort: 30901
  selector:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-0-0
```

redis-svc-2.yaml

```yaml
# redis-svc-2.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: redis-svc-2
  labels:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-0-1
spec:
  type: NodePort
  ports:
    - port: 6379
      name: redis-svc-2
      targetPort: 6379
      nodePort: 30902
  selector:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-0-1
```

redis-svc-3.yaml

```yaml
# redis-svc-3.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: redis-svc-3
  labels:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-1-0
spec:
  type: NodePort
  ports:
    - port: 6379
      name: redis-svc-3
      targetPort: 6379
      nodePort: 30903
  selector:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-1-0
```

redis-svc-4.yaml

```yaml
# redis-svc-4.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: redis-svc-4
  labels:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-1-1
spec:
  type: NodePort
  ports:
    - port: 6379
      name: redis-svc-4
      targetPort: 6379
      nodePort: 30904
  selector:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-1-1
```

redis-svc-5.yaml

```yaml
# redis-svc-5.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: redis-svc-5
  labels:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-2-0
spec:
  type: NodePort
  ports:
    - port: 6379
      name: redis-svc-5
      targetPort: 6379
      nodePort: 30905
  selector:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-2-0
```

redis-svc-6.yaml

```yaml
# redis-svc-6.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: redis-svc-6
  labels:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-2-1
spec:
  type: NodePort
  ports:
    - port: 6379
      name: redis-svc-6
      targetPort: 6379
      nodePort: 30906
  selector:
    statefulset.kubernetes.io/pod-name: drc-example-distributedrediscluster-2-1
```



创建服务

```shell
# kubectl apply -f redis-svc-1.yaml
# kubectl apply -f redis-svc-2.yaml
# kubectl apply -f redis-svc-3.yaml
# kubectl apply -f redis-svc-4.yaml
# kubectl apply -f redis-svc-5.yaml
# kubectl apply -f redis-svc-6.yaml


[root@k8s-master operator]# kubectl get svc
NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)              AGE
example-distributedrediscluster     ClusterIP   10.99.154.50     <none>        6379/TCP,16379/TCP   5h21m
example-distributedrediscluster-0   ClusterIP   None             <none>        6379/TCP,16379/TCP   5h21m
example-distributedrediscluster-1   ClusterIP   None             <none>        6379/TCP,16379/TCP   5h21m
example-distributedrediscluster-2   ClusterIP   None             <none>        6379/TCP,16379/TCP   5h21m
redis-svc-1                         NodePort    10.97.60.200     <none>        6379:30901/TCP       72s
redis-svc-2                         NodePort    10.96.116.98     <none>        6379:30902/TCP       29s
redis-svc-3                         NodePort    10.109.25.127    <none>        6379:30903/TCP       23s
redis-svc-4                         NodePort    10.108.160.149   <none>        6379:30904/TCP       18s
redis-svc-5                         NodePort    10.101.191.132   <none>        6379:30905/TCP       11s
redis-svc-6                         NodePort    10.103.173.119   <none>        6379:30906/TCP       4s
```

```yaml
# Redis集群配置


# 说明
# 配置：
redis-svc-1；
redis-svc-2；
redis-svc-3；
redis-svc-4；
redis-svc-5；
redis-svc-6；
#完整配置
nodes: redis-svc-1.default.svc.cluster.local:6379,redis-svc-2.default.svc.cluster.local:6379,redis-svc-3.default.svc.cluster.local:6379,redis-svc-4.default.svc.cluster.local:6379,redis-svc-5.default.svc.cluster.local:6379,redis-svc-6.default.svc.cluster.local:6379



redis-svc-1.default.svc.cluster.local:6379
redis-svc-2.default.svc.cluster.local:6379
redis-svc-3.default.svc.cluster.local:6379
redis-svc-4.default.svc.cluster.local:6379
redis-svc-5.default.svc.cluster.local:6379
redis-svc-6.default.svc.cluster.local:6379
```



#### 3.5.客户端

![](md\middleware\redis\redis-3.png)

![](md\middleware\redis\redis-4.png)





### 4.RabbitMQ

#### 4.1.安装方式

```shell
# 安装
# RabbitMQ #1 (Official)  rabbitmq/cluster-operator
https://github.com/rabbitmq/cluster-operator


#RabbitMQ Cluster Kubernetes Operator Quickstart
https://www.rabbitmq.com/kubernetes/operator/quickstart-operator.html

# Using RabbitMQ Cluster Kubernetes Operator
https://www.rabbitmq.com/kubernetes/operator/using-operator.html
```

```shell
# 参考

# Operator overview
https://www.rabbitmq.com/kubernetes/operator/operator-overview.html

# Deploying an operator
https://www.rabbitmq.com/kubernetes/operator/install-operator.html

# Deploying a RabbitMQ cluster
https://www.rabbitmq.com/kubernetes/operator/using-operator.html

# Monitoring the cluster
https://www.rabbitmq.com/kubernetes/operator/operator-monitoring.html

# Troubleshooting operator deployments
https://www.rabbitmq.com/kubernetes/operator/troubleshooting-operator.html
```



**下载rabbitmq/cluster-operator**

```shell
# git clone https://github.com/rabbitmq/cluster-operator.git
```



#### 1.1.部署RabbitMQ集群

**1.下载rabbitmq/cluster-operator**

```shell
# git  git clone https://github.com/rabbitmq/cluster-operator.git


[root@k8s-master rabbitmq]# git clone https://github.com/rabbitmq/cluster-operator.git
Cloning into 'cluster-operator'...
remote: Enumerating objects: 11892, done.
remote: Counting objects: 100% (2279/2279), done.
remote: Compressing objects: 100% (976/976), done.
remote: Total 11892 (delta 1438), reused 1976 (delta 1240), pack-reused 9613
Receiving objects: 100% (11892/11892), 11.95 MiB | 1.66 MiB/s, done.
Resolving deltas: 100% (7740/7740), done.
```



**2.创建Operator**

```bash
# kubectl apply -f https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml


# 创建
[root@k8s-master rabbitmq]# kubectl apply -f https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml
namespace/rabbitmq-system created
customresourcedefinition.apiextensions.k8s.io/rabbitmqclusters.rabbitmq.com created
serviceaccount/rabbitmq-cluster-operator created
role.rbac.authorization.k8s.io/rabbitmq-cluster-leader-election-role created
clusterrole.rbac.authorization.k8s.io/rabbitmq-cluster-operator-role created
rolebinding.rbac.authorization.k8s.io/rabbitmq-cluster-leader-election-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/rabbitmq-cluster-operator-rolebinding created
deployment.apps/rabbitmq-cluster-operator created


[root@k8s-master1 production-ready]# kubectl get all -n rabbitmq-system
NAME                                             READY   STATUS    RESTARTS   AGE
pod/rabbitmq-cluster-operator-7cbf865f89-6rppq   1/1     Running   0          50s

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/rabbitmq-cluster-operator   1/1     1            1           50s

NAME                                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/rabbitmq-cluster-operator-7cbf865f89   1         1         1       50s
```



**3.修改配置**

```yaml
# cluster-operator/docs/examples/production-ready
# rabbitmq.yaml
# cpu、memory
# storageClassName: rook-ceph-block




[root@k8s-master production-ready]# vim rabbitmq.yaml 

apiVersion: rabbitmq.com/v1beta1
kind: RabbitmqCluster
metadata:
  name: production-ready
spec:
  replicas: 3
  resources:
    requests:
      cpu: 1
      memory: 2Gi
    limits:
      cpu: 1
      memory: 2Gi
  rabbitmq:
    additionalConfig: |
      cluster_partition_handling = pause_minority
      vm_memory_high_watermark_paging_ratio = 0.99
      disk_free_limit.relative = 1.0
      collect_statistics_interval = 10000
  persistence:
    storageClassName: rook-ceph-block
    storage: "15Gi"
```



**4.使用cluster-operator创建RabbitMQ集群**

```shell
[root@k8s-master1 production-ready]# pwd
/k8s/middleware/rabbitmq/cluster-operator/docs/examples/production-ready
[root@k8s-master1 production-ready]# ll
total 16
-rw-r--r--. 1 root root  199 Dec 22 11:14 pod-disruption-budget.yaml
-rw-r--r--. 1 root root  500 Dec 22 11:16 rabbitmq.yaml
-rw-r--r--. 1 root root 3134 Dec 22 11:14 README.md
-rw-r--r--. 1 root root  409 Dec 22 11:14 ssd-gke.yaml


# 创建
[root@k8s-master1 production-ready]# kubectl apply -f rabbitmq.yaml
rabbitmqcluster.rabbitmq.com/production-ready created


[root@k8s-master1 production-ready]# kubectl apply -f pod-disruption-budget.yaml 
poddisruptionbudget.policy/production-ready-rabbitmq created


# 查看
[root@k8s-master1 ~]# kubectl get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/production-ready-server-0   1/1     Running   0          10m
pod/production-ready-server-1   1/1     Running   2          88s
pod/production-ready-server-2   1/1     Running   0          10m

NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
service/production-ready            ClusterIP   10.1.23.122    <none>        5672/TCP,15672/TCP,15692/TCP    10m
service/production-ready-nodes      ClusterIP   None           <none>        4369/TCP,25672/TCP              10m

NAME                                       READY   AGE
statefulset.apps/production-ready-server   3/3     10m

NAME                                            ALLREPLICASREADY   RECONCILESUCCESS   AGE
rabbitmqcluster.rabbitmq.com/production-ready   True               True               10m



[root@k8s-master production-ready]# kubectl get all -n rabbitmq-system
NAME                                             READY   STATUS    RESTARTS   AGE
pod/rabbitmq-cluster-operator-7cbf865f89-s95gq   1/1     Running   0          105m

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/rabbitmq-cluster-operator   1/1     1            1           105m

NAME                                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/rabbitmq-cluster-operator-7cbf865f89   1         1         1       105m


# PVC
[root@k8s-master1 ~]# kubectl get pvc
NAME                                    STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
persistence-production-ready-server-0   Bound    pvc-837eb2ac-dd8d-4972-a0ec-51667f12b96f   15Gi       RWO            rook-ceph-block   11m
persistence-production-ready-server-1   Bound    pvc-d0732c03-f9f6-4826-b6bc-902d099e342d   15Gi       RWO            rook-ceph-block   11m
persistence-production-ready-server-2   Bound    pvc-8bc841df-20fe-4db1-a128-152539e7ab3d   15Gi       RWO            rook-ceph-block   11m
```



#### 1.2.获取用户名和密码

**获取用户名和密码**

```shell
# user
kubectl -n NAMESPACE get secret INSTANCE-default-user -o jsonpath="{.data.username}" | base64 --decode
# pass
kubectl -n NAMESPACE get secret INSTANCE-default-user -o jsonpath="{.data.password}" | base64 --decode


# 获取获取用户名和密码  production-ready
username="$(kubectl get secret production-ready-default-user -o jsonpath='{.data.username}' | base64 --decode)"
echo "username: $username"
password="$(kubectl get secret production-ready-default-user -o jsonpath='{.data.password}' | base64 --decode)"
echo "password: $password"


root@k8s-master1 ~]# username="$(kubectl get secret production-ready-default-user -o jsonpath='{.data.username}' | base64 --decode)"
[root@k8s-master1 ~]# echo "username: $username"
username: default_user_VcSb2rx0RLFPBfw3kcv

[root@k8s-master1 ~]# password="$(kubectl get secret production-ready-default-user -o jsonpath='{.data.password}' | base64 --decode)"
[root@k8s-master1 ~]# echo "password: $password"
password: apexs6u5gj2wAu29O1YxGtXuQnINzEvs


# 登录信息：
username: default_user_skDybgNFObPZkgkKzJN
password: lojS77Jp5DvMURsjFp3XK73XnwLa1hWI


kubectl port-forward "service/production-ready" 15672
```

```shell
# 系统创建service
# production-ready

[root@k8s-master1 production-ready]# kubectl get svc
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
production-ready            ClusterIP   10.1.23.122    <none>        5672/TCP,15672/TCP,15692/TCP    15m
production-ready-nodes      ClusterIP   None           <none>        4369/TCP,25672/TCP              15m


[root@k8s-master production-ready]# kubectl port-forward "service/production-ready" 15672
Forwarding from 127.0.0.1:15672 -> 15672
Forwarding from [::1]:15672 -> 15672



curl -u$username:$password localhost:15672/api/overview
{"management_version":"3.8.9","rates_mode":"basic", ...}


curl -udefault_user_VcSb2rx0RLFPBfw3kcv:apexs6u5gj2wAu29O1YxGtXuQnINzEvs 10.1.23.122:15672/api/overview


# 访问
[root@k8s-master production-ready]# curl -udefault_user_vmOg0aiF8VarwVhb0sE:rKr9N2v-Cxrec78zo1LUnigjyOla_YLv 10.111.187.65:15672/api/overview
{"management_version":"3.8.21","rates_mode":"basic","sample_retention_policies":{"global":[600,3600,28800,86400],"basic":[600,3600],"detailed":[600]},"exchange_types":[{"name":"direct","description":"AMQP direct exchange, as per the AMQP specification","enabled":true},{"name":"fanout","description":"AMQP fanout exchange, as per the AMQP specification","enabled":true},{"name":"headers","description":"AMQP headers exchange, as per the AMQP specification","enabled":true},{"name":"topic","description":"AMQP topic exchange, as per the AMQP specification","enabled":true}],"product_version":"3.8.21","product_name":"RabbitMQ","rabbitmq_version":"3.8.21","cluster_name":"production-ready","erlang_version":"24.0.5","erlang_full_version":"Erlang/OTP 24 [erts-12.0.3] [source] [64-bit] [smp:4:1] [ds:4:1:10] [async-threads:1] [jit]","disable_stats":false,"enable_queue_totals":false,"message_stats":{},"churn_rates":{"channel_closed":0,"channel_closed_details":{"rate":0.0},"channel_created":0,"channel_created_details":{"rate":0.0},"connection_closed":153,"connection_closed_details":{"rate":0.2},"connection_created":0,"connection_created_details":{"rate":0.0},"queue_created":0,"queue_created_details":{"rate":0.0},"queue_declared":0,"queue_declared_details":{"rate":0.0},"queue_deleted":0,"queue_deleted_details":{"rate":0.0}},"queue_totals":{},"object_totals":{"channels":0,"connections":0,"consumers":0,"exchanges":7,"queues":0},"statistics_db_event_queue":0,"node":"rabbit@production-ready-server-1.production-ready-nodes.default","listeners":[{"node":"rabbit@production-ready-server-0.production-ready-nodes.default","protocol":"amqp","ip_address":"::","port":5672,"socket_opts":{"backlog":128,"nodelay":true,"linger":[true,0],"exit_on_close":false}},{"node":"rabbit@production-ready-server-1.production-ready-nodes.default","protocol":"amqp","ip_address":"::","port":5672,"socket_opts":{"backlog":128,"nodelay":true,"linger":[true,0],"exit_on_close":false}},{"node":"rabbit@production-ready-server-2.production-ready-nodes.default","protocol":"amqp","ip_address":"::","port":5672,"socket_opts":{"backlog":128,"nodelay":true,"linger":[true,0],"exit_on_close":false}},{"node":"rabbit@production-ready-server-0.production-ready-nodes.default","protocol":"clustering","ip_address":"::","port":25672,"socket_opts":[]},{"node":"rabbit@production-ready-server-1.production-ready-nodes.default","protocol":"clustering","ip_address":"::","port":25672,"socket_opts":[]},{"node":"rabbit@production-ready-server-2.production-ready-nodes.default","protocol":"clustering","ip_address":"::","port":25672,"socket_opts":[]},{"node":"rabbit@production-ready-server-0.production-ready-nodes.default","protocol":"http","ip_address":"::","port":15672,"socket_opts":{"cowboy_opts":{"sendfile":false},"port":15672}},{"node":"rabbit@production-ready-server-1.production-ready-nodes.default","protocol":"http","ip_address":"::","port":15672,"socket_opts":{"cowboy_opts":{"sendfile":false},"port":15672}},{"node":"rabbit@production-ready-server-2.production-ready-nodes.default","protocol":"http","ip_address":"::","port":15672,"socket_opts":{"cowboy_opts":{"sendfile":false},"port":15672}},{"node":"rabbit@production-ready-server-0.production-ready-nodes.default","protocol":"http/prometheus","ip_address":"::","port":15692,"socket_opts":{"cowboy_opts":{"sendfile":false},"port":15692,"protocol":"http/prometheus"}},{"node":"rabbit@production-ready-server-1.production-ready-nodes.default","protocol":"http/prometheus","ip_address":"::","port":15692,"socket_opts":{"cowboy_opts":{"sendfile":false},"port":15692,"protocol":"http/prometheus"}},{"node":"rabbit@production-ready-server-2.production-ready-nodes.default","protocol":"http/prometheus","ip_address":"::","port":15692,"socket_opts":{"cowboy_opts":{"sendfile":false},"port":15692,"protocol":"http/prometheus"}}],"contexts":[{"ssl_opts":[],"node":"rabbit@production-ready-server-0.production-ready-nodes.default","description":"RabbitMQ Management","path":"/","cowboy_opts":"[{sendfile,false}]","port":"15672"},{"ssl_opts":[],"node":"rabbit@production-ready-server-1.production-ready-nodes.default","description":"RabbitMQ Management","path":"/","cowboy_opts":"[{sendfile,false}]","port":"15672"},{"ssl_opts":[],"node":"rabbit@production-ready-server-2.production-ready-nodes.default","description":"RabbitMQ Management","path":"/","cowboy_opts":"[{sendfile,false}]","port":"15672"},{"ssl_opts":[],"node":"rabbit@production-ready-server-0.production-ready-nodes.default","description":"RabbitMQ Prometheus","path":"/","cowboy_opts":"[{sendfile,false}]","port":"15692","protocol":"'http/prometheus'"},{"ssl_opts":[],"node":"rabbit@production-ready-server-1.production-ready-nodes.default","description":"RabbitMQ Prometheus","path":"/","cowboy_opts":"[{sendfile,false}]","port":"15692","protocol":"'http/prometheus'"},{"ssl_opts":[],"node":"rabbit@production-ready-server-2.production-ready-nodes.default","description":"RabbitMQ Prometheus","path":"/","cowboy_opts":"[{sendfile,false}]","port":"15692","protocol":"'http/prometheus'"}]}[root@k8s-master production-ready]# 
```



#### 1.3.访问RabbitMQ管理UI

rabbitmq-cluster-svc.yaml

```yaml
# rabbitmq-cluster-svc.yaml


apiVersion: v1
kind: Service
metadata:
  namespace: default
  name: rabbitmq-cluster-svc
  labels:
    app.kubernetes.io/name: production-ready
spec:
  type: NodePort
  ports:
    - port: 15672
      name: rabbitmq-http
      targetPort: 15672
      nodePort: 30672
    - port: 5672
      name: rabbitmq-tcp
      targetPort: 5672
      nodePort: 30673
  selector:
    app.kubernetes.io/name: production-ready
```

```shell
[root@k8s-master1 rabbitmq]# kubectl apply -f rabbitmq-cluster-svc.yaml 
service/rabbitmq-cluster-svc created


[root@k8s-master1 rabbitmq]# kubectl get svc
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                          AGE
production-ready            ClusterIP   10.1.23.122    <none>        5672/TCP,15672/TCP,15692/TCP     20m
production-ready-nodes      ClusterIP   None           <none>        4369/TCP,25672/TCP               20m
rabbitmq-cluster-svc        NodePort    10.1.182.218   <none>        15672:30672/TCP,5672:30673/TCP   20s


curl -udefault_user_vmOg0aiF8VarwVhb0sE:rKr9N2v-Cxrec78zo1LUnigjyOla_YLv 10.106.36.141:15672/api/overview
```

```shell
# 地址

http://172.51.216.81:30672
username: default_user_skDybgNFObPZkgkKzJN
password: lojS77Jp5DvMURsjFp3XK73XnwLa1hWI



# k8s内部访问地址
# K8s 中的容器使用访问
svcname.namespace.svc.cluster.local:port
production-ready.default.svc.cluster.local:5672
```





### 5.MySQL

#### 5.1.安装方式

```shell
# 安装
# MySQL #3	presslabs/mysql-operator
https://github.com/bitpoke/mysql-operator


https://github.com/bitpoke/mysql-operator/blob/master/docs/_index.md
https://github.com/bitpoke/mysql-operator/blob/master/docs/deploy-mysql-cluster.md
```

```shell
# 部署集群

# 1.安装operator
helm repo add presslabs https://presslabs.github.io/charts
helm install presslabs/mysql-operator --name mysql-operator


# 2.安装mysql集群
kubectl apply -f https://raw.githubusercontent.com/bitpoke/mysql-operator/master/examples/example-cluster-secret.yaml
kubectl apply -f https://raw.githubusercontent.com/bitpoke/mysql-operator/master/examples/example-cluster.yaml
```

**下载rabbitmq/cluster-operator**

```shell
# git clone https://github.com/bitpoke/mysql-operator.git


# 下载不下来
# 下载上传
https://github.com/bitpoke/mysql-operator/archive/refs/tags/v0.5.2.tar.gz
```



#### 5.2.部署MySQL集群

##### 5.2.1.Helm安装Operator

**注意：直接安装PVC会出问题，需要下载到本地，修改配置。**



**1.下载Helm安装包**

```shell
[root@k8s-master1 helm]# helm repo add presslabs https://presslabs.github.io/charts
"presslabs" has been added to your repositories

[root@k8s-master1 helm]# helm search repo mysql-operator
NAME                    	CHART VERSION	APP VERSION	DESCRIPTION                    
presslabs/mysql-operator	0.4.0        	v0.4.0     	A Helm chart for mysql operator

[root@k8s-master1 helm]# helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "chartmuseum" chart repository
...Successfully got an update from the "harbor" chart repository
...Successfully got an update from the "elastic" chart repository
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "presslabs" chart repository
...Successfully got an update from the "aliyun" chart repository
...Successfully got an update from the "gitlab" chart repository
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈


[root@k8s-master helm]# helm fetch presslabs/mysql-operator
[root@k8s-master helm]# tar -zxf mysql-operator-0.4.0.tgz
```



**2.修改配置**

```shell
[root@k8s-master1 mysql-operator]# pwd
/k8s/middleware/mysql/helm/mysql-operator
[root@k8s-master1 mysql-operator]# ll
total 24
-rwxr-xr-x 1 root root  279 Jun 17  2020 Chart.yaml
drwxr-xr-x 2 root root   85 Dec  1 17:26 crds
-rwxr-xr-x 1 root root 4545 Jun 17  2020 README.md
drwxr-xr-x 2 root root 4096 Dec  1 17:30 templates
-rwxr-xr-x 1 root root 7212 Dec  1 17:32 values.yaml


# 修改配置文件
[root@k8s-master mysql-operator]# vim values.yaml 
......
  persistence:
    enabled: true
    storageClass: "rook-ceph-block"
    accessMode: "ReadWriteOnce"
    size: 5Gi
......
```



**3.创建Operator**

```shell
# 安装
[root@k8s-master helm]# helm install mysql-operator mysql-operator
W1202 11:18:07.258634   61198 warnings.go:70] apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
W1202 11:18:07.269227   61198 warnings.go:70] apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
W1202 11:18:07.880685   61198 warnings.go:70] rbac.authorization.k8s.io/v1beta1 ClusterRoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 ClusterRoleBinding
W1202 11:18:07.918011   61198 warnings.go:70] rbac.authorization.k8s.io/v1beta1 ClusterRoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 ClusterRoleBinding
NAME: mysql-operator
LAST DEPLOYED: Thu Dec  2 11:18:07 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
You can create a new cluster by issuing:

cat <<EOF | kubectl apply -f-
apiVersion: mysql.presslabs.org/v1alpha1
kind: MysqlCluster
metadata:
  name: my-cluster
spec:
  replicas: 1
  secretName: my-cluster-secret
---
apiVersion: v1
kind: Secret
metadata:
  name: my-cluster-secret
type: Opaque
data:
  ROOT_PASSWORD: $(echo -n "not-so-secure" | base64)
EOF



[root@k8s-master1 helm]# helm list
NAME          	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART                 APP VERSION
mysql-operator	default  	1       	2021-12-21 16:14:27.996829056 +0800 CST	deployed	mysql-operator-0.4.0  v0.4.0  


NAME                           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)            AGE
service/kubernetes             ClusterIP   10.1.0.1       <none>        443/TCP            5d2h
service/mysql-operator         ClusterIP   10.1.249.252   <none>        80/TCP             109s
service/mysql-operator-0-svc   ClusterIP   10.1.239.169   <none>        80/TCP,10008/TCP   109s

NAME                              READY   AGE
statefulset.apps/mysql-operator   1/1     109s


[root@k8s-master1 helm]# kubectl get pvc
NAME                    STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-mysql-operator-0   Bound    pvc-ddbfb37a-6d9b-4a2d-ab40-7fc5c0676f1b   5Gi        RWO            rook-ceph-block   4m11s
```



##### 5.2.2.安装MySQL集群

**1.修改配置**

```shell
[root@k8s-master1 mysql]# tar -zxf mysql-operator-0.5.2.tar.gz 
[root@k8s-master1 mysql]# ll
total 328
drwxr-xr-x.  3 root root     60 Dec 21 16:11 helm
drwxrwxr-x. 12 root root   4096 Nov 23 17:53 mysql-operator-0.5.2
-rw-r--r--.  1 root root 329533 Dec 21 16:05 mysql-operator-0.5.2.tar.gz
[root@k8s-master1 mysql]# cd mysql-operator-0.5.2
[root@k8s-master1 mysql-operator-0.5.2]# cd examples/
[root@k8s-master1 examples]# ll
total 32
-rw-rw-r--. 1 root root  656 Nov 23 17:53 example-backup-secret.yaml
-rw-rw-r--. 1 root root  608 Nov 23 17:53 example-backup.yaml
-rw-rw-r--. 1 root root  331 Nov 23 17:53 example-cluster-init.yaml
-rw-rw-r--. 1 root root  256 Nov 23 17:53 example-cluster-secret.yaml
-rw-rw-r--. 1 root root 4103 Nov 23 17:53 example-cluster.yaml
-rw-rw-r--. 1 root root  184 Nov 23 17:53 example-database.yaml
-rw-rw-r--. 1 root root  533 Nov 23 17:53 example-user.yaml
```

```shell
[root@k8s-master1 examples]# pwd
/k8s/middleware/mysql/mysql-operator-0.5.2/examples
[root@k8s-master examples]# ll
total 32
-rw-r--r-- 1 root root  656 Dec  1 17:12 example-backup-secret.yaml
-rw-r--r-- 1 root root  608 Dec  1 17:12 example-backup.yaml
-rw-r--r-- 1 root root  331 Dec  1 17:12 example-cluster-init.yaml
-rw-r--r-- 1 root root  256 Dec  1 20:49 example-cluster-secret.yaml
-rw-r--r-- 1 root root 4131 Dec  1 20:53 example-cluster.yaml
-rw-r--r-- 1 root root  184 Dec  1 17:12 example-database.yaml
-rw-r--r-- 1 root root  533 Dec  1 17:12 example-user.yaml
```

```yaml
# example-cluster-secret.yaml


[root@k8s-master1 examples]# vim example-cluster-secret.yaml 

apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  # root password is required to be specified
  ROOT_PASSWORD: bXlwYXNz
  ## application credentials that will be created at cluster bootstrap
  # DATABASE:
  # USER:
  # PASSWORD:
  


# 修改root密码 ROOT_PASSWORD
[root@k8s-master examples]# echo -n root |base64
cm9vdA==
```

```yaml
# example-cluster.yam


[root@k8s-master1 examples]# vim example-cluster.yaml 

apiVersion: mysql.presslabs.org/v1alpha1
kind: MysqlCluster
metadata:
  name: my-cluster
spec:
  replicas: 2
  secretName: my-secret
......


# Rook: rook-ceph-block
# 修改配置
   volumeSpec:
     persistentVolumeClaim:
       accessModes: [ "ReadWriteOnce" ]
       resources:
         requests:
           storage: 25Gi
       storageClassName: rook-ceph-block
```



**2.安装MySQL**

```shell
[root@k8s-master1 examples]# kubectl apply -f example-cluster-secret.yaml 
secret/my-secret created
[root@k8s-master1 examples]# kubectl apply -f example-cluster.yaml 
mysqlcluster.mysql.presslabs.org/my-cluster created


# 查看
[root@k8s-master1 examples]# kubectl get all
NAME                     READY   STATUS    RESTARTS   AGE
pod/my-cluster-mysql-0   4/4     Running   0          23m
pod/my-cluster-mysql-1   4/4     Running   0          19m
pod/mysql-operator-0     2/2     Running   0          41m

NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
service/kubernetes                  ClusterIP   10.1.0.1       <none>        443/TCP             5d3h
service/my-cluster-mysql            ClusterIP   10.1.163.112   <none>        3306/TCP            23m
service/my-cluster-mysql-master     ClusterIP   10.1.32.174    <none>        3306/TCP,8080/TCP   23m
service/my-cluster-mysql-replicas   ClusterIP   10.1.199.160   <none>        3306/TCP,8080/TCP   23m
service/mysql                       ClusterIP   None           <none>        3306/TCP,9125/TCP   23m
service/mysql-operator              ClusterIP   10.1.249.252   <none>        80/TCP              41m
service/mysql-operator-0-svc        ClusterIP   10.1.239.169   <none>        80/TCP,10008/TCP    41m

NAME                                READY   AGE
statefulset.apps/my-cluster-mysql   2/2     23m
statefulset.apps/mysql-operator     1/1     41m


[root@k8s-master1 examples]# kubectl get pvc
NAME                      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-my-cluster-mysql-0   Bound    pvc-0119f938-3c89-4f1b-b2df-8b5189c1525c   25Gi       RWO            rook-ceph-block   23m
data-my-cluster-mysql-1   Bound    pvc-87fbd15b-bfb7-443a-9289-177385eb5e3f   25Gi       RWO            rook-ceph-block   19m
data-mysql-operator-0     Bound    pvc-ddbfb37a-6d9b-4a2d-ab40-7fc5c0676f1b   5Gi        RWO            rook-ceph-block   42m
```



#### 5.3.MySQL访问

##### 5.3.1.修改service类型

```shell
[root@k8s-master1 examples]# kubectl get svc
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
kubernetes                  ClusterIP   10.1.0.1       <none>        443/TCP             5d3h
my-cluster-mysql            ClusterIP   10.1.163.112   <none>        3306/TCP            37m
my-cluster-mysql-master     ClusterIP   10.1.32.174    <none>        3306/TCP,8080/TCP   37m
my-cluster-mysql-replicas   ClusterIP   10.1.199.160   <none>        3306/TCP,8080/TCP   37m
mysql                       ClusterIP   None           <none>        3306/TCP,9125/TCP   37m
mysql-operator              ClusterIP   10.1.249.252   <none>        80/TCP              55m
mysql-operator-0-svc        ClusterIP   10.1.239.169   <none>        80/TCP,10008/TCP    55m
```

```shell
# 修改service类型 type: NodePort

[root@k8s-master1 examples]# kubectl edit svc my-cluster-mysql-master
service/my-cluster-mysql-master edited

[root@k8s-master1 examples]# kubectl edit svc my-cluster-mysql-replicas
service/my-cluster-mysql-replicas edited

spec:
......
  type: NodePort


# 查看
[root@k8s-master1 examples]# kubectl get svc
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                         AGE
kubernetes                  ClusterIP   10.1.0.1       <none>        443/TCP                         5d3h
my-cluster-mysql            ClusterIP   10.1.163.112   <none>        3306/TCP                        39m


my-cluster-mysql-master     NodePort    10.1.32.174    <none>        3306:30001/TCP,8080:32568/TCP   39m
my-cluster-mysql-replicas   NodePort    10.1.199.160   <none>        3306:32393/TCP,8080:30392/TCP   39m


mysql                       ClusterIP   None           <none>        3306/TCP,9125/TCP               39m
mysql-operator              ClusterIP   10.1.249.252   <none>        80/TCP                          58m
mysql-operator-0-svc        ClusterIP   10.1.239.169   <none>        80/TCP,10008/TCP                58m

```



##### 5.3.2.访问MySQL

```shell
# 访问地址


my-cluster-mysql-master     NodePort    10.1.32.174    <none>        3306:30001/TCP,8080:32568/TCP   39m
my-cluster-mysql-replicas   NodePort    10.1.199.160   <none>        3306:32393/TCP,8080:30392/TCP   39m

# 外部访问地址
my-cluster-mysql-master
172.51.216.81
30001
root/root

my-cluster-mysql-replicas
172.51.216.81
32393
root/root


# k8s内部访问地址
# K8s 中的容器使用访问
svcname.namespace.svc.cluster.local:port
my-cluster-mysql-master.default.svc.cluster.local:3306
```

![](md\middleware\mysql\mysql-1.png)

![](md\middleware\mysql\mysql-2.png)



##### 5.3.3.测试

主节点：k8s-mysql-master     my-cluster-mysql-master

从节点：k8s-mysql-slaver       my-cluster-mysql-replicas

**主节点可以读写，从节点只能读。**

在主节点创建数据库db1，从节点自动创建db1.

![](md\middleware\mysql\mysql-3.png)















