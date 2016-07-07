# heapster
Compute Resource Usage Analysis and Monitoring of Container Clusters

## Run Heapster in a Kubernetes cluster with an InfluxDB backend and a Grafana UI

1. 确保 cluster 和 kube-dns 工作正常

    ```
    core@zodiac-01 ~/liuqs $ kubectl cluster-info
    Kubernetes master is running at http://localhost:8080
    KubeDNS is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/kube-dns
    ```

1. git clone or download source code from https://github.com/k8sp/heapster.git 并切换目录到 heapster/deploy

   ```
   core@zodiac-01 ~/liuqs $ git clone https://github.com/k8sp/heapster.git && cd heapster/deploy
   Cloning into 'heapster'...
   remote: Counting objects: 59, done.
   remote: Compressing objects: 100% (39/39), done.
   remote: Total 59 (delta 19), reused 54 (delta 19), pack-reused 0
   Unpacking objects: 100% (59/59), done.
   Checking connectivity... done.
   core@zodiac-01 ~/liuqs/heapster/deploy $ ls
   kube-config  kube.sh
   ```

1. Start all of the pods and services

    ```
    core@zodiac-01 ~/liuqs/heapster/deploy $ ./kube.sh start
    heapster pods have been setup
    ```

1. 创建后，执行会看 cluster-info 多了 Heapster 和 monitoring-grafana

    ```
    core@zodiac-01 ~/liuqs/heapster/deploy $ kubectl cluster-info
    Kubernetes master is running at http://localhost:8080
    Heapster is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/heapster
    KubeDNS is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/kube-dns
    monitoring-grafana is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/monitoring-grafana
    ```

1. 查询 pods 在运行

    ```
    core@zodiac-01 ~/liuqs/heapster/deploy $ kubectl get pods --namespace=kube-system
    NAME                                    READY     STATUS        RESTARTS   AGE
    heapster-it1l0                          1/1       Running       0          4m
    influxdb-grafana-zz3of                  2/2       Running       0          4m
    kube-apiserver-10.10.10.201             1/1       Running       0          41m
    kube-controller-manager-10.10.10.201    1/1       Running       0          41m
    kube-dns-v11-9t185                      4/4       Running       0          31m
    kube-proxy-10.10.10.201                 1/1       Running       0          41m
    kube-proxy-10.10.10.202                 1/1       Running       0          41m
    kube-proxy-10.10.10.203                 1/1       Running       0          41m
    kube-proxy-10.10.10.204                 1/1       Running       0          41m
    kube-proxy-10.10.10.205                 1/1       Running       0          41m
    kube-proxy-10.10.10.206                 1/1       Running       0          41m
    kube-proxy-10.10.10.207                 1/1       Running       0          41m
    kube-proxy-10.10.10.208                 1/1       Running       0          41m
    kube-proxy-10.10.10.209                 1/1       Running       0          42m
    kube-proxy-10.10.10.210                 1/1       Running       0          41m
    kube-proxy-10.10.10.211                 1/1       Running       0          41m
    kube-scheduler-10.10.10.201             1/1       Running       0          41m
    kubernetes-dashboard-1775839595-2us7o   1/1       Running       0          44s
    ```

1. 查询 services 在运行

    ```
    core@zodiac-01 ~/liuqs/heapster/deploy $ kubectl get services --namespace=kube-system
    NAME                   CLUSTER-IP   EXTERNAL-IP   PORT(S)             AGE
    heapster               10.3.0.115   <none>        80/TCP              4m
    kube-dns               10.3.0.10    <none>        53/UDP,53/TCP       32m
    kubernetes-dashboard   10.3.0.12    nodes         80/TCP              1m
    monitoring-grafana     10.3.0.224   nodes         80/TCP              4m
    monitoring-influxdb    10.3.0.214   <none>        8083/TCP,8086/TCP   4m
    ```

1. 可以查询通过 describe services 看到 Grafana 的 NodePort 端口号，这个是在配置文件中指定，设置为32017（如果不指定 NodePort 端口，系统会随机出一个30000以后的端口）
1. 通过 http://10.10.10.201:32017/ 可以访问 Grafana UI 界面， 查看和配置监控数据



## Delete Heapster in a Kubernetes cluster with an InfluxDB backend and a Grafana UI

1. 删除 Heapster, InfluxDB, Grafana

    ```
    core@zodiac-01 ~/liuqs $ ./kube.sh stop
    heapster pods have been removed.
    ```


## 注意
1. https://github.com/kubernetes/heapster/issues/657
