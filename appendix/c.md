# 附录 C：Pod 安全策略示例

下面是一个 Kubernetes Pod 安全策略的例子，它为集群中运行的容器执行了强大的安全要求。这个例子是基于[官方的 Kubernetes 文档](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)。我们鼓励管理员对该策略进行修改，以满足他们组织的要求。

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: 'docker/default,runtime/default'
    apparmor.security.beta.kubernetes.io/allowedProfileNames: 'runtime/default'
    seccomp.security.alpha.kubernetes.io/defaultProfileName: 'runtime/default'
    apparmor.security.beta.kubernetes.io/defaultProfileName: 'runtime/default'
spec:
  privileged: false # 需要防止升级到 root
    allowPrivilegeEscalation: false
    requiredDropCapabilities:
      - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim' # 假设管理员设置的 persistentVolumes 是安全的
  hostNetwork: false
  hostIPC: false
  hostPID: false
  runAsUser:
    rule: 'MustRunAsNonRoot' # 要求容器在没有 root 的情况下运行 seLinux
    rule: 'RunAsAny' # 假设节点使用的是 AppArmor 而不是 SELinux
    supplementalGroups:
      rule: 'MustRunAs'
      ranges: # 禁止添加到 root 组
        - min: 1
          max: 65535
    runAsGroup:
      rule: 'MustRunAs'
      ranges: # 禁止添加到 root 组
        - min: 1
          max: 65535
    fsGroup:
      rule: 'MustRunAs'
      ranges: # 禁止添加到 root 组
        - min: 1
          max: 65535
  readOnlyRootFilesystem: true
```

