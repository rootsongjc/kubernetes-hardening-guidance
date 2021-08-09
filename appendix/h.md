# 附录 H：加密示例

要对秘密数据进行静态加密，下面的加密配置文件提供了一个例子，以指定所需的加密类型和加密密钥。将加密密钥存储在加密文件中只能稍微提高安全性。Secret 将被加密，但密钥将在 `EncryptionConfiguration` 文件中被访问。这个例子是基于 [Kubernetes 的官方文档](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)。

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - aescbc:
    keys:
    - name: key1
      secret: <base 64 encoded secret>
  - identity: {}
```

要使用该加密文件进行静态加密，请在重启 API 服务器时设置 `--encryption-provider-config` 标志，并注明配置文件的位置。

