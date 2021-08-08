## 附录 J：pod-reader RBAC 角色

要创建一个 pod-reader 角色，创建一个 YAML 文件，内容如下：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: your-namespace-name
name: pod-reader
rules:
- apiGroups: [""] # "" 表示核心 API 组
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

应用角色：

```sh
kubectl apply --f role.yaml
```

要创建一个全局性的 pod-reader `ClusterRole`：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata: default
# "namespace" 被省略了，因为 ClusterRoles 没有被绑定到一个命名空间上
  name: global-pod-reader
  rules:
  -  apiGroups: [""] # "" 表示核心 API 组
     resources: ["pods"]
     verbs: ["get", "watch", "list"]
```

应用角色：

```sh
kubectl apply --f clusterrole.yaml
```
