* TOC
{:toc}





## 一、生产环境地址





### 1.dashboard

```shell
https://172.51.216.81:30443


token:
eyJhbGciOiJSUzI1NiIsImtpZCI6ImVjQTZKRUd2U1BBZTRDcm1jd0ZzMHpvTGQxNVZSb3AtcmFOQkRPTi14N1kifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLXB4NDVoIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI3YzE3YjMwNS02ZjA2LTQwNzMtYjliZC04OTgwZDJmN2JiMWUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.iigCoOapTAY34INm-XK5luJuBto_a0SLB3yAI2kdKoChGK6XcJUWxQ_kHUVbmoTQeewZAPt_2TPdYun46OkD17gkMgaklT74gudMctcmcU_uoeb9MR2jQR_nxTO0vG72YkI3mmftkB9U8IA5QINl4eE9rUwnCP7YUEZ8DBqraiQ5Fn2vJL-IBSRgdUgcevU2W44khXg5AG1_EXLkMle0_8qkr-cJgp-mNvUVkRUt_PiWGF4ty14V2R4QyTysbDmu-CDFVSibDDzOgc1KQK0r9vtQf-GAXzFJ-GMzmm71GrjrularONiLbby9OvmFjh5PQ9b0Iv3BKoS-tEAi4dh89g
```



![](/images/user-guide/device-profile/device-profile-rule-chain-1-pe.png)



![image](/images/user-guide/device-profile/device-profile-queue-1-pe.png)



{% capture difference-test %}

**信息提示！**<br>
**Note that the configuration of the Modbus connector has changed since Gateway 3.0. The new configuration will be 
generated after installing the new version and running Gateway in the new_modbus.json file.**  
{% endcapture %}
{% include templates/info-banner.md content=difference-test %}





```shell
# 测试地址

# zipkin
http://zipkin.pro.com:31356/zipkin
#sentinel
http://sentinel.pro.com:31356/#/login
账户密码：sentinel/sentinel
# xxl-job
http://xxljob.pro.com:31356/xxl-job-admin
账户密码：admin/123456
# eureka
http://eureka.pro.com:31356/

# gateway
http://gateway.pro.com:31356/dconsumer/hello
http://gateway.pro.com:31356/dconsumer/say
http://gateway.pro.com:31356/dproducer/hello
http://gateway.pro.com:31356/dproducer/say
```
{: .copy-code}



展开



<details>   <summary>标题</summary>   此处可书写文本   嗯，是可以书写文本的 </details>



<details>   <summary>折叠代码块</summary>   <pre><code>       System.out.println("虽然可以折叠代码块");      System.out.println("但是代码无法高亮");   </code></pre> </details>



<details>   <summary>折叠代码块</summary>  
<pre><blockcode>       
System.out.println("虽然可以折叠代码块");      
System.out.println("但是代码无法高亮");   
</blockcode></pre> </details>



<details>
    <summary>折叠代码块</summary>

```shell
# 访问地址：
https://172.51.216.81:32700/
admin
Bl8c"Ab3+6-Q?N()~agu

# 修改密码： 1qaz2wsx
```

</details>





<details>
    <summary>折叠代码块</summary>

```html
<form #editEntityForm="ngForm" [formGroup]="editEntityFormGroup"
      (ngSubmit)="save()"  class="edit-entity-form">
    <mat-toolbar fxLayout="row" color="primary">
        <h2>Edit  </h2>
        <span fxFlex></span>
        <button mat-icon-button (click)="cancel()" type="button">
            <mat-icon class="material-icons">close</mat-icon>
        </button>
    </mat-toolbar>
    <mat-progress-bar color="warn" mode="indeterminate" *ngIf="isLoading$ | async">
    </mat-progress-bar>
    <div style="height: 4px;" *ngIf="!(isLoading$ | async)"></div>
    <div mat-dialog-content fxLayout="column">
        <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
            <mat-form-field fxFlex class="mat-block">
                <mat-label>Entity Name</mat-label>
                <input matInput formControlName="entityName" required readonly="">
            </mat-form-field>
            <mat-form-field fxFlex class="mat-block">
                <mat-label>Entity Label</mat-label>
                <input matInput formControlName="entityLabel">
            </mat-form-field>
        </div>
        <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
            <mat-form-field fxFlex class="mat-block">
                <mat-label>Entity Type</mat-label>
                <input matInput formControlName="entityType" readonly>
            </mat-form-field>
            <mat-form-field fxFlex class="mat-block">
                <mat-label>Type</mat-label>
                <input matInput formControlName="type" readonly>
            </mat-form-field>
        </div>
        <div formGroupName="attributes" fxLayout="column">
            <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
                <mat-form-field fxFlex class="mat-block">
                    <mat-label>Latitude</mat-label>
                    <input type="number" step="any" matInput formControlName="latitude">
                </mat-form-field>
                <mat-form-field fxFlex class="mat-block">
                    <mat-label>Longitude</mat-label>
                    <input type="number" step="any" matInput formControlName="longitude">
                </mat-form-field>
            </div>
            <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
                <mat-form-field fxFlex class="mat-block">
                    <mat-label>Address</mat-label>
                    <input matInput formControlName="address">
                </mat-form-field>
                <mat-form-field fxFlex class="mat-block">
                    <mat-label>Owner</mat-label>
                    <input matInput formControlName="owner">
                </mat-form-field>
            </div>
            <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
                <mat-form-field fxFlex class="mat-block">
                    <mat-label>Integer Value</mat-label>
                    <input type="number" step="1" matInput formControlName="number">
                    <mat-error *ngIf="editEntityFormGroup.get('attributes.number').hasError('pattern')">
                        Invalid integer value.
                    </mat-error>
                </mat-form-field>
                <div class="boolean-value-input" fxLayout="column" fxLayoutAlign="center start" fxFlex>
                    <label class="checkbox-label">Boolean Value</label>
                    <mat-checkbox formControlName="booleanValue" style="margin-bottom: 40px;">
                        
                    </mat-checkbox>
                </div>
            </div>
        </div>
        <div class="relations-list old-relations">
            <div class="mat-body-1" style="padding-bottom: 10px; color: rgba(0,0,0,0.57);">Relations</div>
            <div class="body" [fxShow]="oldRelations().length">
                <div class="row" fxLayout="row" fxLayoutAlign="start center" formArrayName="oldRelations" 
                     *ngFor="let relation of oldRelations().controls; let i = index;">
                    <div [formGroupName]="i" class="mat-elevation-z2" fxFlex fxLayout="row" style="padding: 5px 0 5px 5px;">
                        <div fxFlex fxLayout="column">
                            <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
                                <mat-form-field class="mat-block" style="min-width: 100px;">
                                    <mat-label>Direction</mat-label>
                                    <mat-select formControlName="direction" name="direction">
                                        <mat-option *ngFor="let direction of entitySearchDirection | keyvalue" [value]="direction.value">
                                            
                                        </mat-option>
                                    </mat-select>
                                    <mat-error *ngIf="relation.get('direction').hasError('required')">
                                        Relation direction is required.
                                    </mat-error>
                                </mat-form-field>
                                <tb-relation-type-autocomplete
                                        fxFlex class="mat-block"
                                        formControlName="relationType"
                                        required="true">
                                </tb-relation-type-autocomplete>
                            </div>
                            <div fxLayout="row" fxLayout.xs="column">
                                <tb-entity-select
                                        fxFlex class="mat-block"
                                        required="true"
                                        formControlName="relatedEntity">
                                </tb-entity-select>
                            </div>
                        </div>
                        <div fxLayout="column" fxLayoutAlign="center center">
                            <button mat-icon-button color="primary"
                                    aria-label="Remove"
                                    type="button"
                                    (click)="removeOldRelation(i)"
                                    matTooltip="Remove relation"
                                    matTooltipPosition="above">
                                <mat-icon>close</mat-icon>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="relations-list">
            <div class="mat-body-1" style="padding-bottom: 10px; color: rgba(0,0,0,0.57);">New Relations</div>
            <div class="body" [fxShow]="relations().length">
                <div class="row" fxLayout="row" fxLayoutAlign="start center" formArrayName="relations" *ngFor="let relation of relations().controls; let i = index;">
                    <div [formGroupName]="i" class="mat-elevation-z2" fxFlex fxLayout="row" style="padding: 5px 0 5px 5px;">
                        <div fxFlex fxLayout="column">
                            <div fxLayout="row" fxLayoutGap="8px" fxLayout.xs="column"  fxLayoutGap.xs="0">
                                <mat-form-field class="mat-block" style="min-width: 100px;">
                                    <mat-label>Direction</mat-label>
                                    <mat-select formControlName="direction" name="direction">
                                        <mat-option *ngFor="let direction of entitySearchDirection | keyvalue" [value]="direction.value">
                                            
                                        </mat-option>
                                    </mat-select>
                                    <mat-error *ngIf="relation.get('direction').hasError('required')">
                                        Relation direction is required.
                                    </mat-error>
                                </mat-form-field>
                                <tb-relation-type-autocomplete
                                        fxFlex class="mat-block"
                                        formControlName="relationType"
                                        [required]="true">
                                </tb-relation-type-autocomplete>
                            </div>
                            <div fxLayout="row" fxLayout.xs="column">
                                <tb-entity-select
                                        fxFlex class="mat-block"
                                        [required]="true"
                                        formControlName="relatedEntity">
                                </tb-entity-select>
                            </div>
                        </div>
                        <div fxLayout="column" fxLayoutAlign="center center">
                            <button mat-icon-button color="primary"
                                    aria-label="Remove"
                                    type="button"
                                    (click)="removeRelation(i)"
                                    matTooltip="Remove relation"
                                    matTooltipPosition="above">
                                <mat-icon>close</mat-icon>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
            <div>
                <button mat-raised-button color="primary"
                        type="button"
                        (click)="addRelation()"
                        matTooltip="Add Relation"
                        matTooltipPosition="above">
                    Add
                </button>
            </div>
        </div>
    </div>
    <div mat-dialog-actions fxLayout="row" fxLayoutAlign="end center">
        <button mat-button color="primary"
                type="button"
                [disabled]="(isLoading$ | async)"
                (click)="cancel()" cdkFocusInitial>
            Cancel
        </button>
        <button mat-button mat-raised-button color="primary"
                type="submit"
                [disabled]="(isLoading$ | async) || editEntityForm.invalid || !editEntityForm.dirty">
            Save
        </button>
    </div>
</form>
```

</details>











### 2.Rook

```shell
# 访问地址：
https://172.51.216.81:32700/
admin
Bl8c"Ab3+6-Q?N()~agu

# 修改密码： 1qaz2wsx
```

```shell
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



### 3.Harbor

```shell
# 外网地址：
http://172.51.216.88:8888
admin
admin
```



### 4.Spring Cloud

```shell
# 测试地址

# zipkin
http://zipkin.pro.com:31356/zipkin
#sentinel
http://sentinel.pro.com:31356/#/login
账户密码：sentinel/sentinel
# xxl-job
http://xxljob.pro.com:31356/xxl-job-admin
账户密码：admin/123456
# eureka
http://eureka.pro.com:31356/

# gateway
http://gateway.pro.com:31356/dconsumer/hello
http://gateway.pro.com:31356/dconsumer/say
http://gateway.pro.com:31356/dproducer/hello
http://gateway.pro.com:31356/dproducer/say
```

```shell
# 测试分布式任务调度平台（XXL-JOB）

# 配置服务端
# 参考文档：K8S-SpringCloud---外部服务.md    2.5.部署XXL-JOB执行器（msa-ext-job）


# 查看日志
[root@k8s-master1 pro]# kubectl logs -f --tail=10 msa-deploy-job-d5574c7db-hfh8q -n pro
----job one 1---Mon Dec 20 16:44:07 CST 2021
----job one 1---Mon Dec 20 16:44:08 CST 2021
----job one 1---Mon Dec 20 16:44:09 CST 2021
----job one 1---Mon Dec 20 16:44:10 CST 2021
----job one 1---Mon Dec 20 16:44:11 CST 2021
----job one 1---Mon Dec 20 16:44:12 CST 2021
----job one 1---Mon Dec 20 16:44:13 CST 2021
----job one 1---Mon Dec 20 16:44:14 CST 2021
----job one 1---Mon Dec 20 16:44:15 CST 2021
----job one 1---Mon Dec 20 16:44:16 CST 2021
----job one 1---Mon Dec 20 16:44:17 CST 2021
----job one 1---Mon Dec 20 16:44:18 CST 2021
----job one 1---Mon Dec 20 16:44:19 CST 2021
```



### 5.EFK

```shell
# ES地址：
http://172.51.216.88:9200


# Kibana
http://172.51.216.88:5601/
```



### 6.Prometheus

```shell
# ingress地址

http://prometheus.k8s.com:31356/
http://alertmanager.k8s.com:31356/
http://grafana.k8s.com:31356/
admin/admin
```





## 二、中间件（K8S）



### 1.PostgreSQL

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

```shell
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



### 3.Redis

```shell
# 内部访问

NAME                                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
service/example-distributedrediscluster     ClusterIP   10.1.123.77   <none>        6379/TCP,16379/TCP   3m54s
service/example-distributedrediscluster-0   ClusterIP   None          <none>        6379/TCP,16379/TCP   3m54s
service/example-distributedrediscluster-1   ClusterIP   None          <none>        6379/TCP,16379/TCP   3m54s
service/example-distributedrediscluster-2   ClusterIP   None          <none>        6379/TCP,16379/TCP   3m54s
```



### 4.RabbitMQ

```shell
# 地址

http://172.51.216.81:30672
username: default_user_skDybgNFObPZkgkKzJN
password: 1qaz2wsx

guest/guest



# k8s内部访问地址
# K8s 中的容器使用访问
svcname.namespace.svc.cluster.local:port
production-ready.default.svc.cluster.local:5672
```



### 5.MySQL

```shell
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





## 三、中间件（Docker）



### 1.PostgreSQL

```shell
# 访问地址

172.51.216.88
5432
postgres/postgres
```



### 2.RabbitMQ

```shell
# 访问地址


http://172.51.216.88:15672/ 
账户/密码：
guest/guest 
```



### 3.MySQL

```shell
# 访问地址

172.51.216.88
3306
root/root
```





## 四、折叠测试



#### 5.1.java

<br>

<details>
<summary>
Script should return the following structure:
</summary>
{% highlight java %}
{   
    msg: new payload,
    metadata: new metadata,
    msgType: new msgType 
}
{% endhighlight %}

</details>
<br>



#### 5.2.javascript

attribute from inbound Message payload into Alarm details.

{% highlight javascript %}
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}

return details;
{% endhighlight %}

{: .copy-code}





<br>
<details>
<summary>
attribute from inbound Message payload into Alarm details.
</summary>
{% highlight javascript %}
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}

return details;
{% endhighlight %}
</details>

{: .copy-code}

<br>



#### 5.3.json





<br>
<details>
<summary>
Here is an example of Outbound Message **payload**
</summary>
{% highlight json %}
{
  "tenantId": {
    "entityType": "TENANT",
    "id": "22cd8888-5dac-11e8-bbab-ad47060c9bbb"
  },
  "type": "High Temperature Alarm",
  "originator": {
    "entityType": "DEVICE",
    "id": "11cd8777-5dac-11e8-bbab-ad55560c9ccc"
  },
  "severity": "CRITICAL",
  "status": "ACTIVE_UNACK",
  "startTs": 1526985698000,
  "endTs": 1526985698000,
  "ackTs": 0,
  "clearTs": 0,
  "details": {
    "temperature": 70,
    "ts": 1526985696000
  },
  "propagate": true,
  "id": "33cd8999-5dac-11e8-bbab-ad47060c9431",
  "createdTime": 1526985698000,
  "name": "High Temperature Alarm"
}
{% endhighlight %}

</details>
<br>





--------------------------

<br>

<details>
<summary>
Example of custom connector configuration file. Press to expand.
</summary>
{% highlight json %}
{
  "name": "Custom serial connector",
  "devices": [
    {
      "name": "CustomSerialDevice1",
      "port": "/dev/ttyUSB0",
      "baudrate": 9600,
      "converter": "CustomSerialUplinkConverter",
      "telemetry": [
        {
          "type": "byte",
          "key": "humidity",
          "untilDelimiter": "\r"
        }
      ],
      "attributes":[
        {
          "key": "SerialNumber",
          "type": "string",
          "fromByte": 4,
          "toByte": -1
        }
      ],
      "attributeUpdates": [
        {
          "attributeOnThingsBoard": "attr1",
          "stringToDevice": "value = ${attr1}\n"
        }
      ]
    }
  ]
}
{% endhighlight %}

</details>
<br>



#### 5.4.yaml

<br>

<details>
<summary>
<b>Example of main configuration file. Press to show.</b>
</summary>

{% highlight yaml %}

thingsboard:
  host: thingsboard.cloud
  port: 1883
  remoteShell: false
  remoteConfiguration: false
  statistics:
    enable: true
    statsSendPeriodInSeconds: 3600
  minPackSendDelayMS: 0
  checkConnectorsConfigurationInSeconds: 60
  handleDeviceRenaming: true
  checkingDeviceActivity:
    checkDeviceInactivity: false
    inactivityTimeoutSeconds: 120
    inactivityCheckPeriodSeconds: 10
  security:
    accessToken: PUT_YOUR_ACCESS_TOKEN_HERE
  qos: 1
storage:
  type: memory
  read_records_count: 100
  max_records_count: 100000
grpc:
  enabled: false
  serverPort: 9595
  keepaliveTimeMs: 10000
  keepaliveTimeoutMs: 5000
  keepalivePermitWithoutCalls: true
  maxPingsWithoutData: 0
  minTimeBetweenPingsMs: 10000
  minPingIntervalWithoutDataMs: 5000
connectors:
  -
    name: MQTT Broker Connector
    type: mqtt
    configuration: mqtt.json

  -
    name: Modbus Connector
    type: modbus
    configuration: modbus.json

  -
    name: Modbus Connector
    type: modbus
    configuration: modbus_serial.json

  -
    name: OPC-UA Connector
    type: opcua
    configuration: opcua.json

  -
    name: BLE Connector
    type: ble
    configuration: ble.json

  -
    name: CAN Connector
    type: can
    configuration: can.json

  -
    name: Custom Serial Connector
    type: serial
    configuration: custom_serial.json
    class: CustomSerialConnector

{% endhighlight %}
<b><i>Spaces identity are important.</i></b>  
</details>





#### 5.5.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>

{% highlight python %}

"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger

    def __connect_to_devices(self):    # Function for opening connection and connecting to devices
        for device in self.devices:
            try:    # Start error handler
                connection_start = time.time()
                if self.devices[device].get("serial") is None \
                        or self.devices[device]["serial"] is None \
                        or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                    self.devices[device]["serial"] = None
                    while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                        '''connection to serial port with parameters from configuration file or default'''
                        self.devices[device]["serial"] = serial.Serial(
                                 port=self.__config.get('port', '/dev/ttyUSB0'),
                                 baudrate=self.__config.get('baudrate', 9600),
                                 bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                                 parity=self.__config.get('parity', serial.PARITY_NONE),
                                 stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                                 timeout=self.__config.get('timeout', 1),
                                 xonxoff=self.__config.get('xonxoff', False),
                                 rtscts=self.__config.get('rtscts', False),
                                 write_timeout=self.__config.get('write_timeout', None),
                                 dsrdtr=self.__config.get('dsrdtr', False),
                                 inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                                 exclusive=self.__config.get('exclusive', None)
                        )
                        time.sleep(.1)
                        if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                            log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                            break
            except serial.serialutil.SerialException:
                log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
                time.sleep(10)
            except Exception as e:
                log.exception(e)
                time.sleep(10)
            else:    # if no exception handled - add device and change connection state
                self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
                self.connected = True
    
    def open(self):    # Function called by gateway on start
        self.stopped = False
        self.start()
    
    def get_name(self):    # Function used for logging, sending data and statistic
        return self.name
    
    def is_connected(self):    # Function for checking connection state
        return self.connected
    
    def load_converters(self):    # Function for search a converter and save it.
        devices_config = self.__config.get('devices')
        try:
            if devices_config is not None:
                for device_config in devices_config:
                    if device_config.get('converter') is not None:
                        converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                        self.devices[device_config['name']] = {'converter': converter(device_config),
                                                               'device_config': device_config}
                    else:
                        log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
            else:
                log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
                self.close()
        except Exception as e:
            log.exception(e)
    
    def run(self):    # Main loop of thread
        try:
            while True:
                for device in self.devices:
                    serial = self.devices[device]["serial"]
                    ch = b''
                    data_from_device = b''
                    while ch != b'\n':
                        try:
                            try:
                                ch = serial.read(1)    # Reading data from serial
                            except AttributeError as e:
                                if serial is None:
                                    self.__connect_to_devices()    # if port not found - try to connect to it
                                    raise e
                            data_from_device = data_from_device + ch
                        except Exception as e:
                            log.exception(e)
                            break
                    try:
                        converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                        self.__gateway.send_to_storage(self.get_name(), converted_data)
                        time.sleep(.1)
                    except Exception as e:
                        log.exception(e)
                        self.close()
                        raise e
                if not self.connected:
                    break
        except Exception as e:
            log.exception(e)
    
    def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
        self.stopped = True
        for device in self.devices:
            self.__gateway.del_device(self.devices[device])
            if self.devices[device]['serial'].isOpen():
                self.devices[device]['serial'].close()
    
    def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
        log.debug(content)
        if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
            device_config = self.devices[content["device"]].get("device_config")
            if device_config is not None:
                log.debug(device_config)
                if device_config.get("attributeUpdates") is not None:
                    requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                    for request in requests:
                        attribute = request.get("attributeOnThingsBoard")
                        log.debug(attribute)
                        if attribute is not None and attribute in content["data"]:
                            try:
                                value = content["data"][attribute]    # get value from content
                                str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                                self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                                log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                                time.sleep(.01)
                            except Exception as e:
                                log.exception(e)
    
    def server_side_rpc_handler(self, content):
        pass

{% endhighlight %}
</details>
<br>



#### 5.6.text

<details>
<summary>
Sample output, referencing company.com as CN
</summary>
{% highlight text %}
Generating a RSA private key
writing new private key to 'rootKey.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:company.com
Email Address []:
{% endhighlight %}
</details>

<br/>





<details>
<summary>
Sample output, referencing *group.company.com* as CN
</summary>
{% highlight text %}
Generating a RSA private key
writing new private key to 'intermediateKey.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:group.company.com
Email Address []:
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
{% endhighlight %}
</details>
<br/>





#### 5.7.ruby

<details>

<summary>
<b>An example of the function for a device deletion.</b>
</summary>

{% highlight ruby %}
let $injector = widgetContext.$scope.$injector;
let dialogs = $injector.get(widgetContext.servicesMap.get('dialogs'));
let deviceService = $injector.get(widgetContext.servicesMap.get('deviceService'));

openDeleteDeviceDialog();

function openDeleteDeviceDialog() {
    let title = "Are you sure you want to delete the device " + entityName + "?";
    let content = "Be careful, after the confirmation, the device and all related data will become unrecoverable!";
    dialogs.confirm(title, content, 'Cancel', 'Delete').subscribe(
        function(result) {
            if (result) {
                deleteDevice();
            }
        }
    );
}

function deleteDevice() {
    deviceService.deleteDevice(entityId.id).subscribe(
        function() {
            widgetContext.updateAliases();
        }
    );
}
{% endhighlight %}
</details>





#### 5.8.bash

Lets post temperature = 99. Alarm should be created:

{% highlight bash %}
curl -v -X POST -d '{"temperature":99}' http://localhost:8080/api/v1/$ACCESS_TOKEN/telemetry --header "Content-Type:application/json"
{% endhighlight %}



<details>
<summary>
<b>Lets post temperature = 99. Alarm should be created:</b>
</summary>

{% highlight bash %}
curl -v -X POST -d '{"temperature":99}' http://localhost:8080/api/v1/$ACCESS_TOKEN/telemetry --header "Content-Type:application/json"
{% endhighlight %}
</details>





#### 5.9.改造

##### 5.9.1.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger

    def __connect_to_devices(self):    # Function for opening connection and connecting to devices
        for device in self.devices:
            try:    # Start error handler
                connection_start = time.time()
                if self.devices[device].get("serial") is None \
                        or self.devices[device]["serial"] is None \
                        or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                    self.devices[device]["serial"] = None
                    while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                        '''connection to serial port with parameters from configuration file or default'''
                        self.devices[device]["serial"] = serial.Serial(
                                 port=self.__config.get('port', '/dev/ttyUSB0'),
                                 baudrate=self.__config.get('baudrate', 9600),
                                 bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                                 parity=self.__config.get('parity', serial.PARITY_NONE),
                                 stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                                 timeout=self.__config.get('timeout', 1),
                                 xonxoff=self.__config.get('xonxoff', False),
                                 rtscts=self.__config.get('rtscts', False),
                                 write_timeout=self.__config.get('write_timeout', None),
                                 dsrdtr=self.__config.get('dsrdtr', False),
                                 inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                                 exclusive=self.__config.get('exclusive', None)
                        )
                        time.sleep(.1)
                        if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                            log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                            break
            except serial.serialutil.SerialException:
                log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
                time.sleep(10)
            except Exception as e:
                log.exception(e)
                time.sleep(10)
            else:    # if no exception handled - add device and change connection state
                self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
                self.connected = True
    
    def open(self):    # Function called by gateway on start
        self.stopped = False
        self.start()
    
    def get_name(self):    # Function used for logging, sending data and statistic
        return self.name
    
    def is_connected(self):    # Function for checking connection state
        return self.connected
    
    def load_converters(self):    # Function for search a converter and save it.
        devices_config = self.__config.get('devices')
        try:
            if devices_config is not None:
                for device_config in devices_config:
                    if device_config.get('converter') is not None:
                        converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                        self.devices[device_config['name']] = {'converter': converter(device_config),
                                                               'device_config': device_config}
                    else:
                        log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
            else:
                log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
                self.close()
        except Exception as e:
            log.exception(e)
    
    def run(self):    # Main loop of thread
        try:
            while True:
                for device in self.devices:
                    serial = self.devices[device]["serial"]
                    ch = b''
                    data_from_device = b''
                    while ch != b'\n':
                        try:
                            try:
                                ch = serial.read(1)    # Reading data from serial
                            except AttributeError as e:
                                if serial is None:
                                    self.__connect_to_devices()    # if port not found - try to connect to it
                                    raise e
                            data_from_device = data_from_device + ch
                        except Exception as e:
                            log.exception(e)
                            break
                    try:
                        converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                        self.__gateway.send_to_storage(self.get_name(), converted_data)
                        time.sleep(.1)
                    except Exception as e:
                        log.exception(e)
                        self.close()
                        raise e
                if not self.connected:
                    break
        except Exception as e:
            log.exception(e)
    
    def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
        self.stopped = True
        for device in self.devices:
            self.__gateway.del_device(self.devices[device])
            if self.devices[device]['serial'].isOpen():
                self.devices[device]['serial'].close()
    
    def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
        log.debug(content)
        if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
            device_config = self.devices[content["device"]].get("device_config")
            if device_config is not None:
                log.debug(device_config)
                if device_config.get("attributeUpdates") is not None:
                    requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                    for request in requests:
                        attribute = request.get("attributeOnThingsBoard")
                        log.debug(attribute)
                        if attribute is not None and attribute in content["data"]:
                            try:
                                value = content["data"][attribute]    # get value from content
                                str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                                self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                                log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                                time.sleep(.01)
                            except Exception as e:
                                log.exception(e)
    
    def server_side_rpc_handler(self, content):
        pass

{% endhighlight %}
</details>
<br>



##### 5.9.2.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

    """Import libraries"""
    
    import serial
    import time
    from threading import Thread
    from random import choice
    from string import ascii_lowercase
    from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
    from thingsboard_gateway.tb_utility.tb_utility import TBUtility
    
    class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
        def __init__(self, gateway,  config, connector_type):
            super().__init__()    # Initialize parents classes
            self.statistics = {'MessagesReceived': 0,
                               'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
            self.__config = config    # Save configuration from the configuration file.
            self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
            self.__connector_type = connector_type    # Saving type for connector, need for loading converter
            self.setName(self.__config.get("name",
                                           "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
            log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
            self.daemon = True    # Set self thread as daemon
            self.stopped = True    # Service variable for check state
            self.connected = False    # Service variable for check connection to device
            self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
            self.load_converters()    # Call function to load converters and save it into devices dictionary
            self.__connect_to_devices()    # Call function for connect to devices
            log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
            log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger


​    -------------        


​    def __connect_to_devices(self):    # Function for opening connection and connecting to devices
​        for device in self.devices:
​            try:    # Start error handler
​                connection_start = time.time()
​                if self.devices[device].get("serial") is None \
​                        or self.devices[device]["serial"] is None \
​                        or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
​                    self.devices[device]["serial"] = None
​                    while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
​                        '''connection to serial port with parameters from configuration file or default'''
​                        self.devices[device]["serial"] = serial.Serial(
​                                 port=self.__config.get('port', '/dev/ttyUSB0'),
​                                 baudrate=self.__config.get('baudrate', 9600),
​                                 bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
​                                 parity=self.__config.get('parity', serial.PARITY_NONE),
​                                 stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
​                                 timeout=self.__config.get('timeout', 1),
​                                 xonxoff=self.__config.get('xonxoff', False),
​                                 rtscts=self.__config.get('rtscts', False),
​                                 write_timeout=self.__config.get('write_timeout', None),
​                                 dsrdtr=self.__config.get('dsrdtr', False),
​                                 inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
​                                 exclusive=self.__config.get('exclusive', None)
​                        )
​                        time.sleep(.1)
​                        if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
​                            log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
​                            break
​            except serial.serialutil.SerialException:
​                log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
​                time.sleep(10)
​            except Exception as e:
​                log.exception(e)
​                time.sleep(10)
​            else:    # if no exception handled - add device and change connection state
​                self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
​                self.connected = True
​    
​    def open(self):    # Function called by gateway on start
​        self.stopped = False
​        self.start()
​    
​    def get_name(self):    # Function used for logging, sending data and statistic
​        return self.name
​    
​    def is_connected(self):    # Function for checking connection state
​        return self.connected
​    

    def load_converters(self):    # Function for search a converter and save it.
        devices_config = self.__config.get('devices')
        try:
            if devices_config is not None:
                for device_config in devices_config:
                    if device_config.get('converter') is not None:
                        converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                        self.devices[device_config['name']] = {'converter': converter(device_config),
                                                               'device_config': device_config}
                    else:
                        log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
            else:
                log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
                self.close()
        except Exception as e:
            log.exception(e)
    
    def run(self):    # Main loop of thread
        try:
            while True:
                for device in self.devices:
                    serial = self.devices[device]["serial"]
                    ch = b''
                    data_from_device = b''
                    while ch != b'\n':
                        try:
                            try:
                                ch = serial.read(1)    # Reading data from serial
                            except AttributeError as e:
                                if serial is None:
                                    self.__connect_to_devices()    # if port not found - try to connect to it
                                    raise e
                            data_from_device = data_from_device + ch
                        except Exception as e:
                            log.exception(e)
                            break
                    try:
                        converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                        self.__gateway.send_to_storage(self.get_name(), converted_data)
                        time.sleep(.1)
                    except Exception as e:
                        log.exception(e)
                        self.close()
                        raise e
                if not self.connected:
                    break
        except Exception as e:
            log.exception(e)
    
    def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
        self.stopped = True
        for device in self.devices:
            self.__gateway.del_device(self.devices[device])
            if self.devices[device]['serial'].isOpen():
                self.devices[device]['serial'].close()
    
    def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
        log.debug(content)
        if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
            device_config = self.devices[content["device"]].get("device_config")
            if device_config is not None:
                log.debug(device_config)
                if device_config.get("attributeUpdates") is not None:
                    requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                    for request in requests:
                        attribute = request.get("attributeOnThingsBoard")
                        log.debug(attribute)
                        if attribute is not None and attribute in content["data"]:
                            try:
                                value = content["data"][attribute]    # get value from content
                                str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                                self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                                log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                                time.sleep(.01)
                            except Exception as e:
                                log.exception(e)
    
    def server_side_rpc_handler(self, content):
        pass

{% endhighlight %}
</details>
<br>



##### 5.9.3.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

```python
"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
        
        
-------------        
        

def __connect_to_devices(self):    # Function for opening connection and connecting to devices
    for device in self.devices:
        try:    # Start error handler
            connection_start = time.time()
            if self.devices[device].get("serial") is None \
                    or self.devices[device]["serial"] is None \
                    or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                self.devices[device]["serial"] = None
                while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                    '''connection to serial port with parameters from configuration file or default'''
                    self.devices[device]["serial"] = serial.Serial(
                             port=self.__config.get('port', '/dev/ttyUSB0'),
                             baudrate=self.__config.get('baudrate', 9600),
                             bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                             parity=self.__config.get('parity', serial.PARITY_NONE),
                             stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                             timeout=self.__config.get('timeout', 1),
                             xonxoff=self.__config.get('xonxoff', False),
                             rtscts=self.__config.get('rtscts', False),
                             write_timeout=self.__config.get('write_timeout', None),
                             dsrdtr=self.__config.get('dsrdtr', False),
                             inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                             exclusive=self.__config.get('exclusive', None)
                    )
                    time.sleep(.1)
                    if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                        log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                        break
        except serial.serialutil.SerialException:
            log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
            time.sleep(10)
        except Exception as e:
            log.exception(e)
            time.sleep(10)
        else:    # if no exception handled - add device and change connection state
            self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
            self.connected = True

def open(self):    # Function called by gateway on start
    self.stopped = False
    self.start()

def get_name(self):    # Function used for logging, sending data and statistic
    return self.name

def is_connected(self):    # Function for checking connection state
    return self.connected

def load_converters(self):    # Function for search a converter and save it.
    devices_config = self.__config.get('devices')
    try:
        if devices_config is not None:
            for device_config in devices_config:
                if device_config.get('converter') is not None:
                    converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                    self.devices[device_config['name']] = {'converter': converter(device_config),
                                                           'device_config': device_config}
                else:
                    log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
        else:
            log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
            self.close()
    except Exception as e:
        log.exception(e)

def run(self):    # Main loop of thread
    try:
        while True:
            for device in self.devices:
                serial = self.devices[device]["serial"]
                ch = b''
                data_from_device = b''
                while ch != b'\n':
                    try:
                        try:
                            ch = serial.read(1)    # Reading data from serial
                        except AttributeError as e:
                            if serial is None:
                                self.__connect_to_devices()    # if port not found - try to connect to it
                                raise e
                        data_from_device = data_from_device + ch
                    except Exception as e:
                        log.exception(e)
                        break
                try:
                    converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                    self.__gateway.send_to_storage(self.get_name(), converted_data)
                    time.sleep(.1)
                except Exception as e:
                    log.exception(e)
                    self.close()
                    raise e
            if not self.connected:
                break
    except Exception as e:
        log.exception(e)

def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass
```

{% endhighlight %}
</details>
<br>



##### 5.9.4.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

```
"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
        
        
-------------        
        

def __connect_to_devices(self):    # Function for opening connection and connecting to devices
    for device in self.devices:
        try:    # Start error handler
            connection_start = time.time()
            if self.devices[device].get("serial") is None \
                    or self.devices[device]["serial"] is None \
                    or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                self.devices[device]["serial"] = None
                while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                    '''connection to serial port with parameters from configuration file or default'''
                    self.devices[device]["serial"] = serial.Serial(
                             port=self.__config.get('port', '/dev/ttyUSB0'),
                             baudrate=self.__config.get('baudrate', 9600),
                             bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                             parity=self.__config.get('parity', serial.PARITY_NONE),
                             stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                             timeout=self.__config.get('timeout', 1),
                             xonxoff=self.__config.get('xonxoff', False),
                             rtscts=self.__config.get('rtscts', False),
                             write_timeout=self.__config.get('write_timeout', None),
                             dsrdtr=self.__config.get('dsrdtr', False),
                             inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                             exclusive=self.__config.get('exclusive', None)
                    )
                    time.sleep(.1)
                    if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                        log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                        break
        except serial.serialutil.SerialException:
            log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
            time.sleep(10)
        except Exception as e:
            log.exception(e)
            time.sleep(10)
        else:    # if no exception handled - add device and change connection state
            self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
            self.connected = True

def open(self):    # Function called by gateway on start
    self.stopped = False
    self.start()

def get_name(self):    # Function used for logging, sending data and statistic
    return self.name

def is_connected(self):    # Function for checking connection state
    return self.connected

def load_converters(self):    # Function for search a converter and save it.
    devices_config = self.__config.get('devices')
    try:
        if devices_config is not None:
            for device_config in devices_config:
                if device_config.get('converter') is not None:
                    converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                    self.devices[device_config['name']] = {'converter': converter(device_config),
                                                           'device_config': device_config}
                else:
                    log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
        else:
            log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
            self.close()
    except Exception as e:
        log.exception(e)

def run(self):    # Main loop of thread
    try:
        while True:
            for device in self.devices:
                serial = self.devices[device]["serial"]
                ch = b''
                data_from_device = b''
                while ch != b'\n':
                    try:
                        try:
                            ch = serial.read(1)    # Reading data from serial
                        except AttributeError as e:
                            if serial is None:
                                self.__connect_to_devices()    # if port not found - try to connect to it
                                raise e
                        data_from_device = data_from_device + ch
                    except Exception as e:
                        log.exception(e)
                        break
                try:
                    converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                    self.__gateway.send_to_storage(self.get_name(), converted_data)
                    time.sleep(.1)
                except Exception as e:
                    log.exception(e)
                    self.close()
                    raise e
            if not self.connected:
                break
    except Exception as e:
        log.exception(e)

def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass
```
{: .copy-code}
{% endhighlight %}
</details>
<br>



##### 5.9.5.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

```
"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
        
        
-------------        
        

def __connect_to_devices(self):    # Function for opening connection and connecting to devices
    for device in self.devices:
        try:    # Start error handler
            connection_start = time.time()
            if self.devices[device].get("serial") is None \
                    or self.devices[device]["serial"] is None \
                    or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                self.devices[device]["serial"] = None
                while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                    '''connection to serial port with parameters from configuration file or default'''
                    self.devices[device]["serial"] = serial.Serial(
                             port=self.__config.get('port', '/dev/ttyUSB0'),
                             baudrate=self.__config.get('baudrate', 9600),
                             bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                             parity=self.__config.get('parity', serial.PARITY_NONE),
                             stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                             timeout=self.__config.get('timeout', 1),
                             xonxoff=self.__config.get('xonxoff', False),
                             rtscts=self.__config.get('rtscts', False),
                             write_timeout=self.__config.get('write_timeout', None),
                             dsrdtr=self.__config.get('dsrdtr', False),
                             inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                             exclusive=self.__config.get('exclusive', None)
                    )
                    time.sleep(.1)
                    if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                        log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                        break
        except serial.serialutil.SerialException:
            log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
            time.sleep(10)
        except Exception as e:
            log.exception(e)
            time.sleep(10)
        else:    # if no exception handled - add device and change connection state
            self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
            self.connected = True

def open(self):    # Function called by gateway on start
    self.stopped = False
    self.start()

def get_name(self):    # Function used for logging, sending data and statistic
    return self.name

def is_connected(self):    # Function for checking connection state
    return self.connected

def load_converters(self):    # Function for search a converter and save it.
    devices_config = self.__config.get('devices')
    try:
        if devices_config is not None:
            for device_config in devices_config:
                if device_config.get('converter') is not None:
                    converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                    self.devices[device_config['name']] = {'converter': converter(device_config),
                                                           'device_config': device_config}
                else:
                    log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
        else:
            log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
            self.close()
    except Exception as e:
        log.exception(e)

def run(self):    # Main loop of thread
    try:
        while True:
            for device in self.devices:
                serial = self.devices[device]["serial"]
                ch = b''
                data_from_device = b''
                while ch != b'\n':
                    try:
                        try:
                            ch = serial.read(1)    # Reading data from serial
                        except AttributeError as e:
                            if serial is None:
                                self.__connect_to_devices()    # if port not found - try to connect to it
                                raise e
                        data_from_device = data_from_device + ch
                    except Exception as e:
                        log.exception(e)
                        break
                try:
                    converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                    self.__gateway.send_to_storage(self.get_name(), converted_data)
                    time.sleep(.1)
                except Exception as e:
                    log.exception(e)
                    self.close()
                    raise e
            if not self.connected:
                break
    except Exception as e:
        log.exception(e)

def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass
```
{% endhighlight %}
{: .copy-code}

</details>
<br>



##### 5.9.6.python

<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

```
"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
        
        
-------------        
        

def __connect_to_devices(self):    # Function for opening connection and connecting to devices
    for device in self.devices:
        try:    # Start error handler
            connection_start = time.time()
            if self.devices[device].get("serial") is None \
                    or self.devices[device]["serial"] is None \
                    or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                self.devices[device]["serial"] = None
                while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                    '''connection to serial port with parameters from configuration file or default'''
                    self.devices[device]["serial"] = serial.Serial(
                             port=self.__config.get('port', '/dev/ttyUSB0'),
                             baudrate=self.__config.get('baudrate', 9600),
                             bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                             parity=self.__config.get('parity', serial.PARITY_NONE),
                             stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                             timeout=self.__config.get('timeout', 1),
                             xonxoff=self.__config.get('xonxoff', False),
                             rtscts=self.__config.get('rtscts', False),
                             write_timeout=self.__config.get('write_timeout', None),
                             dsrdtr=self.__config.get('dsrdtr', False),
                             inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                             exclusive=self.__config.get('exclusive', None)
                    )
                    time.sleep(.1)
                    if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                        log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                        break
        except serial.serialutil.SerialException:
            log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
            time.sleep(10)
        except Exception as e:
            log.exception(e)
            time.sleep(10)
        else:    # if no exception handled - add device and change connection state
            self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
            self.connected = True

def open(self):    # Function called by gateway on start
    self.stopped = False
    self.start()

def get_name(self):    # Function used for logging, sending data and statistic
    return self.name

def is_connected(self):    # Function for checking connection state
    return self.connected

def load_converters(self):    # Function for search a converter and save it.
    devices_config = self.__config.get('devices')
    try:
        if devices_config is not None:
            for device_config in devices_config:
                if device_config.get('converter') is not None:
                    converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                    self.devices[device_config['name']] = {'converter': converter(device_config),
                                                           'device_config': device_config}
                else:
                    log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
        else:
            log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
            self.close()
    except Exception as e:
        log.exception(e)

def run(self):    # Main loop of thread
    try:
        while True:
            for device in self.devices:
                serial = self.devices[device]["serial"]
                ch = b''
                data_from_device = b''
                while ch != b'\n':
                    try:
                        try:
                            ch = serial.read(1)    # Reading data from serial
                        except AttributeError as e:
                            if serial is None:
                                self.__connect_to_devices()    # if port not found - try to connect to it
                                raise e
                        data_from_device = data_from_device + ch
                    except Exception as e:
                        log.exception(e)
                        break
                try:
                    converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                    self.__gateway.send_to_storage(self.get_name(), converted_data)
                    time.sleep(.1)
                except Exception as e:
                    log.exception(e)
                    self.close()
                    raise e
            if not self.connected:
                break
    except Exception as e:
        log.exception(e)

def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass
```
{% endhighlight %}

</details>
{: .copy-code}

<br>





<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

```
"""Import libraries"""

import serial
import time
from threading import Thread
from random import choice
from string import ascii_lowercase
from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
from thingsboard_gateway.tb_utility.tb_utility import TBUtility

class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
    def __init__(self, gateway,  config, connector_type):
        super().__init__()    # Initialize parents classes
        self.statistics = {'MessagesReceived': 0,
                           'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
        self.__config = config    # Save configuration from the configuration file.
        self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
        self.__connector_type = connector_type    # Saving type for connector, need for loading converter
        self.setName(self.__config.get("name",
                                       "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
        log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
        self.daemon = True    # Set self thread as daemon
        self.stopped = True    # Service variable for check state
        self.connected = False    # Service variable for check connection to device
        self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
        self.load_converters()    # Call function to load converters and save it into devices dictionary
        self.__connect_to_devices()    # Call function for connect to devices
        log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
        log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
        
        
-------------        
        

def __connect_to_devices(self):    # Function for opening connection and connecting to devices
    for device in self.devices:
        try:    # Start error handler
            connection_start = time.time()
            if self.devices[device].get("serial") is None \
                    or self.devices[device]["serial"] is None \
                    or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                self.devices[device]["serial"] = None
                while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                    '''connection to serial port with parameters from configuration file or default'''
                    self.devices[device]["serial"] = serial.Serial(
                             port=self.__config.get('port', '/dev/ttyUSB0'),
                             baudrate=self.__config.get('baudrate', 9600),
                             bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                             parity=self.__config.get('parity', serial.PARITY_NONE),
                             stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                             timeout=self.__config.get('timeout', 1),
                             xonxoff=self.__config.get('xonxoff', False),
                             rtscts=self.__config.get('rtscts', False),
                             write_timeout=self.__config.get('write_timeout', None),
                             dsrdtr=self.__config.get('dsrdtr', False),
                             inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                             exclusive=self.__config.get('exclusive', None)
                    )
                    time.sleep(.1)
                    if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                        log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                        break
        except serial.serialutil.SerialException:
            log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
            time.sleep(10)
        except Exception as e:
            log.exception(e)
            time.sleep(10)
        else:    # if no exception handled - add device and change connection state
            self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
            self.connected = True

def open(self):    # Function called by gateway on start
    self.stopped = False
    self.start()

def get_name(self):    # Function used for logging, sending data and statistic
    return self.name

def is_connected(self):    # Function for checking connection state
    return self.connected

def load_converters(self):    # Function for search a converter and save it.
    devices_config = self.__config.get('devices')
    try:
        if devices_config is not None:
            for device_config in devices_config:
                if device_config.get('converter') is not None:
                    converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                    self.devices[device_config['name']] = {'converter': converter(device_config),
                                                           'device_config': device_config}
                else:
                    log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
        else:
            log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
            self.close()
    except Exception as e:
        log.exception(e)

def run(self):    # Main loop of thread
    try:
        while True:
            for device in self.devices:
                serial = self.devices[device]["serial"]
                ch = b''
                data_from_device = b''
                while ch != b'\n':
                    try:
                        try:
                            ch = serial.read(1)    # Reading data from serial
                        except AttributeError as e:
                            if serial is None:
                                self.__connect_to_devices()    # if port not found - try to connect to it
                                raise e
                        data_from_device = data_from_device + ch
                    except Exception as e:
                        log.exception(e)
                        break
                try:
                    converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                    self.__gateway.send_to_storage(self.get_name(), converted_data)
                    time.sleep(.1)
                except Exception as e:
                    log.exception(e)
                    self.close()
                    raise e
            if not self.connected:
                break
    except Exception as e:
        log.exception(e)

def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass
```
{% endhighlight %}
{: .copy-code}
</details>
<br>



#### 5.10.显示文本

1.

{% highlight javascript %}
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}

{% endhighlight %}



2.

{% highlight javascript %}

```
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}
```

{% endhighlight %}



3.

{% highlight javascript %}

```java
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}
```

{% endhighlight %}



4.

{% highlight javascript %}

```java
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}
```
{: .copy-code}

{% endhighlight %}



5.

{% highlight javascript %}

```java
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}
```
{% endhighlight %}
{: .copy-code}







#### 5.11.完整测试

1.

<br>
<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>


{% highlight python %}

    """Import libraries"""
    
    import serial
    import time
    from threading import Thread
    from random import choice
    from string import ascii_lowercase
    from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
    from thingsboard_gateway.tb_utility.tb_utility import TBUtility
    
    class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
        def __init__(self, gateway,  config, connector_type):
            super().__init__()    # Initialize parents classes
            self.statistics = {'MessagesReceived': 0,
                               'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
            self.__config = config    # Save configuration from the configuration file.
            self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
            self.__connector_type = connector_type    # Saving type for connector, need for loading converter
            self.setName(self.__config.get("name",
                                           "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
            log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
            self.daemon = True    # Set self thread as daemon
            self.stopped = True    # Service variable for check state
            self.connected = False    # Service variable for check connection to device
            self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
            self.load_converters()    # Call function to load converters and save it into devices dictionary
            self.__connect_to_devices()    # Call function for connect to devices
            log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
            log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
    
    def __connect_to_devices(self):    # Function for opening connection and connecting to devices
        for device in self.devices:
            try:    # Start error handler
                connection_start = time.time()
                if self.devices[device].get("serial") is None \
                        or self.devices[device]["serial"] is None \
                        or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                    self.devices[device]["serial"] = None
                    while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                        '''connection to serial port with parameters from configuration file or default'''
                        self.devices[device]["serial"] = serial.Serial(
                                 port=self.__config.get('port', '/dev/ttyUSB0'),
                                 baudrate=self.__config.get('baudrate', 9600),
                                 bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                                 parity=self.__config.get('parity', serial.PARITY_NONE),
                                 stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                                 timeout=self.__config.get('timeout', 1),
                                 xonxoff=self.__config.get('xonxoff', False),
                                 rtscts=self.__config.get('rtscts', False),
                                 write_timeout=self.__config.get('write_timeout', None),
                                 dsrdtr=self.__config.get('dsrdtr', False),
                                 inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                                 exclusive=self.__config.get('exclusive', None)
                        )
                        time.sleep(.1)
                        if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                            log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                            break
            except serial.serialutil.SerialException:
                log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
                time.sleep(10)
            except Exception as e:
                log.exception(e)
                time.sleep(10)
            else:    # if no exception handled - add device and change connection state
                self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
                self.connected = True
    
    def open(self):    # Function called by gateway on start
        self.stopped = False
        self.start()
    
    def get_name(self):    # Function used for logging, sending data and statistic
        return self.name
    
    def is_connected(self):    # Function for checking connection state
        return self.connected
    
    def load_converters(self):    # Function for search a converter and save it.
        devices_config = self.__config.get('devices')
        try:
            if devices_config is not None:
                for device_config in devices_config:
                    if device_config.get('converter') is not None:
                        converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                        self.devices[device_config['name']] = {'converter': converter(device_config),
                                                               'device_config': device_config}
                    else:
                        log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
            else:
                log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
                self.close()
        except Exception as e:
            log.exception(e)
    
    def run(self):    # Main loop of thread
        try:
            while True:
                for device in self.devices:
                    serial = self.devices[device]["serial"]
                    ch = b''
                    data_from_device = b''
                    while ch != b'\n':
                        try:
                            try:
                                ch = serial.read(1)    # Reading data from serial
                            except AttributeError as e:
                                if serial is None:
                                    self.__connect_to_devices()    # if port not found - try to connect to it
                                    raise e
                            data_from_device = data_from_device + ch
                        except Exception as e:
                            log.exception(e)
                            break
                    try:
                        converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                        self.__gateway.send_to_storage(self.get_name(), converted_data)
                        time.sleep(.1)
                    except Exception as e:
                        log.exception(e)
                        self.close()
                        raise e
                if not self.connected:
                    break
        except Exception as e:
            log.exception(e)
    
    def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
        self.stopped = True
        for device in self.devices:
            self.__gateway.del_device(self.devices[device])
            if self.devices[device]['serial'].isOpen():
                self.devices[device]['serial'].close()
    
    def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
        log.debug(content)
        if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
            device_config = self.devices[content["device"]].get("device_config")
            if device_config is not None:
                log.debug(device_config)
                if device_config.get("attributeUpdates") is not None:
                    requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                    for request in requests:
                        attribute = request.get("attributeOnThingsBoard")
                        log.debug(attribute)
                        if attribute is not None and attribute in content["data"]:
                            try:
                                value = content["data"][attribute]    # get value from content
                                str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                                self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                                log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                                time.sleep(.01)
                            except Exception as e:
                                log.exception(e)
    
    def server_side_rpc_handler(self, content):
        pass

{% endhighlight %}
</details>
{: .copy-code}

<br>



2.

{% highlight javascript %}

```
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}
```

{% endhighlight %}
{: .copy-code}





#### 5.12.py

```

```






```

```


<br>

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>
{% highlight python %}

    """Import libraries"""
    
    import serial
    import time
    from threading import Thread
    from random import choice
    from string import ascii_lowercase
    from thingsboard_gateway.connectors.connector import Connector, log    # Import base class for connector and logger
    from thingsboard_gateway.tb_utility.tb_utility import TBUtility
    
    class CustomSerialConnector(Thread, Connector):    # Define a connector class, it should inherit from "Connector" class.
        def __init__(self, gateway,  config, connector_type):
            super().__init__()    # Initialize parents classes
            self.statistics = {'MessagesReceived': 0,
                               'MessagesSent': 0}    # Dictionary, will save information about count received and sent messages.
            self.__config = config    # Save configuration from the configuration file.
            self.__gateway = gateway    # Save gateway object, we will use some gateway methods for adding devices and saving data from them.
            self.__connector_type = connector_type    # Saving type for connector, need for loading converter
            self.setName(self.__config.get("name",
                                           "Custom %s connector " % self.get_name() + ''.join(choice(ascii_lowercase) for _ in range(5))))    # get from the configuration or create name for logs.
            log.info("Starting Custom %s connector", self.get_name())    # Send message to logger
            self.daemon = True    # Set self thread as daemon
            self.stopped = True    # Service variable for check state
            self.connected = False    # Service variable for check connection to device
            self.devices = {}    # Dictionary with devices, will contain devices configurations, converters for devices and serial port objects
            self.load_converters()    # Call function to load converters and save it into devices dictionary
            self.__connect_to_devices()    # Call function for connect to devices
            log.info('Custom connector %s initialization success.', self.get_name())    # Message to logger
            log.info("Devices in configuration file found: %s ", '\n'.join(device for device in self.devices))    # Message to logger
    
    def __connect_to_devices(self):    # Function for opening connection and connecting to devices
        for device in self.devices:
            try:    # Start error handler
                connection_start = time.time()
                if self.devices[device].get("serial") is None \
                        or self.devices[device]["serial"] is None \
                        or not self.devices[device]["serial"].isOpen():    # Connect only if serial not available earlier or it is closed.
                    self.devices[device]["serial"] = None
                    while self.devices[device]["serial"] is None or not self.devices[device]["serial"].isOpen():    # Try connect
                        '''connection to serial port with parameters from configuration file or default'''
                        self.devices[device]["serial"] = serial.Serial(
                                 port=self.__config.get('port', '/dev/ttyUSB0'),
                                 baudrate=self.__config.get('baudrate', 9600),
                                 bytesize=self.__config.get('bytesize', serial.EIGHTBITS),
                                 parity=self.__config.get('parity', serial.PARITY_NONE),
                                 stopbits=self.__config.get('stopbits', serial.STOPBITS_ONE),
                                 timeout=self.__config.get('timeout', 1),
                                 xonxoff=self.__config.get('xonxoff', False),
                                 rtscts=self.__config.get('rtscts', False),
                                 write_timeout=self.__config.get('write_timeout', None),
                                 dsrdtr=self.__config.get('dsrdtr', False),
                                 inter_byte_timeout=self.__config.get('inter_byte_timeout', None),
                                 exclusive=self.__config.get('exclusive', None)
                        )
                        time.sleep(.1)
                        if time.time() - connection_start > 10:    # Break connection try if it setting up for 10 seconds
                            log.error("Connection refused per timeout for device %s", self.devices[device]["device_config"].get("name"))
                            break
            except serial.serialutil.SerialException:
                log.error("Port %s for device %s - not found", self.__config.get('port', '/dev/ttyUSB0'), device)
                time.sleep(10)
            except Exception as e:
                log.exception(e)
                time.sleep(10)
            else:    # if no exception handled - add device and change connection state
                self.__gateway.add_device(self.devices[device]["device_config"]["name"], {"connector": self})
                self.connected = True
    
    def open(self):    # Function called by gateway on start
        self.stopped = False
        self.start()
    
    def get_name(self):    # Function used for logging, sending data and statistic
        return self.name
    
    def is_connected(self):    # Function for checking connection state
        return self.connected
    
    def load_converters(self):    # Function for search a converter and save it.
        devices_config = self.__config.get('devices')
        try:
            if devices_config is not None:
                for device_config in devices_config:
                    if device_config.get('converter') is not None:
                        converter = TBUtility.check_and_import(self.__connector_type, device_config['converter'])
                        self.devices[device_config['name']] = {'converter': converter(device_config),
                                                               'device_config': device_config}
                    else:
                        log.error('Converter configuration for the custom connector %s -- not found, please check your configuration file.', self.get_name())
            else:
                log.error('Section "devices" in the configuration not found. A custom connector %s has being stopped.', self.get_name())
                self.close()
        except Exception as e:
            log.exception(e)
    
    def run(self):    # Main loop of thread
        try:
            while True:
                for device in self.devices:
                    serial = self.devices[device]["serial"]
                    ch = b''
                    data_from_device = b''
                    while ch != b'\n':
                        try:
                            try:
                                ch = serial.read(1)    # Reading data from serial
                            except AttributeError as e:
                                if serial is None:
                                    self.__connect_to_devices()    # if port not found - try to connect to it
                                    raise e
                            data_from_device = data_from_device + ch
                        except Exception as e:
                            log.exception(e)
                            break
                    try:
                        converted_data = self.devices[device]['converter'].convert(self.devices[device]['device_config'], data_from_device)
                        self.__gateway.send_to_storage(self.get_name(), converted_data)
                        time.sleep(.1)
                    except Exception as e:
                        log.exception(e)
                        self.close()
                        raise e
                if not self.connected:
                    break
        except Exception as e:
            log.exception(e)
    
    def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
        self.stopped = True
        for device in self.devices:
            self.__gateway.del_device(self.devices[device])
            if self.devices[device]['serial'].isOpen():
                self.devices[device]['serial'].close()
    
    def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
        log.debug(content)
        if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
            device_config = self.devices[content["device"]].get("device_config")
            if device_config is not None:
                log.debug(device_config)
                if device_config.get("attributeUpdates") is not None:
                    requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                    for request in requests:
                        attribute = request.get("attributeOnThingsBoard")
                        log.debug(attribute)
                        if attribute is not None and attribute in content["data"]:
                            try:
                                value = content["data"][attribute]    # get value from content
                                str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                                self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                                log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                                time.sleep(.01)
                            except Exception as e:
                                log.exception(e)
    
    def server_side_rpc_handler(self, content):
        pass

{% endhighlight %}
</details>
{: .copy-code}

<br>



8.

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>
{% highlight python %}

def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass

{% endhighlight %}
</details>
{: .copy-code}

<br>





9.

<details>
<summary>
<b>Example of custom connector file. Press to expand.</b>
</summary>
{% highlight python %}
```
def close(self):    # Close connect function, usually used if exception handled in gateway main loop or in connector main loop
    self.stopped = True
    for device in self.devices:
        self.__gateway.del_device(self.devices[device])
        if self.devices[device]['serial'].isOpen():
            self.devices[device]['serial'].close()

def on_attributes_update(self, content):    # Function used for processing attribute update requests from ThingsBoard
    log.debug(content)
    if self.devices.get(content["device"]) is not None:    # checking - is device in configuration?
        device_config = self.devices[content["device"]].get("device_config")
        if device_config is not None:
            log.debug(device_config)
            if device_config.get("attributeUpdates") is not None:
                requests = device_config["attributeUpdates"]    # getting configuration for attribute requests
                for request in requests:
                    attribute = request.get("attributeOnThingsBoard")
                    log.debug(attribute)
                    if attribute is not None and attribute in content["data"]:
                        try:
                            value = content["data"][attribute]    # get value from content
                            str_to_send = str(request["stringToDevice"].replace("${" + attribute + "}", str(value))).encode("UTF-8")    # form a string to send to device
                            self.devices[content["device"]]["serial"].write(str_to_send)    # send string to device
                            log.debug("Attribute update request to device %s : %s", content["device"], str_to_send)
                            time.sleep(.01)
                        except Exception as e:
                            log.exception(e)

def server_side_rpc_handler(self, content):
    pass
```

{% endhighlight %}
</details>
{: .copy-code}

<br>



#### 5.13.复制



attribute from inbound Message payload into Alarm details.

{% highlight javascript %}
var details = {temperature: msg.temperature, count: 1};

if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}

return details;
{% endhighlight %}
{: .copy-code}





<br>

<details>
<summary>
attribute from inbound Message payload into Alarm details.
</summary>
{% highlight javascript %}
var details = {temperature: msg.temperature, count: 1};


if (metadata.prevAlarmDetails) {
    var prevDetails = JSON.parse(metadata.prevAlarmDetails);
    if(prevDetails.count) {
        details.count = prevDetails.count + 1;
    }
}

return details;
{% endhighlight %}
</details>
{: .copy-code}

<br>


