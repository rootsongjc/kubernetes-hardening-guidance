# 认证和授权

认证和授权是限制访问集群资源的主要机制。如果集群配置错误，网络行为者可以扫描知名的 Kubernetes 端口，访问集群的数据库或进行 API 调用，而不需要经过认证。用户认证不是 Kubernetes 的一个内置功能。然而，有几种方法可以让管理员在集群中添加认证。

## 认证

> 管理员必须向集群添加一个认证方法，以实现认证和授权机制。

Kubernetes 集群有两种类型的用户：服务账户和普通用户账户。服务账户代表 Pod 处理 API 请求。认证通常由 Kubernetes 通过 ServiceAccount Admission Controller 使用承载令牌自动管理。不记名令牌被安装到 Pod 中约定俗成的位置，如果令牌不安全，可能会在集群外使用。正因为如此，对 Pod Secret 的访问应该限制在那些需要使用 Kubernetes RBAC 查看的人身上。对于普通用户和管理员账户，没有自动的用户认证方法。管理员必须在集群中添加一个认证方法，以实现认证和授权机制。

Kubernetes 假设由一个独立于集群的服务来管理用户认证。[Kubernetes 文档中](https://kubernetes.io/docs/reference/access-authn-authz/authentication)列出了几种实现用户认证的方法，包括客户端证书、承载令牌、认证插件和其他认证协议。至少应该实现一种用户认证方法。当实施多种认证方法时，第一个成功认证请求的模块会缩短评估的时间。管理员不应使用静态密码文件等弱方法。薄弱的认证方法可能允许网络行为者冒充合法用户进行认证。

匿名请求是被其他配置的认证方法拒绝的请求，并且不与任何个人用户或 Pod 相联系。在一个设置了令牌认证并启用了匿名请求的服务器中，没有令牌的请求将作为匿名请求执行。在 Kubernetes 1.6 和更新的版本中，匿名请求是默认启用的。当启用 RBAC 时，匿名请求需要 ` system:anonymous` 用户或 `system:unauthenticated` 组的明确授权。匿名请求应该通过向 API 服务器传递 `--anonymous-auth=false` 选项来禁用。启用匿名请求可能会允许网络行为者在没有认证的情况下访问集群资源。

## 基于角色的访问控制

RBAC 是根据组织内个人的角色来控制集群资源访问的一种方法。在 Kubernetes 1.6 和更新的版本中，RBAC 是默认启用的。要使用 kubectl 检查集群中是否启用了 RBAC，执行 `kubectl api-version`。如果启用，应该列出`rbac.authorization.k8s.io/v1` 的 API 版本。云 Kubernetes 服务可能有不同的方式来检查集群是否启用了 RBAC。如果没有启用 RBAC，在下面的命令中用 `--authorization-mode` 标志启动 API 服务器。

```sh
kube-apiserver --authorization-mode=RBAC
```

留下授权模式标志，如 `AlwaysAllow`，允许所有的授权请求，有效地禁用所有的授权，限制了执行最小权限的访问能力。

可以设置两种类型的权限：`Roles` 和 `ClusterRoles`。`Roles` 为特定命名空间设置权限，而 `ClusterRoles` 则为所有集群资源设置权限，而不考虑命名空间。`Roles` 和 `ClusterRoles` 只能用于添加权限。没有拒绝规则。如果一个集群被配置为使用 RBAC，并且匿名访问被禁用，Kubernetes API 服务器将拒绝没有明确允许的权限。[**附录** J](appendix/j.md) 中显示了一个 RBAC 角色的例子：`pod-reader` RBAC 角色。

一个 `Role` 或 `ClusterRole` 定义了一个权限，但并没有将该权限与一个用户绑定。`RoleBindings` 和 `ClusterRoleBindings` 用于将一个 `Roles` 或 `ClusterRoles` 与一个用户、组或服务账户联系起来。角色绑定将角色或集群角色的权限授予定义的命名空间中的用户、组或服务账户。`ClusterRoles` 是独立于命名空间而创建的，然后可以使用 `RoleBinding` 来限制命名空间的范围授予个人。`ClusterRoleBindings` 授予用户、群组或服务账户跨所有集群资源的 `ClusterRoles`。RBAC `RoleBinding` 和 `ClusterRoleBinding` 的例子在[**附录 K：RBAC `RoleBinding` 和 `ClusterRoleBinding` 示例**](appendix/k.md)中。

要创建或更新 `Roles` 和 `ClusterRoles`，用户必须在同一范围内拥有新角色所包含的权限，或者拥有对 `rbac.authorization.k8s.io` API 组中的 `Roles` 或 `ClusterRoles` 资源执行升级动词的明确权限。创建绑定后，`Roles` 或 `ClusterRoles` 是不可改变的。要改变一个角色，必须删除该绑定。

分配给用户、组和服务账户的权限应该遵循最小权限原则，只给资源以必要的权限。用户或用户组可以被限制在所需资源所在的特定命名空间。默认情况下，为每个命名空间创建一个服务账户，以便 Pod 访问 Kubernetes API。可以使用 RBAC 策略来指定每个命名空间的服务账户的允许操作。对 Kubernetes API 的访问是通过创建 RBAC 角色或 `ClusterRoles` 来限制的，该角色具有适当的 API 请求动词和所需的资源，该行动可以应用于此。有一些工具可以通过打印用户、组和服务账户及其相关分配的 `Roles` 和 `ClusterRoles` 来帮助审计 RBAC 策略。