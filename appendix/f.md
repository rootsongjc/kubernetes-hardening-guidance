## 附录 F：LimitRange 示例

在 Kubernetes 1.10 和更新版本中，`LimitRange` 支持被默认启用。下面的 YAML 文件为每个容器指定了一个 `LimitRange`，其中有一个默认的请求和限制，以及最小和最大的请求。

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-min-max-demo-lr
spec:
  limits
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    max:
      cpu: 2
    min:
      cpu 0.5
    type: Container
```

`LimitRange` 可以应用于命名空间，使用：

```sh
kubectl apply -f <example-LimitRange>.yaml --namespace=<Enter-Namespace>
```

在应用了这个 `LimitRange` 配置的例子后，如果没有指定，命名空间中创建的所有容器都会被分配到默认的 CPU 请求和限制。命名空间中的所有容器的 CPU 请求必须大于或等于最小值，小于或等于最大 CPU 值，否则容器将不会被实例化。