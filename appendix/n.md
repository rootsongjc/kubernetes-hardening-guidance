# 附录 N：webhook 配置

YAML 文件示例：

```yaml
apiVersion: v1
kind: Config
preferences: {}
clusters:
  - name: example-cluster
    cluster:
      server: http://127.0.0.1:8080
      #web endpoint address for the log files to be sent to
      name: audit-webhook-service
    users:
  - name: example-users
    user:
      username: example-user
      password: example-password
  contexts:
  - name: example-context
    context:
      cluster: example-cluster
      user: example-user
   current-context: example-context
#source: https://dev.bitolog.com/implement-audits-webhook/
```

由 webhook 发送的审计事件是以 HTTP POST 请求的形式发送的，请求体中包含 JSON 审计事件。指定的地址应该指向一个能够接受和解析这些审计事件的端点，无论是第三方服务还是内部配置的端点。

向 `kube-apiserve`r 提交 webhook 配置文件的标志示例：

在控制面编辑 `kube-apiserver.yaml` 文件

```sh
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

在 kube-apiserver.yaml 文件中添加以下文字

```
--audit-webhook-config-file=/etc/kubernetes/policies/webhook-policy.yaml
--audit-webhook-initial-backoff=5
--audit-webhook-mode=batch
--audit-webhook-batch-buffer-size=5
```

`audit-webhook-initial-backoff` 标志决定了在一个初始失败的请求后要等待多长时间才能重试。可用的 webhook 模式有 `batch`、`block` 和 `blocking-stric` 的。当使用批处理模式时，有可能配置最大等待时间、缓冲区大小等。Kubernetes 官方文档包含了其他配置选项的更多细节[审计](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/)和 [`kube-apiserver`](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)。
