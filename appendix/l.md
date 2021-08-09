# 附录 L：审计策略

下面是一个审计策略，它以最高级别记录所有审计事件：

```yaml
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
  - level: RequestResponse
# 这个审计策略记录了 RequestResponse 级别的所有审计事件
```

这种审计策略在最高级别上记录所有事件。如果一个组织有可用的资源来存储、解析和检查大量的日志，那么在最高级别上记录所有事件是一个很好的方法，可以确保当事件发生时，所有必要的背景信息都出现在日志中。如果资源消耗和可用性是一个问题，那么可以建立更多的日志规则来降低非关键组件和常规非特权操作的日志级别，只要满足系统的审计要求。如何建立这些规则的例子可以在 [Kubernetes 官方文档](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/)中找到。
