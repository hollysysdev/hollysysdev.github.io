# K8S - 实战篇 --- 部署Kubernetes组件



* TOC
{:toc}



## 一、概述



**Kubernetes集群需要的组件：**

- **基础组件： HPA、Ingress；**
- **重要组件：Harbor、Rook、Helm、ChartMuseum（暂时没用）；**







## 二、基础组件



### 1.HPA

HPA（Horizontal Pod Autoscaler）在k8s集群中用于POD水平自动伸缩，它是基于CPU和内存利用率对Deployment和Replicaset控制器中的pod数量进行自动扩缩容（除了CPU和内存利用率之外，也可以基于其他应程序提供的度量指标custom metrics进行自动扩缩容）。pod自动缩放不适用于无法缩放的对象，比如DaemonSets。HPA由Kubernetes API资源和控制器实现。资源决定了控制器的行为，控制器会周期性的获取CPU和内存利用率，并与目标值相比较后来调整replication controller或deployment中的副本数量。

 

**HPA使用前提条件：**

- 集群中部署了Metrics Server插件, 可以获取到Pod的CPU和内存利用率。
- Pod部署的Yaml文件中必须设置资源限制和资源请求。



#### 1.1.安装metrics-server

metrics-server可以用来收集集群中的资源使用情况

https://github.com/kubernetes-sigs/metrics-server



**参考**

```shell
# 安装git
[root@master ~]# yum install git -y
# 获取metrics-server, 注意使用的版本
[root@master ~]# git clone -b v0.3.6 https://github.com/kubernetes-incubator/metrics-server
# 修改deployment, 注意修改的是镜像和初始化参数
[root@master ~]# cd /root/metrics-server/deploy/1.8+/
[root@master 1.8+]# vim metrics-server-deployment.yaml
按图中添加下面选项
hostNetwork: true
image: registry.cn-hangzhou.aliyuncs.com/google_containers/metrics-server-amd64:v0.3.6
args:
- --kubelet-insecure-tls
- --kubelet-preferred-address-types=InternalIP,Hostname,InternalDNS,ExternalDNS,ExternalIP
```

![](md\module\hpa-3.png)

**安装**

```shell
# 获取metrics-server, 注意使用的版本
[root@k8s-master hpa]# git clone -b v0.3.6 https://github.com/kubernetes-incubator/metrics-server


# 修改deployment, 注意修改的是镜像和初始化参数
[root@k8s-master1 1.8+]# cd /k8s/module/hpa/metrics-server/deploy/1.8+
[root@k8s-master1 1.8+]# ll
total 28
-rw-r--r--. 1 root root 393 Dec 17 09:22 aggregated-metrics-reader.yaml
-rw-r--r--. 1 root root 308 Dec 17 09:22 auth-delegator.yaml
-rw-r--r--. 1 root root 329 Dec 17 09:22 auth-reader.yaml
-rw-r--r--. 1 root root 298 Dec 17 09:22 metrics-apiservice.yaml
-rw-r--r--. 1 root root 804 Dec 17 09:22 metrics-server-deployment.yaml
-rw-r--r--. 1 root root 291 Dec 17 09:22 metrics-server-service.yaml
-rw-r--r--. 1 root root 517 Dec 17 09:22 resource-reader.yaml


[root@master 1.8+]# vim metrics-server-deployment.yaml
# 按图中添加下面选项
hostNetwork: true
image: registry.cn-hangzhou.aliyuncs.com/google_containers/metrics-server-amd64:v0.3.6
args:
- --kubelet-insecure-tls
- --kubelet-preferred-address-types=InternalIP,Hostname,InternalDNS,ExternalDNS,ExternalIP
```

```powershell
# 安装metrics-server

[root@k8s-master1 1.8+]# kubectl apply -f ./
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
Warning: rbac.authorization.k8s.io/v1beta1 ClusterRoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 ClusterRoleBinding
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
Warning: rbac.authorization.k8s.io/v1beta1 RoleBinding is deprecated in v1.17+, unavailable in v1.22+; use rbac.authorization.k8s.io/v1 RoleBinding
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
Warning: apiregistration.k8s.io/v1beta1 APIService is deprecated in v1.19+, unavailable in v1.22+; use apiregistration.k8s.io/v1 APIService
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
serviceaccount/metrics-server created
deployment.apps/metrics-server created
service/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created


# 查看pod运行情况
[root@k8s-master 1.8+]# kubectl get pod -n kube-system
NAME                                       READY   STATUS    RESTARTS   AGE
...
metrics-server-9c7dc6fdc-mgl6r             1/1     Running   0          30s


# 使用kubectl top node 查看资源使用情况
[root@k8s-master1 1.8+]# kubectl top node
NAME          CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
k8s-master1   344m         8%     3791Mi          24%       
k8s-master2   283m         7%     2373Mi          15%       
k8s-master3   332m         8%     2202Mi          13%       
k8s-node1     250m         6%     1189Mi          7%        
k8s-node2     258m         6%     1270Mi          8%        
k8s-node3     267m         6%     1173Mi          7%        
k8s-node4     240m         6%     1197Mi          7% 


[root@k8s-master1 1.8+]# kubectl top pod -n kube-system
NAME                                       CPU(cores)   MEMORY(bytes)   
calico-kube-controllers-558995777d-l8lpr   3m           27Mi            
calico-node-6l5w2                          21m          150Mi           
calico-node-g68dm                          22m          142Mi           
calico-node-qt82k                          29m          150Mi           
calico-node-rvtbg                          34m          146Mi           
calico-node-tn2xj                          19m          146Mi           
calico-node-wnjhd                          24m          149Mi           
calico-node-wt5cb                          23m          149Mi           
coredns-7f89b7bc75-h2htb                   2m           16Mi            
coredns-7f89b7bc75-k5phr                   2m           16Mi            
etcd-k8s-master1                           19m          192Mi           
etcd-k8s-master2                           19m          193Mi           
etcd-k8s-master3                           21m          192Mi           
kube-apiserver-k8s-master1                 35m          453Mi           
kube-apiserver-k8s-master2                 35m          427Mi           
kube-apiserver-k8s-master3                 34m          438Mi           
kube-controller-manager-k8s-master1        1m           29Mi            
kube-controller-manager-k8s-master2        1m           27Mi            
kube-controller-manager-k8s-master3        7m           61Mi            
kube-proxy-5pjpd                           1m           20Mi            
kube-proxy-86vds                           1m           21Mi            
kube-proxy-cv28h                           1m           18Mi            
kube-proxy-mmbj6                           1m           17Mi            
kube-proxy-s9lhm                           1m           19Mi            
kube-proxy-tvt2z                           1m           18Mi            
kube-proxy-w7j6l                           1m           21Mi            
kube-scheduler-k8s-master1                 2m           23Mi            
kube-scheduler-k8s-master2                 2m           23Mi            
kube-scheduler-k8s-master3                 2m           27Mi            
metrics-server-67bd88dc86-dk2w9            1m           14Mi  


# 至此,metrics-server安装完成
```



#### 1.2.常用命令

```shell
# 使用kubectl top node 查看资源使用情况
[root@master 1.8+]# kubectl top node


[root@k8s-master hpa]# kubectl describe node k8s-node1


# 查看pod资源
[root@master 1.8+]# kubectl top pod -n kube-system


# 查看hpa
[root@k8s-master hpa]# kubectl get hpa -n dev


[root@k8s-master ~]# kubectl get deploy -n dev -w
```





### 2.Ingress

Ingress 是对集群中服务的外部访问进行管理的 API 对象，典型的访问方式是 HTTP和HTTPS。

Ingress 可以提供负载均衡、SSL 和基于名称的虚拟托管。

必须具有 ingress 控制器【例如 ingress-nginx】才能满足 Ingress 的要求。仅创建 Ingress 资源无效。



#### 2.1.搭建Ingress

（1）在gitlab上下载yaml文件，并创建部署

gitlab ingress-nginx项目：https://github.com/kubernetes/ingress-nginx

ingress安装指南：https://kubernetes.github.io/ingress-nginx/deploy/



```shell
https://kubernetes.github.io/ingress-nginx/deploy/

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/baremetal/deploy.yaml


# 安装说明
Bare metal clusters¶
This section is applicable to Kubernetes clusters deployed on bare metal servers, as well as "raw" VMs where Kubernetes was installed manually, using generic Linux distros (like CentOS, Ubuntu...)

For quick testing, you can use a NodePort. This should work on almost every cluster, but it will typically use a port in the range 30000-32767.


kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.0/deploy/static/provider/baremetal/deploy.yaml


For more information about bare metal deployments (and how to use port 80 instead of a random port in the 30000-32767 range), see bare-metal considerations.
```



```shell
# 创建文件夹
[root@k8s-master1 ingress]# pwd
/k8s/module/ingress


[root@k8s-master1 ingress]# wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/baremetal/deploy.yaml
Connecting to raw.githubusercontent.com (185.199.108.133:443)
deploy.yaml          100% |******************************************************************************| 19299   0:00:00 ETA


# kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/baremetal/deploy.yaml
[root@k8s-master1 ingress]# kubectl apply -f deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created


# 查看nginx-ingress-controller控制器
[root@k8s-master1 ingress]# kubectl get pod -n ingress-nginx -o wide
NAME                                        READY   STATUS              RESTARTS   AGE   IP               NODE        NOMINATED NODE   READINESS GATES
ingress-nginx-admission-create-d77f7        0/1     ErrImagePull        0          23m   10.244.36.67     k8s-node1   <none>           <none>
ingress-nginx-admission-patch-245h9         0/1     ErrImagePull        0          23m   10.244.169.131   k8s-node2   <none>           <none>
ingress-nginx-controller-69db7f75b4-ws8x9   0/1     ContainerCreating   0          23m   <none>           k8s-node3   <none>           <none>


# 此种方式无法拉取镜像
```

```shell
# 问题解决


# 无法拉取镜像
[root@k8s-master1 ingress]#  kubectl get pod -n ingress-nginx -o wide
NAME                                        READY   STATUS              RESTARTS   AGE   IP               NODE        NOMINATED NODE   READINESS GATES
ingress-nginx-admission-create-bcd7x        0/1     ImagePullBackOff    0          18m   10.244.122.95    k8s-node4   <none>           <none>
ingress-nginx-admission-patch-hkp57         0/1     ErrImagePull        0          18m   10.244.107.228   k8s-node3   <none>           <none>
ingress-nginx-controller-65c4f84996-crknk   0/1     ContainerCreating   0          18m   <none>           k8s-node2   <none>           <none>


# 需要拉取的镜像
k8s.gcr.io/ingress-nginx/controller:v1.0.0@sha256:0851b34f69f69352bf168e6ccf30e1e20714a264ab1ecd1933e4d8c0fc3215c6

k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.0@sha256:f3b6b39a6062328c095337b4cadcefd1612348fdd5190b1dcbcb9b9e90bd8068


#修改镜像地址
	image: k8s.gcr.io/ingress-nginx/controller:v1.0.0  -> https://hub.docker.com/r/willdockerhub/ingress-nginx-controller
	docker pull willdockerhub/ingress-nginx-controller:v1.0.0
	
	image: k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.0 -> https://hub.docker.com/r/jettech/kube-webhook-certgen/tags
	docker pull jettech/kube-webhook-certgen:v1.0.0
	
	
# 修改文件
# 修改镜像地址：
# willdockerhub/ingress-nginx-controller:v1.0.0
# jettech/kube-webhook-certgen:v1.0.0
[root@k8s-master1 ingress]# vim deploy.yaml
```

```shell
# 创新创建

[root@k8s-master1 ingress]# kubectl apply -f deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created


[root@k8s-master1 ingress]# kubectl get all -n ingress-nginx
NAME                                            READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-hhhbp        0/1     Completed   0          57s
pod/ingress-nginx-admission-patch-nd458         0/1     Completed   0          57s
pod/ingress-nginx-controller-7d4df87d89-7gjmc   1/1     Running     0          57s

NAME                                         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.1.253.108   <none>        80:31100/TCP,443:31826/TCP   57s
service/ingress-nginx-controller-admission   ClusterIP   10.1.236.3     <none>        443/TCP                      57s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           57s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-7d4df87d89   1         1         1       57s

NAME                                       COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   1/1           2s         57s
job.batch/ingress-nginx-admission-patch    1/1           2s         57s



# 查看service规则
[root@k8s-master1 ingress]# kubectl get svc -n ingress-nginx
NAME                                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             NodePort    10.1.253.108   <none>        80:31100/TCP,443:31826/TCP   83s
ingress-nginx-controller-admission   ClusterIP   10.1.236.3     <none>        443/TCP                      83s
```



```shell
# 问题
[root@k8s-master ingress]#  kubectl apply -f ingress-http.yaml
Error from server (InternalError): error when creating "ingress-http.yaml": Internal error occurred: failed calling webhook "validate.nginx.ingress.kubernetes.io": Post "https://ingress-nginx-controller-admission.ingress-nginx.svc:443/networking/v1/ingresses?timeout=10s": dial tcp 10.100.142.101:443: connect: connection refused

# 解决方式
解决方案：
最后参考下面的文章解决此问题
使用下面的命令查看 webhook
kubectl get validatingwebhookconfigurations
ingress-nginx-admission

删除ingress-nginx-admission
kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission

https://blog.csdn.net/qq_39218530/article/details/115372879
```





## 三、重要组件



### 1.Harbor



#### 1.1.安装 docker-compose

```shell
#下载源码
curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-Linux-x86_64 -o /usr/local/bin/docker-compose


#给docker-compose添加执行权限
sudo chmod +x /usr/local/bin/docker-compose

#查看docker-compose是否安装成功
docker-compose -version
```



#### 2.2.安装Harbor

```shell
# 1.下载Harbor的压缩包
链接：https://pan.baidu.com/s/1W0eawaqMmq3ijx-jvrQqXQ  提取码：acby


# 2.解压
/k8s/harbor
 
# tar -xzf harbor-offline-installer-v2.1.0.tgz
# cd harbor
# cp harbor.yml.tmpl harbor.yml
 
 
# 3.修改配置文件
vi harbor.yml
 
hostname: 172.51.216.88 
port: 8888 
harbor_admin_password：默认admin 密码  Harbor12345
 
注释https
#https:
  # https port for harbor, default is 443
  #port: 443
  # The path of cert and key files for nginx
  #certificate: /your/certificate/path
  #private_key: /your/private/key/path
 
 
# 4.安装Harbor
./prepare
# 安装，并开启 hlem charts 功能
./install.sh  --with-chartmuseum

#./install.sh

 
# 5.启动Harbor
docker-compose up -d 启动
docker-compose stop 停止
docker-compose restart 重新启动
 
```

K8S节点配置docker私有镜像源（worker节点）

```shell
# 6. 配置docker私有镜像源（worker节点）
 
#把Harbor地址加入到Docker信任列表， 注意：worker节点都需要修改配置

vi /etc/docker/daemon.json
"insecure-registries": ["http://IP:端口"]

{
   "registry-mirrors": ["https://gcctk8ld.mirror.aliyuncs.com"],
   "insecure-registries": ["http://172.51.216.88:8888"]
}


#重启Docker
systemctl daemon-reload
systemctl restart docker

```

登录

```shell
# 7.登陆harbor

# docker login -u 用户名 -p 密码 http://IP:端口
docker login -u admin -p admin http://172.51.216.88:8888
 
# 登出：
docker logout http://172.51.216.88:8888


# 外网地址：
http://172.51.216.88:8888
admin
admin
```

![](md\module\harbor-1.png)



#### 2.3.harbor 作为 charts 仓库

**此步操作略**

- **用 Harbor 管理 Helm Charts，并开启 hlem charts 功能**

```shell
# 安装，并开启 hlem charts 功能
./install.sh  --with-chartmuseum


[root@dev harbor]# ./install.sh  --with-chartmuseum
......
[Step 5]: starting Harbor ...
Creating network "harbor_harbor" with the default driver
Creating network "harbor_harbor-chartmuseum" with the default driver
Creating harbor-log ... done
Creating redis         ... done
Creating registry      ... done
Creating harbor-db     ... done
Creating registryctl   ... done
Creating chartmuseum   ... done
Creating harbor-portal ... done
Creating harbor-core   ... done
Creating nginx             ... done
Creating harbor-jobservice ... done
✔ ----Harbor has been installed and started successfully.----

```



- 安装 push 插件

```shell
# master(172.51.216.81)，安装helm服务器
# 下载太慢，如果有这个文件我们也可以直接拷贝到如下目录里：
/root/.cache/helm/plugins/https-github.com-chartmuseum-helm-push


# 安装
helm plugin install https://github.com/chartmuseum/helm-push 
# 查看已成功
helm plugin list



# 安装
[root@k8s-master ~]# helm plugin install https://github.com/chartmuseum/helm-push

# 查看已成功
[root@k8s-master ~]# helm plugin list
NAME   	VERSION	DESCRIPTION                      
cm-push	0.10.0 	Push chart package to ChartMuseum

```

![](md\module\harbor-2.png)



#### 2.4.控制harbor服务

启动和重启

```shell
Harbor 的日常运维管理是通过docker-compose来完成的，Harbor本身有多个服务进程，都放在docker容器之中运行，我们可以通过docker ps命令查看。 

# 暂停Harbor
docker-compose pause

# 停止Harbor
docker-compose stop
docker-compose down -v

# 开启harbor服务
docker-compose start
 
# 重启Harbor
docker-compose up -d
```

```shell
# 进入目录
[root@dev harbor]# cd /k8s/harbor/harbor
[root@dev harbor]# pwd
/k8s/harbor/harbor


# 停止Harbor
[root@dev harbor]# docker-compose stop
Stopping nginx             ... done
Stopping harbor-jobservice ... done
Stopping harbor-core       ... done
Stopping harbor-portal     ... done
Stopping registry          ... done
Stopping chartmuseum       ... done
Stopping harbor-db         ... done
Stopping registryctl       ... done
Stopping redis             ... done
Stopping harbor-log        ... done


# 开启harbor服务
[root@dev harbor]# docker-compose start
Starting log         ... done
Starting registry    ... done
Starting registryctl ... done
Starting postgresql  ... done
Starting portal      ... done
Starting redis       ... done
Starting core        ... done
Starting jobservice  ... done
Starting proxy       ... done
Starting chartmuseum ... done

```



#### 2.5.Helm仓库

##### 2.5.1.安装 push 插件

Helm服务器安装helm-push插件

```shell
# master(172.51.216.81，172.51.216.82，172.51.216.83)，安装helm服务器

# 安装
helm plugin install https://github.com/chartmuseum/helm-push 
# 查看已成功
helm plugin list



# 安装
[root@k8s-master ~]# helm plugin install https://github.com/chartmuseum/helm-push

# 查看已成功
[root@k8s-master helm]# helm plugin list
NAME   	VERSION	DESCRIPTION                      
cm-push	0.10.1 	Push chart package to ChartMuseum
```

**本地安装**

```shell
# 下载 helm-push_0.10.1_linux_amd64.tar.gz
https://github.com/chartmuseum/helm-push/releases/download/v0.10.1/helm-push_0.10.1_linux_amd64.tar.gz


# 解压
[root@k8s-master2 helm]# tar -zxf helm-push_0.10.1_linux_amd64.tar.gz 
[root@k8s-master2 helm]# ll
total 23636
drwxr-xr-x. 2 root root         26 Dec 23 10:51 bin
-rw-r--r--. 1 root root   10481411 Dec 23 10:51 helm-push_0.10.1_linux_amd64.tar.gz
-rw-r--r--. 1 1001 docker    11357 Oct 12 22:09 LICENSE
-rw-r--r--. 1 1001 docker      407 Oct 12 22:09 plugin.yaml


# 安装
[root@k8s-master2 helm]# helm plugin install .
sh: scripts/install_plugin.sh: No such file or directory
Error: plugin install hook for "cm-push" exited with error


# 测试
[root@k8s-master2 helm]# helm cm-push --help
Helm plugin to push chart package to ChartMuseum

Examples:

  $ helm cm-push mychart-0.1.0.tgz chartmuseum       # push .tgz from "helm package"
  $ helm cm-push . chartmuseum                       # package and push chart directory
  $ helm cm-push . --version="1.2.3" chartmuseum     # override version in Chart.yaml
  $ helm cm-push . https://my.chart.repo.com         # push directly to chart repo URL

Usage:
  helm cm-push [flags]

Flags:
      --access-token string             Send token in Authorization header [$HELM_REPO_ACCESS_TOKEN]
  -a, --app-version string              Override app version pre-push
      --auth-header string              Alternative header to use for token auth [$HELM_REPO_AUTH_HEADER]
      --ca-file string                  Verify certificates of HTTPS-enabled servers using this CA bundle [$HELM_REPO_CA_FILE]
      --cert-file string                Identify HTTPS client using this SSL certificate file [$HELM_REPO_CERT_FILE]
      --check-helm-version              outputs either "2" or "3" indicating the current Helm major version
      --context-path string             ChartMuseum context path [$HELM_REPO_CONTEXT_PATH]
      --debug                           Enable verbose output
  -d, --dependency-update               update dependencies from "requirements.yaml" to dir "charts/" before packaging
  -f, --force                           Force upload even if chart version exists
  -h, --help                            help for helm
      --home string                     Location of your Helm config. Overrides $HELM_HOME (default "/root/.helm")
      --host string                     Address of Tiller. Overrides $HELM_HOST
      --insecure                        Connect to server with an insecure way by skipping certificate verification [$HELM_REPO_INSECURE]
      --key-file string                 Identify HTTPS client using this SSL key file [$HELM_REPO_KEY_FILE]
      --keyring string                  location of a public keyring (default "/root/.gnupg/pubring.gpg")
      --kube-context string             Name of the kubeconfig context to use
      --kubeconfig string               Absolute path of the kubeconfig file to be used
  -p, --password string                 Override HTTP basic auth password [$HELM_REPO_PASSWORD]
      --tiller-connection-timeout int   The duration (in seconds) Helm will wait to establish a connection to Tiller (default 300)
      --tiller-namespace string         Namespace of Tiller (default "kube-system")
  -t, --timeout int                     The duration (in seconds) Helm will wait to get response from chartmuseum (default 30)
  -u, --username string                 Override HTTP basic auth username [$HELM_REPO_USERNAME]
  -v, --version string                  Override chart version pre-push
```



##### 2.5.2.添加Helm仓库

```shell
# 添加 helm repo
helm repo add --username admin --password admin harborcloud http://172.51.216.88:8888/chartrepo/springcloud
helm repo list


# 查看
[root@k8s-master1 chartmuseum]# helm repo list
NAME         	URL                                                   
stable       	http://mirror.azure.cn/kubernetes/charts              
aliyun       	https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
bitnami      	https://charts.bitnami.com/bitnami                    
ingress-nginx	https://kubernetes.github.io/ingress-nginx            
elastic      	https://helm.elastic.co                               
presslabs    	https://presslabs.github.io/charts                    
timescale    	https://charts.timescale.com


# 添加 helm repo
[root@k8s-master chartmuseum]# helm repo add --username admin --password admin harborcloud http://172.51.216.88:8888/chartrepo/springcloud
"harborcloud" has been added to your repositories

[root@k8s-master1 test]# helm repo list
NAME         	URL                                                   
stable       	http://mirror.azure.cn/kubernetes/charts              
aliyun       	https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
bitnami      	https://charts.bitnami.com/bitnami                    
ingress-nginx	https://kubernetes.github.io/ingress-nginx            
elastic      	https://helm.elastic.co                               
presslabs    	https://presslabs.github.io/charts                    
timescale    	https://charts.timescale.com                          
chartmuseum  	http://172.51.216.88:9999                             
harborcloud  	http://172.51.216.88:8888/chartrepo/springcloud


# 更新
[root@k8s-master chartmuseum]# helm repo update
```



##### 2.5.3.上传Helm仓库

```shell
$ helm cm-push mychart/ chartmuseum
Pushing mychart-0.3.2.tgz to chartmuseum...
Done.


$ helm cm-push mychart/ --version="1.2.3" chartmuseum
Pushing mychart-1.2.3.tgz to chartmuseum...
Done.


$ helm cm-push mychart-0.3.2.tgz chartmuseum
Pushing mychart-0.3.2.tgz to chartmuseum...
Done.
```



```shell
# 推送

[root@k8s-master1 test]# helm cm-push cloud/ harborcloud
Pushing cloud-1.0.0.tgz to harborcloud...
Done.



[root@k8s-master1 test]# helm cm-push cloud-1.0.0.tgz harborcloud
Pushing cloud-1.0.0.tgz to harborcloud...
Done.
```



![](md\module\harbor-11.png)

![](md\module\harbor-12.png)

![](md\module\harbor-13.png)





### 2.Rook

#### 2.1.磁盘规划

为4台服务器（k8s-node1、k8s-node2、k8s-node3、k8s-node4）分别增加200G裸盘

查看磁盘

```shell
[root@k8s-node1 ~]# lsblk -f
NAME            FSTYPE      LABEL           UUID                                   MOUNTPOINT
sr0             iso9660     CentOS 7 x86_64 2020-11-04-11-36-43-00                 
vda                                                                                
├─vda1          xfs                         80853249-f7a8-43f7-91dc-820d70c300cc   /boot
└─vda2          LVM2_member                 Jth3iR-YpQG-cwff-2pBY-goGN-yl26-b9AZ2n 
  ├─centos-root xfs                         a1c8d6f7-61ba-4af5-bde9-175afd2093d0   /
  ├─centos-swap swap                        ff17871a-3e49-4d75-8c33-f252532cde1b   
  └─centos-home xfs                         5262049d-e810-4073-b542-55639c43158e   /home
vdb                                                                                


[root@k8s-node1 ~]# lsblk
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sr0              11:0    1   4.4G  0 rom  
vda             252:0    0   300G  0 disk 
├─vda1          252:1    0     1G  0 part /boot
└─vda2          252:2    0   299G  0 part 
  ├─centos-root 253:0    0    50G  0 lvm  /
  ├─centos-swap 253:1    0   7.9G  0 lvm  
  └─centos-home 253:2    0 241.1G  0 lvm  /home
vdb             252:16   0   200G  0 disk 


# vdb是200G裸盘
```



#### 2.2.安装步骤

```powershell
git clone --single-branch --branch release-1.7 https://github.com/rook/rook.git

cd rook/cluster/examples/kubernetes/ceph

kubectl create -f crds.yaml -f common.yaml -f operator.yaml

kubectl create -f cluster.yaml
```



#### 2.2.下载源码

```powershell
# git clone --single-branch --branch v1.6.5 https://github.com/rook/rook.git


[root@k8s-master rook]# git clone --single-branch --branch v1.6.5 https://github.com/rook/rook.git
Cloning into 'rook'...
remote: Enumerating objects: 64346, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 64346 (delta 0), reused 17 (delta 0), pack-reused 64329
Receiving objects: 100% (64346/64346), 37.89 MiB | 1.76 MiB/s, done.
Resolving deltas: 100% (45134/45134), done.
Note: checking out '2ee204712b854921ea6dc018ffb151e58bc714e7'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name



# cd rook/cluster/examples/kubernetes/ceph
[root@k8s-master rook]# cd rook/cluster/examples/kubernetes/ceph
[root@k8s-master ceph]# ll
total 896
-rw-r--r-- 1 root root    436 Nov 16 15:47 ceph-client.yaml
-rw-r--r-- 1 root root   1059 Nov 16 15:47 cluster-external-management.yaml
-rw-r--r-- 1 root root   1242 Nov 16 15:47 cluster-external.yaml
-rw-r--r-- 1 root root   8151 Nov 16 15:47 cluster-on-pvc.yaml
-rw-r--r-- 1 root root   3230 Nov 16 15:47 cluster-stretched.yaml
-rw-r--r-- 1 root root   1411 Nov 16 15:47 cluster-test.yaml
-rw-r--r-- 1 root root  14105 Nov 16 15:47 cluster.yaml
-rw-r--r-- 1 root root   2341 Nov 16 15:47 common-external.yaml
-rw-r--r-- 1 root root   2723 Nov 16 15:47 common-second-cluster.yaml
-rw-r--r-- 1 root root  30170 Nov 16 15:47 common.yaml
-rw-r--r-- 1 root root 561795 Nov 16 15:47 crds.yaml
-rw-r--r-- 1 root root  55487 Nov 16 15:47 create-external-cluster-resources.py
-rw-r--r-- 1 root root   3615 Nov 16 15:47 create-external-cluster-resources.sh
drwxr-xr-x 5 root root     47 Nov 16 15:47 csi
-rw-r--r-- 1 root root    411 Nov 16 15:47 dashboard-external-https.yaml
-rw-r--r-- 1 root root    410 Nov 16 15:47 dashboard-external-http.yaml
-rw-r--r-- 1 root root    954 Nov 16 15:47 dashboard-ingress-https.yaml
-rw-r--r-- 1 root root    413 Nov 16 15:47 dashboard-loadbalancer.yaml
-rw-r--r-- 1 root root   1789 Nov 16 15:47 direct-mount.yaml
-rw-r--r-- 1 root root   3432 Nov 16 15:47 filesystem-ec.yaml
-rw-r--r-- 1 root root   1215 Nov 16 15:47 filesystem-mirror.yaml
-rw-r--r-- 1 root root    777 Nov 16 15:47 filesystem-test.yaml
-rw-r--r-- 1 root root   4463 Nov 16 15:47 filesystem.yaml
drwxr-xr-x 2 root root    115 Nov 16 15:47 flex
-rw-r--r-- 1 root root   4977 Nov 16 15:47 import-external-cluster.sh
drwxr-xr-x 2 root root   4096 Nov 16 15:47 monitoring
-rw-r--r-- 1 root root    745 Nov 16 15:47 nfs-test.yaml
-rw-r--r-- 1 root root   1832 Nov 16 15:47 nfs.yaml
-rw-r--r-- 1 root root    587 Nov 16 15:47 object-bucket-claim-delete.yaml
-rw-r--r-- 1 root root    587 Nov 16 15:47 object-bucket-claim-retain.yaml
-rw-r--r-- 1 root root   3669 Nov 16 15:47 object-ec.yaml
-rw-r--r-- 1 root root    814 Nov 16 15:47 object-external.yaml
-rw-r--r-- 1 root root   1804 Nov 16 15:47 object-multisite-pull-realm.yaml
-rw-r--r-- 1 root root   1282 Nov 16 15:47 object-multisite.yaml
-rw-r--r-- 1 root root   5917 Nov 16 15:47 object-openshift.yaml
-rw-r--r-- 1 root root    685 Nov 16 15:47 object-test.yaml
-rw-r--r-- 1 root root    508 Nov 16 15:47 object-user.yaml
-rw-r--r-- 1 root root   5521 Nov 16 15:47 object.yaml
-rw-r--r-- 1 root root  23928 Nov 16 15:47 operator-openshift.yaml
-rw-r--r-- 1 root root  22587 Nov 16 15:47 operator.yaml
-rw-r--r-- 1 root root   2652 Nov 16 15:47 osd-purge.yaml
-rw-r--r-- 1 root root   1100 Nov 16 15:47 pool-ec.yaml
-rw-r--r-- 1 root root    504 Nov 16 15:47 pool-test.yaml
-rw-r--r-- 1 root root   3313 Nov 16 15:47 pool.yaml
drwxr-xr-x 2 root root     23 Nov 16 15:47 pre-k8s-1.16
-rw-r--r-- 1 root root   1456 Nov 16 15:47 rbdmirror.yaml
-rw-r--r-- 1 root root    525 Nov 16 15:47 rgw-external.yaml
-rw-r--r-- 1 root root   2558 Nov 16 15:47 scc.yaml
-rw-r--r-- 1 root root    737 Nov 16 15:47 storageclass-bucket-delete.yaml
-rw-r--r-- 1 root root    736 Nov 16 15:47 storageclass-bucket-retain.yaml
drwxr-xr-x 2 root root     29 Nov 16 15:47 test-data
-rw-r--r-- 1 root root   1738 Nov 16 15:47 toolbox-job.yaml
-rw-r--r-- 1 root root   1474 Nov 16 15:47 toolbox.yaml
```



#### 2.3.部署Rook Operator

**1.修改配置**

由于国内环境无法Pull官方镜像，所以要修改默认镜像地址，改为阿里云镜像仓库(registry.aliyuncs.com/it00021hot是我的镜像地址，利用github action每天定时同步官方镜像，所有版本的镜像都有)

**operator.yaml**

```powershell
[root@k8s-master ceph]# vim operator.yaml


#  原来默认配置

 75   # ROOK_CSI_CEPH_IMAGE: "quay.io/cephcsi/cephcsi:v3.3.1"
 76   # ROOK_CSI_REGISTRAR_IMAGE: "k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.0.1"
 77   # ROOK_CSI_RESIZER_IMAGE: "k8s.gcr.io/sig-storage/csi-resizer:v1.0.1"
 78   # ROOK_CSI_PROVISIONER_IMAGE: "k8s.gcr.io/sig-storage/csi-provisioner:v2.0.4"
 79   # ROOK_CSI_SNAPSHOTTER_IMAGE: "k8s.gcr.io/sig-storage/csi-snapshotter:v4.0.0"
 80   # ROOK_CSI_ATTACHER_IMAGE: "k8s.gcr.io/sig-storage/csi-attacher:v3.0.2"


# 修改后

  ROOK_CSI_CEPH_IMAGE: "registry.aliyuncs.com/it00021hot/cephcsi:v3.3.1"
  ROOK_CSI_REGISTRAR_IMAGE: "registry.aliyuncs.com/it00021hot/csi-node-driver-registrar:v2.0.1"
  ROOK_CSI_RESIZER_IMAGE: "registry.aliyuncs.com/it00021hot/csi-resizer:v1.0.1"
  ROOK_CSI_PROVISIONER_IMAGE: "registry.aliyuncs.com/it00021hot/csi-provisioner:v2.0.4"
  ROOK_CSI_SNAPSHOTTER_IMAGE: "registry.aliyuncs.com/it00021hot/csi-snapshotter:v4.0.0"
  ROOK_CSI_ATTACHER_IMAGE: "registry.aliyuncs.com/it00021hot/csi-attacher:v3.0.2"



# 开启自动发现磁盘（用于后期扩展）
  ROOK_ENABLE_DISCOVERY_DAEMON: "true"
```



**2.创建Operator**

```powershell
# kubectl create -f crds.yaml -f common.yaml -f operator.yaml

[root@k8s-master ceph]# kubectl create -f crds.yaml -f common.yaml -f operator.yaml
customresourcedefinition.apiextensions.k8s.io/cephblockpools.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephclients.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephclusters.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephfilesystemmirrors.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephfilesystems.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephnfses.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephobjectrealms.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephobjectstores.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephobjectstoreusers.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephobjectzonegroups.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephobjectzones.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/cephrbdmirrors.ceph.rook.io created
customresourcedefinition.apiextensions.k8s.io/objectbucketclaims.objectbucket.io created
customresourcedefinition.apiextensions.k8s.io/objectbuckets.objectbucket.io created
customresourcedefinition.apiextensions.k8s.io/volumereplicationclasses.replication.storage.openshift.io created
customresourcedefinition.apiextensions.k8s.io/volumereplications.replication.storage.openshift.io created
customresourcedefinition.apiextensions.k8s.io/volumes.rook.io created
namespace/rook-ceph created
clusterrolebinding.rbac.authorization.k8s.io/rook-ceph-object-bucket created
serviceaccount/rook-ceph-admission-controller created
clusterrole.rbac.authorization.k8s.io/rook-ceph-admission-controller-role created
clusterrolebinding.rbac.authorization.k8s.io/rook-ceph-admission-controller-rolebinding created
clusterrole.rbac.authorization.k8s.io/rook-ceph-cluster-mgmt created
role.rbac.authorization.k8s.io/rook-ceph-system created
clusterrole.rbac.authorization.k8s.io/rook-ceph-global created
clusterrole.rbac.authorization.k8s.io/rook-ceph-mgr-cluster created
clusterrole.rbac.authorization.k8s.io/rook-ceph-object-bucket created
serviceaccount/rook-ceph-system created
rolebinding.rbac.authorization.k8s.io/rook-ceph-system created
clusterrolebinding.rbac.authorization.k8s.io/rook-ceph-global created
serviceaccount/rook-ceph-osd created
serviceaccount/rook-ceph-mgr created
serviceaccount/rook-ceph-cmd-reporter created
role.rbac.authorization.k8s.io/rook-ceph-osd created
clusterrole.rbac.authorization.k8s.io/rook-ceph-osd created
clusterrole.rbac.authorization.k8s.io/rook-ceph-mgr-system created
role.rbac.authorization.k8s.io/rook-ceph-mgr created
role.rbac.authorization.k8s.io/rook-ceph-cmd-reporter created
rolebinding.rbac.authorization.k8s.io/rook-ceph-cluster-mgmt created
rolebinding.rbac.authorization.k8s.io/rook-ceph-osd created
rolebinding.rbac.authorization.k8s.io/rook-ceph-mgr created
rolebinding.rbac.authorization.k8s.io/rook-ceph-mgr-system created
clusterrolebinding.rbac.authorization.k8s.io/rook-ceph-mgr-cluster created
clusterrolebinding.rbac.authorization.k8s.io/rook-ceph-osd created
rolebinding.rbac.authorization.k8s.io/rook-ceph-cmd-reporter created
podsecuritypolicy.policy/00-rook-privileged created
clusterrole.rbac.authorization.k8s.io/psp:rook created
clusterrolebinding.rbac.authorization.k8s.io/rook-ceph-system-psp created
rolebinding.rbac.authorization.k8s.io/rook-ceph-default-psp created
rolebinding.rbac.authorization.k8s.io/rook-ceph-osd-psp created
rolebinding.rbac.authorization.k8s.io/rook-ceph-mgr-psp created
rolebinding.rbac.authorization.k8s.io/rook-ceph-cmd-reporter-psp created
serviceaccount/rook-csi-cephfs-plugin-sa created
serviceaccount/rook-csi-cephfs-provisioner-sa created
role.rbac.authorization.k8s.io/cephfs-external-provisioner-cfg created
rolebinding.rbac.authorization.k8s.io/cephfs-csi-provisioner-role-cfg created
clusterrole.rbac.authorization.k8s.io/cephfs-csi-nodeplugin created
clusterrole.rbac.authorization.k8s.io/cephfs-external-provisioner-runner created
clusterrolebinding.rbac.authorization.k8s.io/rook-csi-cephfs-plugin-sa-psp created
clusterrolebinding.rbac.authorization.k8s.io/rook-csi-cephfs-provisioner-sa-psp created
clusterrolebinding.rbac.authorization.k8s.io/cephfs-csi-nodeplugin created
clusterrolebinding.rbac.authorization.k8s.io/cephfs-csi-provisioner-role created
serviceaccount/rook-csi-rbd-plugin-sa created
serviceaccount/rook-csi-rbd-provisioner-sa created
role.rbac.authorization.k8s.io/rbd-external-provisioner-cfg created
rolebinding.rbac.authorization.k8s.io/rbd-csi-provisioner-role-cfg created
clusterrole.rbac.authorization.k8s.io/rbd-csi-nodeplugin created
clusterrole.rbac.authorization.k8s.io/rbd-external-provisioner-runner created
clusterrolebinding.rbac.authorization.k8s.io/rook-csi-rbd-plugin-sa-psp created
clusterrolebinding.rbac.authorization.k8s.io/rook-csi-rbd-provisioner-sa-psp created
clusterrolebinding.rbac.authorization.k8s.io/rbd-csi-nodeplugin created
clusterrolebinding.rbac.authorization.k8s.io/rbd-csi-provisioner-role created
configmap/rook-ceph-operator-config created
deployment.apps/rook-ceph-operator created



[root@k8s-master1 ceph]# kubectl get all -n rook-ceph
NAME                                     READY   STATUS    RESTARTS   AGE
pod/rook-ceph-operator-5b94b79f5-tvtf8   1/1     Running   0          10m
pod/rook-discover-2n9px                  1/1     Running   0          5m52s
pod/rook-discover-542w9                  1/1     Running   0          5m52s
pod/rook-discover-nzgtp                  1/1     Running   0          5m52s
pod/rook-discover-qm64s                  1/1     Running   0          5m52s

NAME                           DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/rook-discover   4         4         4       4            4           <none>          5m52s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/rook-ceph-operator   1/1     1            1           10m

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/rook-ceph-operator-5b94b79f5   1         1         1       10m
```



#### 2.4.创建Ceph Cluster

**cluster.yaml默认配置不修改**

```powershell
# kubectl create -f cluster.yaml

[root@k8s-master ceph]# kubectl create -f cluster.yaml
cephcluster.ceph.rook.io/rook-ceph created


# 实时查看pod创建进度
kubectl get pod -n rook-ceph -w
# 实时查看集群创建进度
kubectl get cephcluster -n rook-ceph rook-ceph -w
# 详细描述
kubectl describe cephcluster -n rook-ceph rook-ceph




[root@k8s-master1 ceph]# kubectl get all -n rook-ceph
NAME                                                      READY   STATUS      RESTARTS   AGE
pod/csi-cephfsplugin-2gqgj                                3/3     Running     0          35m
pod/csi-cephfsplugin-6n5cr                                3/3     Running     0          35m
pod/csi-cephfsplugin-jsqxq                                3/3     Running     0          35m
pod/csi-cephfsplugin-mf5vz                                3/3     Running     0          35m
pod/csi-cephfsplugin-provisioner-575f74897d-mdsqg         6/6     Running     0          35m
pod/csi-cephfsplugin-provisioner-575f74897d-n5vxd         6/6     Running     0          35m
pod/csi-rbdplugin-8v984                                   3/3     Running     0          35m
pod/csi-rbdplugin-j868t                                   3/3     Running     0          35m
pod/csi-rbdplugin-ln5kf                                   3/3     Running     0          35m
pod/csi-rbdplugin-provisioner-8576bbbbc7-s8pmg            6/6     Running     0          35m
pod/csi-rbdplugin-provisioner-8576bbbbc7-xbd48            6/6     Running     0          35m
pod/csi-rbdplugin-wkg68                                   3/3     Running     0          35m
pod/rook-ceph-crashcollector-k8s-node1-7b85799995-9knfh   1/1     Running     0          24m
pod/rook-ceph-crashcollector-k8s-node2-5c6dd78c8-8rvdx    1/1     Running     0          24m
pod/rook-ceph-crashcollector-k8s-node3-864f9745b9-c8rwg   1/1     Running     0          24m
pod/rook-ceph-crashcollector-k8s-node4-64df8c7dc6-tpl5h   1/1     Running     0          24m
pod/rook-ceph-mgr-a-649b594c6b-8hkv9                      1/1     Running     0          24m
pod/rook-ceph-mon-a-6db6589c4b-gxlgl                      1/1     Running     0          31m
pod/rook-ceph-mon-b-84789c4d67-zqlnc                      1/1     Running     0          28m
pod/rook-ceph-mon-c-5765fd46d4-qhm7j                      1/1     Running     0          25m
pod/rook-ceph-operator-5b94b79f5-tvtf8                    1/1     Running     0          49m
pod/rook-ceph-osd-0-59cff4d448-5tmwd                      1/1     Running     0          24m
pod/rook-ceph-osd-1-c89d9c79-682lv                        1/1     Running     0          24m
pod/rook-ceph-osd-2-589c5c677c-7nddz                      1/1     Running     0          24m
pod/rook-ceph-osd-3-75457f8dd9-ht4sf                      1/1     Running     0          24m
pod/rook-ceph-osd-prepare-k8s-node1-zdwxl                 0/1     Completed   0          24m
pod/rook-ceph-osd-prepare-k8s-node2-kstlz                 0/1     Completed   0          24m
pod/rook-ceph-osd-prepare-k8s-node3-74khx                 0/1     Completed   0          24m
pod/rook-ceph-osd-prepare-k8s-node4-nxd5x                 0/1     Completed   0          24m
pod/rook-discover-2n9px                                   1/1     Running     0          45m
pod/rook-discover-542w9                                   1/1     Running     0          45m
pod/rook-discover-nzgtp                                   1/1     Running     0          45m
pod/rook-discover-qm64s                                   1/1     Running     0          45m

NAME                               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
service/csi-cephfsplugin-metrics   ClusterIP   10.1.251.118   <none>        8080/TCP,8081/TCP   35m
service/csi-rbdplugin-metrics      ClusterIP   10.1.181.21    <none>        8080/TCP,8081/TCP   35m
service/rook-ceph-mgr              ClusterIP   10.1.78.114    <none>        9283/TCP            24m
service/rook-ceph-mgr-dashboard    ClusterIP   10.1.61.67     <none>        8443/TCP            24m
service/rook-ceph-mon-a            ClusterIP   10.1.154.144   <none>        6789/TCP,3300/TCP   31m
service/rook-ceph-mon-b            ClusterIP   10.1.191.249   <none>        6789/TCP,3300/TCP   28m
service/rook-ceph-mon-c            ClusterIP   10.1.79.47     <none>        6789/TCP,3300/TCP   25m

NAME                              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/csi-cephfsplugin   4         4         4       4            4           <none>          35m
daemonset.apps/csi-rbdplugin      4         4         4       4            4           <none>          35m
daemonset.apps/rook-discover      4         4         4       4            4           <none>          45m

NAME                                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/csi-cephfsplugin-provisioner         2/2     2            2           35m
deployment.apps/csi-rbdplugin-provisioner            2/2     2            2           35m
deployment.apps/rook-ceph-crashcollector-k8s-node1   1/1     1            1           24m
deployment.apps/rook-ceph-crashcollector-k8s-node2   1/1     1            1           24m
deployment.apps/rook-ceph-crashcollector-k8s-node3   1/1     1            1           24m
deployment.apps/rook-ceph-crashcollector-k8s-node4   1/1     1            1           24m
deployment.apps/rook-ceph-mgr-a                      1/1     1            1           24m
deployment.apps/rook-ceph-mon-a                      1/1     1            1           31m
deployment.apps/rook-ceph-mon-b                      1/1     1            1           28m
deployment.apps/rook-ceph-mon-c                      1/1     1            1           25m
deployment.apps/rook-ceph-operator                   1/1     1            1           49m
deployment.apps/rook-ceph-osd-0                      1/1     1            1           24m
deployment.apps/rook-ceph-osd-1                      1/1     1            1           24m
deployment.apps/rook-ceph-osd-2                      1/1     1            1           24m
deployment.apps/rook-ceph-osd-3                      1/1     1            1           24m

NAME                                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/csi-cephfsplugin-provisioner-575f74897d         2         2         2       35m
replicaset.apps/csi-rbdplugin-provisioner-8576bbbbc7            2         2         2       35m
replicaset.apps/rook-ceph-crashcollector-k8s-node1-7b85799995   1         1         1       24m
replicaset.apps/rook-ceph-crashcollector-k8s-node2-5c6dd78c8    1         1         1       24m
replicaset.apps/rook-ceph-crashcollector-k8s-node3-864f9745b9   1         1         1       24m
replicaset.apps/rook-ceph-crashcollector-k8s-node3-fc65d5787    0         0         0       24m
replicaset.apps/rook-ceph-crashcollector-k8s-node4-64df8c7dc6   1         1         1       24m
replicaset.apps/rook-ceph-mgr-a-649b594c6b                      1         1         1       24m
replicaset.apps/rook-ceph-mon-a-6db6589c4b                      1         1         1       31m
replicaset.apps/rook-ceph-mon-b-84789c4d67                      1         1         1       28m
replicaset.apps/rook-ceph-mon-c-5765fd46d4                      1         1         1       25m
replicaset.apps/rook-ceph-operator-5b94b79f5                    1         1         1       49m
replicaset.apps/rook-ceph-osd-0-59cff4d448                      1         1         1       24m
replicaset.apps/rook-ceph-osd-1-c89d9c79                        1         1         1       24m
replicaset.apps/rook-ceph-osd-2-589c5c677c                      1         1         1       24m
replicaset.apps/rook-ceph-osd-3-75457f8dd9                      1         1         1       24m

NAME                                        COMPLETIONS   DURATION   AGE
job.batch/rook-ceph-osd-prepare-k8s-node1   1/1           4s         24m
job.batch/rook-ceph-osd-prepare-k8s-node2   1/1           4s         24m
job.batch/rook-ceph-osd-prepare-k8s-node3   1/1           4s         24m
job.batch/rook-ceph-osd-prepare-k8s-node4   1/1           4s         24m



# 查看日志
[root@k8s-master ~]# kubectl logs -f rook-ceph-operator-5b94b79f5-72jw9 -n rook-ceph

```



#### 2.5.安装Toolbox

```powershell
# 安装toolbox工具
kubectl create -f toolbox.yaml

[root@k8s-master ceph]# kubectl create -f toolbox.yaml
deployment.apps/rook-ceph-tools created


- 进入工作目录
cd /root/rook/cluster/examples/kubernetes/ceph
- 创建toolbox
kubectl  create -f toolbox.yaml -n rook-ceph
- 查看pod
kubectl  get pod -n rook-ceph -l app=rook-ceph-tools
- 进入pod
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- bash
- 查看集群状态
ceph status
- 查看osd状态
ceph osd status
- 集群空间用量
ceph df

ceph status
ceph osd status
ceph df
rados df



[root@k8s-master1 ceph]# kubectl  get pod -n rook-ceph -l app=rook-ceph-tools
NAME                               READY   STATUS    RESTARTS   AGE
rook-ceph-tools-6bc7c4f9fc-s5k87   1/1     Running   0          42s



[root@k8s-master ~]# kubectl -n rook-ceph exec -it rook-ceph-tools-6bc7c4f9fc-p5j59 -- bash
[root@k8s-master ~]# kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- bash
[root@rook-ceph-tools-6bc7c4f9fc-s5k87 /]# ceph status
  cluster:
    id:     0baad0d4-c9b0-4be3-a3be-e0c779394658
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum a,c,b (age 27m)
    mgr: a(active, since 27m)
    osd: 4 osds: 4 up (since 27m), 4 in (since 27m)
 
  data:
    pools:   1 pools, 1 pgs
    objects: 0 objects, 0 B
    usage:   4.0 GiB used, 796 GiB / 800 GiB avail
    pgs:     1 active+clean
 


[root@rook-ceph-tools-6bc7c4f9fc-p5j59 /]# ceph osd status
ID  HOST        USED  AVAIL  WR OPS  WR DATA  RD OPS  RD DATA  STATE      
 0  k8s-node3  1026M  98.9G      0        0       0        0   exists,up  
 1  k8s-node2  1026M  98.9G      0        0       0        0   exists,up  
 2  k8s-node1  1026M  98.9G      0        0       0        0   exists,up 

 
[root@rook-ceph-tools-6bc7c4f9fc-s5k87 /]# ceph df
--- RAW STORAGE ---
CLASS  SIZE     AVAIL    USED     RAW USED  %RAW USED
hdd    800 GiB  796 GiB  9.8 MiB   4.0 GiB       0.50
TOTAL  800 GiB  796 GiB  9.8 MiB   4.0 GiB       0.50
 
--- POOLS ---
POOL                   ID  PGS  STORED  OBJECTS  USED  %USED  MAX AVAIL
device_health_metrics   1    1     0 B        0   0 B      0    252 GiB


[root@rook-ceph-tools-6bc7c4f9fc-s5k87 /]# ceph df
--- RAW STORAGE ---
CLASS  SIZE     AVAIL    USED     RAW USED  %RAW USED
hdd    800 GiB  796 GiB  9.8 MiB   4.0 GiB       0.50
TOTAL  800 GiB  796 GiB  9.8 MiB   4.0 GiB       0.50
 
--- POOLS ---
POOL                   ID  PGS  STORED  OBJECTS  USED  %USED  MAX AVAIL
device_health_metrics   1    1     0 B        0   0 B      0    252 GiB


[root@rook-ceph-tools-6bc7c4f9fc-s5k87 /]# rados df
POOL_NAME              USED  OBJECTS  CLONES  COPIES  MISSING_ON_PRIMARY  UNFOUND  DEGRADED  RD_OPS   RD  WR_OPS   WR  USED COMPR  UNDER COMPR
device_health_metrics   0 B        0       0       0                   0        0         0       0  0 B       0  0 B         0 B          0 B

total_objects    0
total_used       4.0 GiB
total_avail      796 GiB
total_space      800 GiB


[root@rook-ceph-tools-6bc7c4f9fc-s5k87 /]# ceph health detail
HEALTH_OK
```



#### 2.6.Dashboard

Ceph 有一个 Dashboard 工具，我们可以在上面查看集群的状态，包括总体运行状态，mgr、osd 和其他 Ceph 进程的状态，查看池和 PG 状态，以及显示守护进程的日志等等。

Dashboard 服务，如果在集群内部我们可以通过 DNS 名称 http://rook-ceph-mgr-dashboard.rook-ceph:7000 或者 CluterIP http://10.109.8.98:7000 来进行访问。

但是如果要在集群外部进行访问的话，我们就需要通过 Ingress 或者 NodePort 类型的 Service 来暴露了，为了方便测试我们这里创建一个新的 NodePort 类型的服务来访问 Dashboard，资源清单如下所示：（dashboard-external-http.yaml）

通过 `http://ip:port`就可以访问到 Dashboard 了，

Rook 创建了一个默认的用户 admin，并在运行 Rook 的命名空间中生成了一个名为 rook-ceph-dashboard-admin-password 的 Secret，要获取密码，可以运行以下命令：

```shell
kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo


[root@k8s-master ~]# kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo
Bl8c"Ab3+6-Q?N()~agu
```



**默认配置实ssl，只能通过HTTPS访问，不能通过HTTP访问。**



**1.HTTPS**

rook-dashboard-https.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: rook-ceph-mgr-dashboard-https
  namespace: rook-ceph
  labels:
    app: rook-ceph-mgr
    rook_cluster: rook-ceph
spec:
  ports:
  - name: dashboard
    port: 8443
    nodePort: 32700
    protocol: TCP
    targetPort: 8443
  selector:
    app: rook-ceph-mgr
    rook_cluster: rook-ceph
  sessionAffinity: None
  type: NodePort



# 创建
kubectl apply -f rook-dashboard-https.yaml


[root@k8s-master1 rook]# kubectl get svc -n rook-ceph
NAME                            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
rook-ceph-mgr-dashboard         ClusterIP   10.1.61.67     <none>        8443/TCP            35m
rook-ceph-mgr-dashboard-https   NodePort    10.1.236.73    <none>        8443:32700/TCP      11s



# 访问地址：
https://172.51.216.81:32700/
admin
Bl8c"Ab3+6-Q?N()~agu

# 修改密码： 1qaz2wsx
```



![](md\module\rook-1.png)

![](md\module\rook-2.png)



#### 2.7.块存储（RBD）

**创建CephBlockPool和StorageClass**

```yaml
vim storageclass.yaml
-------------------------------------------
 
apiVersion: ceph.rook.io/v1
kind: CephBlockPool
metadata:
  name: replicapool
  namespace: rook-ceph
spec:
  failureDomain: host # host级容灾
  replicated:
    size: 3           # 默认三个副本
    requireSafeReplicaSize: true  # 强制高可用，如果size为1则需改为false
 
---
apiVersion: storage.k8s.io/v1
kind: StorageClass                       # sc无需指定命名空间
metadata:
  name: rook-ceph-block
provisioner: rook-ceph.rbd.csi.ceph.com   # 存储驱动
parameters:
  clusterID: rook-ceph # namespace:cluster
  pool: replicapool                       # 关联到CephBlockPool
  imageFormat: "2"
  imageFeatures: layering
 
  csi.storage.k8s.io/provisioner-secret-name: rook-csi-rbd-provisioner
  csi.storage.k8s.io/provisioner-secret-namespace: rook-ceph # namespace:cluster
  csi.storage.k8s.io/controller-expand-secret-name: rook-csi-rbd-provisioner
  csi.storage.k8s.io/controller-expand-secret-namespace: rook-ceph # namespace:cluster
  csi.storage.k8s.io/node-stage-secret-name: rook-csi-rbd-node
  csi.storage.k8s.io/node-stage-secret-namespace: rook-ceph # namespace:cluster
  
  csi.storage.k8s.io/fstype: ext4   
 
allowVolumeExpansion: true               # 是否允许扩容
reclaimPolicy: Delete                    # PV回收策略
```



```powershell
# 创建CephBlockPool和StorageClass

[root@k8s-master1 rook]# kubectl apply -f storageclass.yaml
cephblockpool.ceph.rook.io/replicapool created
storageclass.storage.k8s.io/rook-ceph-block created



# 查看sc
[root@k8s-master1 rook]# kubectl get sc
NAME              PROVISIONER                  RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
rook-ceph-block   rook-ceph.rbd.csi.ceph.com   Delete          Immediate           true                   30s


# 查看CephBlockPool（也可在dashboard中查看）
[root@k8s-master1 rook]# kubectl get cephblockpools -n rook-ceph
NAME          AGE
replicapool   58s
```

![](md\module\rook-3.png)



#### 2.8.共享文件存储（CephFS）

**创建filesystem和StorageClass**

创建Cephfs文件系统需要先部署MDS服务，该服务负责处理文件系统中的元数据。



**1.部署MDS**

```yaml
vim filesystem.yaml
---------------------------------------


apiVersion: ceph.rook.io/v1
kind: CephFilesystem
metadata:
  name: myfs
  namespace: rook-ceph 
spec:
  metadataPool:
    replicated:
      size: 3                         # 元数据副本数
      requireSafeReplicaSize: true
    parameters:
      compression_mode:
        none
  dataPools:
    - failureDomain: host
      replicated:
        size: 3                     # 存储数据的副本数
        requireSafeReplicaSize: true
      parameters:
        compression_mode:
          none
  preserveFilesystemOnDelete: true
  metadataServer:
    activeCount: 3                # MDS实例的副本数，默认1，生产环境建议设置为3
```

```shell
[root@k8s-master1 rook]# kubectl apply -f filesystem.yaml
cephfilesystem.ceph.rook.io/myfs created


[root@k8s-master1 rook]# kubectl get CephFilesystem -n rook-ceph
NAME   ACTIVEMDS   AGE   PHASE
myfs   3           22s   Ready


[root@k8s-master1 rook]# kubectl -n rook-ceph get pod -l app=rook-ceph-mds
NAME                                    READY   STATUS    RESTARTS   AGE
rook-ceph-mds-myfs-a-65d54cb647-mvk9z   1/1     Running   0          29s
rook-ceph-mds-myfs-b-5d9db94db9-rzcwv   1/1     Running   0          28s
rook-ceph-mds-myfs-c-58fb4f6b6c-zq6sx   1/1     Running   0          27s
rook-ceph-mds-myfs-d-64f895bd7c-87dv4   1/1     Running   0          27s
rook-ceph-mds-myfs-e-78c85876c9-sjsfl   1/1     Running   0          26s
rook-ceph-mds-myfs-f-54749fbf49-xczts   1/1     Running   0          25s



[root@rook-ceph-tools-6bc7c4f9fc-p5j59 /]# ceph status
...
  services:
    mds: myfs:3 {0=myfs-b=up:active,1=myfs-a=up:active,2=myfs-e=up:active} 3 up:standby
    ...
```



**2.创建StorageClass**

**直接部署`/k8s/rook/rook/cluster/examples/kubernetes/ceph/csi/cephfs/storageclass.yaml`**

```yaml
vim storageclass.yaml 
----------------------------------


apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rook-cephfs
provisioner: rook-ceph.cephfs.csi.ceph.com # driver:namespace:operator
parameters:
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rook-cephfs
provisioner: rook-ceph.cephfs.csi.ceph.com # driver:namespace:operator
parameters:
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: rook-cephfs
provisioner: rook-ceph.cephfs.csi.ceph.com # driver:namespace:operator
parameters:
  # clusterID is the namespace where operator is deployed.
  clusterID: rook-ceph # namespace:cluster

  # CephFS filesystem name into which the volume shall be created
  fsName: myfs

  # Ceph pool into which the volume shall be created
  # Required for provisionVolume: "true"
  pool: myfs-data0

  # The secrets contain Ceph admin credentials. These are generated automatically by the operator
  # in the same namespace as the cluster.
  csi.storage.k8s.io/provisioner-secret-name: rook-csi-cephfs-provisioner
  csi.storage.k8s.io/provisioner-secret-namespace: rook-ceph # namespace:cluster
  csi.storage.k8s.io/controller-expand-secret-name: rook-csi-cephfs-provisioner
  csi.storage.k8s.io/controller-expand-secret-namespace: rook-ceph # namespace:cluster
  csi.storage.k8s.io/node-stage-secret-name: rook-csi-cephfs-node
  csi.storage.k8s.io/node-stage-secret-namespace: rook-ceph # namespace:cluster

  # (optional) The driver can use either ceph-fuse (fuse) or ceph kernel client (kernel)
  # If omitted, default volume mounter will be used - this is determined by probing for ceph-fuse
  # or by setting the default mounter explicitly via --volumemounter command-line argument.
  # mounter: kernel
reclaimPolicy: Delete
allowVolumeExpansion: true
mountOptions:
  # uncomment the following line for debugging
  #- debug
```



```powershell
[root@k8s-master1 rook]# pwd
/k8s/module/rook
[root@k8s-master1 rook]# ll
total 3420
drwxrwxr-x. 11 root root    4096 Jun 11  2021 rook



[root@k8s-master1 rook]# kubectl apply -f rook/cluster/examples/kubernetes/ceph/csi/cephfs/storageclass.yaml
storageclass.storage.k8s.io/rook-cephfs created


[root@k8s-master1 rook]# kubectl get sc
NAME              PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
rook-ceph-block   rook-ceph.rbd.csi.ceph.com      Delete          Immediate           true                   12m
rook-cephfs       rook-ceph.cephfs.csi.ceph.com   Delete          Immediate           true                   71s
```

![](md\module\rook-4.png)



### 3.Helm

#### 3.1.安装Helm

安装过程

```shell
wget https://get.helm.sh/helm-v3.6.3-linux-amd64.tar.gz
#wget https://get.helm.sh/helm-v3.7.0-linux-amd64.tar.gz

tar -zxvf helm-v3.6.3-linux-amd64.tar.gz

cp linux-amd64/helm /usr/local/bin/

helm help
```

```shell
# 三台master分别安装


# 下载安装文件
[root@k8s-master install]# wget https://get.helm.sh/helm-v3.6.3-linux-amd64.tar.gz


# 解压
[root@k8s-master1 helm]# tar -zxvf helm-v3.6.3-linux-amd64.tar.gz 
linux-amd64/
linux-amd64/helm
linux-amd64/LICENSE
linux-amd64/README.md

[root@k8s-master install]# tree
.
├── helm-v3.6.3-linux-amd64.tar.gz 
└── linux-amd64
    ├── helm
    ├── LICENSE
    └── README.md


[root@k8s-master install]# cd linux-amd64/
[root@k8s-master linux-amd64]# ls
helm  LICENSE  README.md


# 复制
[root@k8s-master linux-arm64]# cp linux-amd64/helm /usr/local/bin/

# 命令补全
# 添加环境变量
[root@k8s-master linux-amd64]# echo "source <(helm completion bash)" >> ~/.bashrc
[root@k8s-master linux-amd64]# source ~/.bashrc


# 查看版本
[root@k8s-master1 ~]# helm version
version.BuildInfo{Version:"v3.6.3", GitCommit:"d506314abfb5d21419df8c7e7e68012379db2354", GitTreeState:"clean", GoVersion:"go1.16.5"}
```



```shell
[root@k8s-master linux-amd64]# helm

[root@k8s-master linux-amd64]# helm help
The Kubernetes package manager

Common actions for Helm:

- helm search:    search for charts
- helm pull:      download a chart to your local directory to view
- helm install:   upload the chart to Kubernetes
- helm list:      list releases of charts

Environment variables:

| Name                               | Description                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------|
| $HELM_CACHE_HOME                   | set an alternative location for storing cached files.                             |
| $HELM_CONFIG_HOME                  | set an alternative location for storing Helm configuration.                       |
| $HELM_DATA_HOME                    | set an alternative location for storing Helm data.                                |
| $HELM_DEBUG                        | indicate whether or not Helm is running in Debug mode                             |
| $HELM_DRIVER                       | set the backend storage driver. Values are: configmap, secret, memory, sql.       |
| $HELM_DRIVER_SQL_CONNECTION_STRING | set the connection string the SQL storage driver should use.                      |
| $HELM_MAX_HISTORY                  | set the maximum number of helm release history.                                   |
| $HELM_NAMESPACE                    | set the namespace used for the helm operations.                                   |
| $HELM_NO_PLUGINS                   | disable plugins. Set HELM_NO_PLUGINS=1 to disable plugins.                        |
| $HELM_PLUGINS                      | set the path to the plugins directory                                             |
| $HELM_REGISTRY_CONFIG              | set the path to the registry config file.                                         |
| $HELM_REPOSITORY_CACHE             | set the path to the repository cache directory                                    |
| $HELM_REPOSITORY_CONFIG            | set the path to the repositories file.                                            |
| $KUBECONFIG                        | set an alternative Kubernetes configuration file (default "~/.kube/config")       |
| $HELM_KUBEAPISERVER                | set the Kubernetes API Server Endpoint for authentication                         |
| $HELM_KUBECAFILE                   | set the Kubernetes certificate authority file.                                    |
| $HELM_KUBEASGROUPS                 | set the Groups to use for impersonation using a comma-separated list.             |
| $HELM_KUBEASUSER                   | set the Username to impersonate for the operation.                                |
| $HELM_KUBECONTEXT                  | set the name of the kubeconfig context.                                           |
| $HELM_KUBETOKEN                    | set the Bearer KubeToken used for authentication.                                 |

Helm stores cache, configuration, and data based on the following configuration order:

- If a HELM_*_HOME environment variable is set, it will be used
- Otherwise, on systems supporting the XDG base directory specification, the XDG variables will be used
- When no other location is set a default location will be used based on the operating system

By default, the default directories depend on the Operating System. The defaults are listed below:

| Operating System | Cache Path                | Configuration Path             | Data Path               |
|------------------|---------------------------|--------------------------------|-------------------------|
| Linux            | $HOME/.cache/helm         | $HOME/.config/helm             | $HOME/.local/share/helm |
| macOS            | $HOME/Library/Caches/helm | $HOME/Library/Preferences/helm | $HOME/Library/helm      |
| Windows          | %TEMP%\helm               | %APPDATA%\helm                 | %APPDATA%\helm          |

Usage:
  helm [command]

Available Commands:
  completion  generate autocompletion scripts for the specified shell
  create      create a new chart with the given name
  dependency  manage a chart's dependencies
  env         helm client environment information
  get         download extended information of a named release
  help        Help about any command
  history     fetch release history
  install     install a chart
  lint        examine a chart for possible issues
  list        list releases
  package     package a chart directory into a chart archive
  plugin      install, list, or uninstall Helm plugins
  pull        download a chart from a repository and (optionally) unpack it in local directory
  repo        add, list, remove, update, and index chart repositories
  rollback    roll back a release to a previous revision
  search      search for a keyword in charts
  show        show information of a chart
  status      display the status of the named release
  template    locally render templates
  test        run tests for a release
  uninstall   uninstall a release
  upgrade     upgrade a release
  verify      verify that a chart at the given path has been signed and is valid
  version     print the client version information

Flags:
      --debug                       enable verbose output
  -h, --help                        help for helm
      --kube-apiserver string       the address and the port for the Kubernetes API server
      --kube-as-group stringArray   group to impersonate for the operation, this flag can be repeated to specify multiple groups.
      --kube-as-user string         username to impersonate for the operation
      --kube-ca-file string         the certificate authority file for the Kubernetes API server connection
      --kube-context string         name of the kubeconfig context to use
      --kube-token string           bearer token used for authentication
      --kubeconfig string           path to the kubeconfig file
  -n, --namespace string            namespace scope for this request
      --registry-config string      path to the registry config file (default "/root/.config/helm/registry.json")
      --repository-cache string     path to the file containing cached repository indexes (default "/root/.cache/helm/repository")
      --repository-config string    path to the file containing repository names and URLs (default "/root/.config/helm/repositories.yaml")

Use "helm [command] --help" for more information about a command.
```



#### 3.2.Chart仓库

- 微软仓库（http://mirror.azure.cn/kubernetes/charts/）这个仓库强烈推荐，基本上官网有的chart这里都有。
- 阿里云仓库（https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts ）
- 官方仓库（https://hub.kubeapps.com/charts/incubator）官方chart仓库，国内有点不好使。

```shell
# 添加存储库
helm repo add stable http://mirror.azure.cn/kubernetes/charts
helm repo add aliyun https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts 
helm repo update

# 查看配置的存储库
helm repo list 
NAME    URL                                                   
stable  http://mirror.azure.cn/kubernetes/charts              
aliyun  https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

# helm  search  repo  aliyun

# 删除存储库
helm repo remove aliyun
```



```shell
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
$ helm repo update # Make sure we get the latest list of charts
$ helm repo add ali-stable    https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts  #阿里云


#  helm repo list
NAME            URL                                                       
ingress-nginx   https://kubernetes.github.io/ingress-nginx                
stable          https://kubernetes-charts.storage.googleapis.com/         
bitnami         https://charts.bitnami.com/bitnami                        
incubator       https://kubernetes-charts-incubator.storage.googleapis.com/
```



```shell
# 添加仓库
[root@k8s-master1 ~]# helm repo add stable http://mirror.azure.cn/kubernetes/charts
"stable" has been added to your repositories
[root@k8s-master1 ~]# helm repo add aliyun https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts 
"aliyun" has been added to your repositories
[root@k8s-master1 ~]# helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
[root@k8s-master1 ~]# helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
"ingress-nginx" has been added to your repositories
[root@k8s-master1 ~]# helm repo add  elastic    https://helm.elastic.co
"elastic" has been added to your repositories


# 更新
[root@k8s-master1 ~]# helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "elastic" chart repository
...Successfully got an update from the "aliyun" chart repository
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "bitnami" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈


[root@k8s-master1 ~]# helm repo list
NAME         	URL                                                   
stable       	http://mirror.azure.cn/kubernetes/charts              
aliyun       	https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
bitnami      	https://charts.bitnami.com/bitnami                    
ingress-nginx	https://kubernetes.github.io/ingress-nginx            
elastic      	https://helm.elastic.co  
```



### 4.ChartMuseum

#### 4.1.安装ChartMuseum

直接使用最简单的 docker run 方式，使用local 本地存储方式，通过 -v 映射到宿主机 /k8s/chartmuseum/charts
更多支持安装方式见官网

```shell
# 官方参考

https://chartmuseum.com/
https://github.com/helm/chartmuseum
https://github.com/chartmuseum/helm-push
https://github.com/chartmuseum/ui
```



```shell
# 搭建服务器：172.51.216.88


# 创建目录
[root@localhost k8s]# mkdir /k8s/chartmuseum/charts -p



# 运行容器
docker run -d --name chartmuseum-charts --restart=always \
-p 9999:8080 \
-e DEBUG=1 \
-e STORAGE=local \
-e STORAGE_LOCAL_ROOTDIR=/charts \
-v /k8s/chartmuseum/charts:/charts \
ghcr.io/helm/chartmuseum:v0.13.1
```

**注意：安装Harbor时，自动安装了一个chartmuseum，要注意区分。**



#### 4.2.测试

```shell
# 使用 curl 测试下接口，没有报错就行，当前仓库内容还是空的
[root@dev charts]# curl localhost:9999/api/charts
{}


[root@dev charts]# curl localhost:9999/api/charts |jq
-bash: jq: command not found
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100     2  100     2    0     0    358      0 --:--:-- --:--:-- --:--:--   400
(23) Failed writing body


# 访问地址
http://172.51.216.85:9999
```



#### 4.3.安装 push 插件

Helm服务器安装helm-push插件

```shell
# master(172.51.216.81，172.51.216.82，172.51.216.83)，安装helm服务器

# 安装
helm plugin install https://github.com/chartmuseum/helm-push 
# 查看已成功
helm plugin list



# 安装
[root@k8s-master ~]# helm plugin install https://github.com/chartmuseum/helm-push

# 查看已成功
[root@k8s-master helm]# helm plugin list
NAME   	VERSION	DESCRIPTION                      
cm-push	0.10.1 	Push chart package to ChartMuseum
```

**本地安装**

```shell
# 下载 helm-push_0.10.1_linux_amd64.tar.gz
https://github.com/chartmuseum/helm-push/releases/download/v0.10.1/helm-push_0.10.1_linux_amd64.tar.gz


# 解压
[root@k8s-master2 helm]# tar -zxf helm-push_0.10.1_linux_amd64.tar.gz 
[root@k8s-master2 helm]# ll
total 23636
drwxr-xr-x. 2 root root         26 Dec 23 10:51 bin
-rw-r--r--. 1 root root   10481411 Dec 23 10:51 helm-push_0.10.1_linux_amd64.tar.gz
-rw-r--r--. 1 1001 docker    11357 Oct 12 22:09 LICENSE
-rw-r--r--. 1 1001 docker      407 Oct 12 22:09 plugin.yaml


# 安装
[root@k8s-master2 helm]# helm plugin install .
sh: scripts/install_plugin.sh: No such file or directory
Error: plugin install hook for "cm-push" exited with error


# 测试
[root@k8s-master2 helm]# helm cm-push --help
Helm plugin to push chart package to ChartMuseum

Examples:

  $ helm cm-push mychart-0.1.0.tgz chartmuseum       # push .tgz from "helm package"
  $ helm cm-push . chartmuseum                       # package and push chart directory
  $ helm cm-push . --version="1.2.3" chartmuseum     # override version in Chart.yaml
  $ helm cm-push . https://my.chart.repo.com         # push directly to chart repo URL

Usage:
  helm cm-push [flags]

Flags:
      --access-token string             Send token in Authorization header [$HELM_REPO_ACCESS_TOKEN]
  -a, --app-version string              Override app version pre-push
      --auth-header string              Alternative header to use for token auth [$HELM_REPO_AUTH_HEADER]
      --ca-file string                  Verify certificates of HTTPS-enabled servers using this CA bundle [$HELM_REPO_CA_FILE]
      --cert-file string                Identify HTTPS client using this SSL certificate file [$HELM_REPO_CERT_FILE]
      --check-helm-version              outputs either "2" or "3" indicating the current Helm major version
      --context-path string             ChartMuseum context path [$HELM_REPO_CONTEXT_PATH]
      --debug                           Enable verbose output
  -d, --dependency-update               update dependencies from "requirements.yaml" to dir "charts/" before packaging
  -f, --force                           Force upload even if chart version exists
  -h, --help                            help for helm
      --home string                     Location of your Helm config. Overrides $HELM_HOME (default "/root/.helm")
      --host string                     Address of Tiller. Overrides $HELM_HOST
      --insecure                        Connect to server with an insecure way by skipping certificate verification [$HELM_REPO_INSECURE]
      --key-file string                 Identify HTTPS client using this SSL key file [$HELM_REPO_KEY_FILE]
      --keyring string                  location of a public keyring (default "/root/.gnupg/pubring.gpg")
      --kube-context string             Name of the kubeconfig context to use
      --kubeconfig string               Absolute path of the kubeconfig file to be used
  -p, --password string                 Override HTTP basic auth password [$HELM_REPO_PASSWORD]
      --tiller-connection-timeout int   The duration (in seconds) Helm will wait to establish a connection to Tiller (default 300)
      --tiller-namespace string         Namespace of Tiller (default "kube-system")
  -t, --timeout int                     The duration (in seconds) Helm will wait to get response from chartmuseum (default 30)
  -u, --username string                 Override HTTP basic auth username [$HELM_REPO_USERNAME]
  -v, --version string                  Override chart version pre-push
```



#### 4.4.添加Helm仓库

```shell
# 添加 helm repo
helm repo add chartmuseum http://172.51.216.88:9999
helm repo list


# 查看
[root@k8s-master1 chartmuseum]# helm repo list
NAME         	URL                                                   
stable       	http://mirror.azure.cn/kubernetes/charts              
aliyun       	https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
bitnami      	https://charts.bitnami.com/bitnami                    
ingress-nginx	https://kubernetes.github.io/ingress-nginx            
elastic      	https://helm.elastic.co                               
presslabs    	https://presslabs.github.io/charts                    
timescale    	https://charts.timescale.com


# 添加 helm repo
[root@k8s-master chartmuseum]# helm repo add chartmuseum http://172.51.216.88:9999
"chartmuseum" has been added to your repositories

[root@k8s-master1 chartmuseum]# helm repo list
NAME         	URL                                                   
stable       	http://mirror.azure.cn/kubernetes/charts              
aliyun       	https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
bitnami      	https://charts.bitnami.com/bitnami                    
ingress-nginx	https://kubernetes.github.io/ingress-nginx            
elastic      	https://helm.elastic.co                               
presslabs    	https://presslabs.github.io/charts                    
timescale    	https://charts.timescale.com                          
chartmuseum  	http://172.51.216.88:9999    


# 更新
[root@k8s-master chartmuseum]# helm repo update
```



#### 4.5.上传Helm仓库

```shell
$ helm cm-push mychart/ chartmuseum
Pushing mychart-0.3.2.tgz to chartmuseum...
Done.


$ helm cm-push mychart/ --version="1.2.3" chartmuseum
Pushing mychart-1.2.3.tgz to chartmuseum...
Done.


$ helm cm-push mychart-0.3.2.tgz chartmuseum
Pushing mychart-0.3.2.tgz to chartmuseum...
Done.
```



```shell
# 推送失败

[root@k8s-master1 test]# helm cm-push cloud/ chartmuseum
Pushing cloud-1.0.0.tgz to chartmuseum...
Error: 500: open /charts/cloud-1.0.0.tgz: permission denied
Usage:
  helm cm-push [flags]

Flags:
      --access-token string             Send token in Authorization header [$HELM_REPO_ACCESS_TOKEN]
  -a, --app-version string              Override app version pre-push
      --auth-header string              Alternative header to use for token auth [$HELM_REPO_AUTH_HEADER]
      --ca-file string                  Verify certificates of HTTPS-enabled servers using this CA bundle [$HELM_REPO_CA_FILE]
      --cert-file string                Identify HTTPS client using this SSL certificate file [$HELM_REPO_CERT_FILE]
      --check-helm-version              outputs either "2" or "3" indicating the current Helm major version
      --context-path string             ChartMuseum context path [$HELM_REPO_CONTEXT_PATH]
      --debug                           Enable verbose output
  -d, --dependency-update               update dependencies from "requirements.yaml" to dir "charts/" before packaging
  -f, --force                           Force upload even if chart version exists
  -h, --help                            help for helm
      --home string                     Location of your Helm config. Overrides $HELM_HOME (default "/root/.helm")
      --host string                     Address of Tiller. Overrides $HELM_HOST
      --insecure                        Connect to server with an insecure way by skipping certificate verification [$HELM_REPO_INSECURE]
      --key-file string                 Identify HTTPS client using this SSL key file [$HELM_REPO_KEY_FILE]
      --keyring string                  location of a public keyring (default "/root/.gnupg/pubring.gpg")
      --kube-context string             Name of the kubeconfig context to use
      --kubeconfig string               Absolute path of the kubeconfig file to be used
  -p, --password string                 Override HTTP basic auth password [$HELM_REPO_PASSWORD]
      --tiller-connection-timeout int   The duration (in seconds) Helm will wait to establish a connection to Tiller (default 300)
      --tiller-namespace string         Namespace of Tiller (default "kube-system")
  -t, --timeout int                     The duration (in seconds) Helm will wait to get response from chartmuseum (default 30)
  -u, --username string                 Override HTTP basic auth username [$HELM_REPO_USERNAME]
  -v, --version string                  Override chart version pre-push

Error: plugin "cm-push" exited with error




[root@k8s-master1 test]# helm cm-push cloud-1.0.0.tgz chartmuseum
Pushing cloud-1.0.0.tgz to chartmuseum...
Error: 500: open /charts/cloud-1.0.0.tgz: permission denied
Usage:
  helm cm-push [flags]

Flags:
      --access-token string             Send token in Authorization header [$HELM_REPO_ACCESS_TOKEN]
  -a, --app-version string              Override app version pre-push
      --auth-header string              Alternative header to use for token auth [$HELM_REPO_AUTH_HEADER]
      --ca-file string                  Verify certificates of HTTPS-enabled servers using this CA bundle [$HELM_REPO_CA_FILE]
      --cert-file string                Identify HTTPS client using this SSL certificate file [$HELM_REPO_CERT_FILE]
      --check-helm-version              outputs either "2" or "3" indicating the current Helm major version
      --context-path string             ChartMuseum context path [$HELM_REPO_CONTEXT_PATH]
      --debug                           Enable verbose output
  -d, --dependency-update               update dependencies from "requirements.yaml" to dir "charts/" before packaging
  -f, --force                           Force upload even if chart version exists
  -h, --help                            help for helm
      --home string                     Location of your Helm config. Overrides $HELM_HOME (default "/root/.helm")
      --host string                     Address of Tiller. Overrides $HELM_HOST
      --insecure                        Connect to server with an insecure way by skipping certificate verification [$HELM_REPO_INSECURE]
      --key-file string                 Identify HTTPS client using this SSL key file [$HELM_REPO_KEY_FILE]
      --keyring string                  location of a public keyring (default "/root/.gnupg/pubring.gpg")
      --kube-context string             Name of the kubeconfig context to use
      --kubeconfig string               Absolute path of the kubeconfig file to be used
  -p, --password string                 Override HTTP basic auth password [$HELM_REPO_PASSWORD]
      --tiller-connection-timeout int   The duration (in seconds) Helm will wait to establish a connection to Tiller (default 300)
      --tiller-namespace string         Namespace of Tiller (default "kube-system")
  -t, --timeout int                     The duration (in seconds) Helm will wait to get response from chartmuseum (default 30)
  -u, --username string                 Override HTTP basic auth username [$HELM_REPO_USERNAME]
  -v, --version string                  Override chart version pre-push

Error: plugin "cm-push" exited with error
```

```shell
# 推送失败


https://www.jianshu.com/p/18478cf7a37f
https://blog.csdn.net/sinat_28371057/article/details/109892977
```















