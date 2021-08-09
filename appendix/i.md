# 附录 I：KMS 配置实例

要用密钥管理服务（KMS）提供商插件来加密 Secret，可以使用以下加密配置 YAML 文件的例子来为提供商设置属性。这个例子是基于 [Kubernetes 的官方文档](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)。

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - kms:
    name: myKMSPlugin
    endpoint: unix://tmp/socketfile.sock
    cachesize: 100
    timeout: 3s
  - identity: {}
```

要配置 API 服务器使用 KMS 提供商，请将 `--encryption-provider-config` 标志与配置文件的位置一起设置，并重新启动 API 服务器。

要从本地加密提供者切换到 KMS，请将 `EncryptionConfiguration` 文件中的 KMS 提供者部分添加到当前加密方法之上，如下所示。

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
    providers:
    - kms:
      name: myKMSPlugin
      endpoint: unix://tmp/socketfile.sock
      cachesize: 100
      timeout: 3s
    - aescbc:
      keys:
      - name: key1
        secret: <base64 encoded secret>
```

重新启动 API 服务器并运行下面的命令来重新加密所有与 KMS 供应商的 Secret。

```sh
kubectl get secrets --all-namespaces -o json | kubectl replace -f -
```
