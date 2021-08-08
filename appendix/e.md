## 附录 E：网络策略示例

网络策略根据使用的网络插件而不同。下面是一个网络策略的例子，参考 [Kubernetes 文档](https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/)将 nginx 服务的访问限制在带有标签访问的 Pod 上。

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: example-access-nginx
  namespace: prod #这可以是任何一个命名空间，或者在不使用命名空间的情况下省略。
spec:
  podSelector:
    matchLabels:
      app: nginx
  ingress:
    - from:
      - podSelector:
        matchLabels:
          access: "true"
```

新的 `NetworkPolicy` 可以通过以下方式应用：

```sh
kubectl apply -f policy.yaml
```

一个默认的拒绝所有入口的策略：

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector: {}
  policyType:
    - Ingress
```

 一个默认的拒绝所有出口的策略：

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-egress
spec:
  podSelector: {}
  policyType:
  - Egress
```

