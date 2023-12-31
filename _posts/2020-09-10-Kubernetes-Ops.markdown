# Kubernetes 集群常用操作

## Deployment Scale 

有时需要完成对 pod 增加一个或多个副本数量，又或是减少pod 的副本数量
可以使用下面的 scale 命令完成

```bash
kubectl scale deployment -n nsname deploymentName --replicas=number
```

其中 number 代表增加/减少 的副本数量


## ConfigMap 数据解析

通常情况下 ConfigMap 包含了大量的内容数据，格式可能为 JSON, Yaml 

使用 -o 参数 （yaml ,  json ) 完成数据的解析


```bash
kubectl get configMap -n nsname configMapName -o yaml

kubectl get configMap -n nsname configMapName -o json
```


当解析的数据是JSON 数据时，还可以借助 jq 等工具完成层级解析

```bash
kubectl get cm -n tce ocloud-ckafka-oss-gateway -o jsonpath='{.data.local\.json}' | jq -r  .service.db_hcmq.host
```

## Switch Cluster

https://stackoverflow.com/questions/43643463/how-to-switch-kubectl-clusters-between-gcloud-and-minikube


You can switch from local (minikube) to gcloud and back with:

```bash
kubectl config use-context CONTEXT_NAME`
```


to list all contexts:
```bash
kubectl config get-contexts
```

You can create different enviroments for local and gcloud and put it in separate yaml files.