# 附录 G：ResourceQuota 示例

通过将 YAML 文件应用于命名空间或在 Pod 的配置文件中指定要求来创建 `ResourceQuota` 对象，以限制命名空间内的总体资源使用。下面的例子是基于 [Kubernetes 官方文档](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)的一个命名空间的配置文件示例：

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: example-cpu-mem-resourcequota
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```

可以这样应用这个 `ResourceQuota`：

```sh
kubectl apply -f example-cpu-mem-resourcequota.yaml -- namespace=<insert-namespace-here>
```

这个 `ResourceQuota` 对所选择的命名空间施加了以下限制：

- 每个容器都必须有一个内存请求、内存限制、CPU 请求和 CPU 限制。
- 所有容器的总内存请求不应超过 1 GiB
- 所有容器的总内存限制不应超过 2 GiB
- 所有容器的 CPU 请求总量不应超过 1 个 CPU
- 所有容器的总 CPU 限制不应超过 2 个 CPU
