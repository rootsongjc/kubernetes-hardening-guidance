# 附录 M：向 kube-apiserver 提交审计策略文件的标志示例

在控制平面，用文本编辑器打开 `kube-apiserver.yaml` 文件。编辑 `kube-apiserver` 配置需要管理员权限。

```sh
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

在 `kube-apiserver.yaml` 文件中添加以下文字：

```
--audit-policy-file=/etc/kubernetes/policy/audit-policy.yaml --audit-log-path=/var/log/audit.log --audit-log-maxage=1825
```

`audit-policy-file` 标志应该设置为审计策略的路径，而 `audit-log-path` 标志应该设置为所需的审计日志写入的安全位置。还有一些其他的标志，比如这里显示的 `audit-log-maxage` 标志，它规定了日志应该被保存的最大天数，还有一些标志用于指定要保留的最大审计日志文件的数量，最大的日志文件大小（兆字节）等等。启用日志记录的唯一必要标志是 `audit-policy-file` 和 `audit-log-path` 标志。其他标志可以用来配置日志，以符合组织的政策。

如果用户的 `kube-apiserver` 是作为 Pod 运行的，那么就有必要挂载卷，并配置策略和日志文件位置的 `hostPath` 以保留审计记录。这可以通过在 [Kubernetes 文档中](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/)指出的 `kube-apiserver.yaml` 文件中添加以下部分来完成：

```yaml
  volumeMounts:
    - mountPath: /etc/kubernetes/audit-policy.yaml
      name: audit
      readOnly: true
    - mountPath: /var/log/audit.log
      name: audit-log
      readOnly: false
volumes:
- hostPath:
    path: /etc/kubernetes/audit-policy.yaml
    type: File
  name: audit
- hostPath:
    path: /var/log/audit.log
    type: FileOrCreate
  name: audit-log
```
