# 附录K：RBAC RoleBinding 和 ClusterRoleBinding 示例

要创建一个 `RoleBinding`，需创建一个 YAML 文件，内容如下：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
# 这个角色绑定允许 "jane" 读取 "your-namespace-name" 的 Pod 命名空间
# 你需要在该命名空间中已经有一个名为 "pod-reader"的角色。
kind: RoleBinding
metadata:
  name: read-pods
  namespace: your-namespace-name
  subjects: # 你可以指定一个以上的 "subject"
- kind: User
  name: jane # "name" 是大小写敏感的
  apiGroup: rbac.authorization.k8s.io
  roleRef: # "roleRef" 指定绑定到一个 Role/ClusterRole
     kind: Role # 必须是 Role 或 ClusterRole
     name: pod-reader # 这必须与你想绑定的 Role 或 ClusterRole 的名字相匹配
     apiGroup: rbac.authorization.k8s.io
```

应用 `RoleBinding`：

```sh
kubectl apply --f rolebinding.yaml
```

要创建一个`ClusterRoleBinding`，请创建一个 YAML 文件，内容如下：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
# 这个集群角色绑定允许 "manager" 组中的任何人在任何命名空间中读取 Pod 信息。
kind: ClusterRoleBinding
metadata:
  name: global-pod-reader
subjects:  # 你可以指定一个以上的 "subject"
  - kind: Group
    name: manager # Name 是大小写敏感的
    apiGroup: rbac.authorization.k8s.io
    roleRef: # "roleRef" 指定绑定到一个 Role/ClusterRole
      kind: ClusterRole # 必须是 Role 或 ClusterRole
      name: global-pod-reader # 这必须与你想绑定的 Role 或 ClusterRole 的名字相匹配
      apiGroup: rbac.authorization.k8s.io
```

应用 `RoleBinding`：

```sh
kubectl apply --f clusterrolebinding.yaml
```
