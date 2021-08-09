# 附录 D：命名空间示例

下面的例子是为每个团队或用户组，可以使用 kubectl 命令或 YAML 文件创建一个 Kubernetes 命名空间。应避免使用任何带有 `kube` 前缀的名称，因为它可能与 Kubernetes 系统保留的命名空间相冲突。

Kubectl 命令来创建一个命名空间。

```sh
kubectl create namespace <insert-namespace-name-here>
```

要使用 YAML 文件创建命名空间，创建一个名为 my-namespace.yaml 的新文件，内容如下：

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <insert-namespace-name-here>
```

应用命名空间，使用：

```sh
kubectl create –f ./my-namespace.yaml
```

要在现有的命名空间创建新的 Pod，请切换到所需的命名空间：

```sh
kubectl config use-context <insert-namespace-here>
```

应用新的 Deployment，使用：

```sh
kubectl apply -f deployment.yaml
```

另外，也可以用以下方法将命名空间添加到 kubectl 命令中：

```sh
kubectl apply -f deployment.yaml --namespace=<insert-namespace-here>
```

或在 YAML 声明中的元数据下指定 `namespace：<insert-namespace-here>`。

一旦创建，资源不能在命名空间之间移动。必须删除该资源，然后在新的命名空间中创建。
