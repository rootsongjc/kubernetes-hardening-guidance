# 附录 B：只读文件系统的部署模板示例

下面是一个使用只读根文件系统的 Kubernetes 部署模板的例子。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web
    name: web
  spec:
    selector:
      matchLabels:
        app: web
    template:
      metadata:
        labels:
          app: web
          name: web
      spec:
        containers:
        - command: ["sleep"]
          args: ["999"]
          image: ubuntu:latest
          name: web
          securityContext:
            readOnlyRootFilesystem: true #使容器的文件系统成为只读
          volumeMounts:
            - mountPath: /writeable/location/here #创建一个可写卷
              name: volName
        volumes:
        - emptyDir: {}
          name: volName
```
