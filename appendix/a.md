# 附录 A：非 root 应用的 Dockerfile 示例

下面的例子是一个 Dockerfile，它以非 root 用户和非 group 成员身份运行一个应用程序。

```docker
FROM ubuntu:latest
# 升级和安装 make 工具
RUN apt update && apt install -y make
# 从一个名为 code 的文件夹中复制源代码，并使用 make 工具构建应用程序。
COPY ./code
RUN make /code
# 创建一个新的用户（user1）和新的组（group1）；然后切换到该用户的上下文中。
RUN useradd user1 && groupadd group1
USER user1:group1
# 设置容器的默认入口
CMD /code/app
```

