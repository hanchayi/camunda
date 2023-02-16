<!--
https://docs.camunda.io/docs/self-managed/platform-deployment/docker/
 -->

# Docker

此页面将指导您完成 Camunda Platform 8 Docker 映像以及如何使用 Docker Compose 在开发人员设置中运行该平台。

## Docker 镜像

我们通过 Dockerhub 提供 Docker 镜像。所有这些图像都可以公开访问（Web Modeler Beta 除外）。

> 提供的 Docker 映像仅支持在 Linux 系统上用于生产。 Windows 或 macOS 仅支持开发环境。



|  组件   | 镜像  |  链接到配置选项 |
|  ----  | ----  | ---- |
| Zeebe  | [camunda/zeebe:latest](https://hub.docker.com/r/camunda/zeebe)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |
| Operate  | [	camunda/operate:latest](https://hub.docker.com/r/camunda/operate)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |
| Tasklist  | [camunda/tasklist:latest](https://hub.docker.com/r/camunda/tasklist)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |
| Identity  | [camunda/identity:latest](https://hub.docker.com/r/camunda/identity)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |
| Optimize  | [camunda/optimize:latest](https://hub.docker.com/r/camunda/optimize)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |
| Connectors  | [camunda/connectors:latest](https://hub.docker.com/r/camunda/connectors)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |
| Connectors Bundle  | [camunda/connectors-bundle:latest](https://hub.docker.com/r/camunda/connectors-bundle)  | [环境变量](/self-managed/zeebe-deployment/configuration/environment-variables/) |


Zeebe 是唯一经常作为独立组件单独运行的组件。在这种情况下，它不需要任何其他东西，所以一个简单的 docker run 就足够了：

``` bash
docker run --name zeebe -p 26500-26502:26500-26502 camunda/zeebe:latest
```

这将为您提供一个暴露以下端口的单个代理节点：
- 26500：Gateway API（这是客户端需要使用的端口）
- 26501：命令 API（内部，网关到代理gateway-to-broker）
- 26502：内部 API（内部，代理到代理broker-to-broker）

## Web建模器

Web Modeler 的 Docker 镜像不可公开访问，但企业客户只能从 Camunda 的私有 Docker 源获得。

|  组件   | 镜像  |
|  ----  | ----  |
| Backend (restapi) | registry.camunda.cloud/web-modeler-ee/modeler-restapi:0.4.0-beta |
| Frontend (webapp) | registry.camunda.cloud/web-modeler-ee/modeler-webapp:0.4.0-beta |
| WebSocket server | registry.camunda.cloud/web-modeler-ee/modeler-websockets:0.4.0-beta |

要拉取图像，您首先需要使用从 Camunda 收到的凭据登录：

``` bash
$ docker login registry.camunda.cloud
Username: your_username
Password: ******
Login Succeeded
```

请参阅下面的说明，了解如何将 Web Modeler 图像与 Docker Compose 结合使用。

## Docker Compose

用于运行 Zeebe、Operate、Tasklist、Optimize、Identity 和 Connectors Bundle 的 Docker Compose 配置在 camunda 平台[仓库](https://github.com/camunda/camunda-platform/blob/main/docker-compose.yaml)中可用。按照[README](https://github.com/camunda/camunda-platform#using-docker-compose)中的说明进行操作。

这个 Docker Compose 配置有两个目的：

1. 它可用于在本地启动开发环境。
2. 它记录了各种组件需要如何连接在一起。

> 我们建议在本地环境中使用 Helm + KIND 而不是 Docker Compose，因为 Helm 配置经过实战测试并且更接近生产系统。

### Web Modeler

camunda-platform 存储库中还提供了用于运行 Web Modeler 的附加 [Docker Compose](https://github.com/camunda/camunda-platform/blob/main/docker-compose-web-modeler-beta.yaml) 配置。按照 [README](https://github.com/camunda/camunda-platform#web-modeler-self-managed-beta-release) 中的说明使用此配置。

## 配置提示

### Zeebe

#### Volumes

默认数据卷在/usr/local/zeebe/data 下。它包含所有应该保留的数据。

#### Configuration

Zeebe 配置位于 /usr/local/zeebe/config/application.yaml。日志记录配置位于 /usr/local/zeebe/config/log4j2.xml。

也可以使用环境变量更改 Docker 映像的配置。配置模板文件还包含有关用于每个配置设置的环境变量的信息。

常用的环境变量：

- ZEEBE_LOG_LEVEL - 设置 Zeebe 记录器的日志级别（默认值：信息）
- ZEEBE_BROKER_NETWORK_HOST - 设置要绑定的主机地址而不是容器的 IP
- ZEEBE_BROKER_CLUSTER_INITIALCONTACTPOINTS - 在集群设置中设置其他代理的联系点

### 优化

一些配置属性是可选的并且具有默认值。在下表中查看这些属性及其默认值的说明：


|  组件   | 描述  |  默认值 |
|  ----  | ----  |  ---- |
|  SPRING_PROFILES_ACTIVE  | 在自我管理模式下启动优化。  | ---- |
|  CAMUNDA_OPTIMIZE_IDENTITY_ISSUER_URL  | Optimize 可以访问身份的 URL。  | ---- |

例如这个 `docker-compose` 配置：

```
optimize:
    container_name: optimize
    image: camunda/optimize:latest
    ports:
        - 8090:8090
    environment:
        - SPRING_PROFILES_ACTIVE=ccsm
        - CAMUNDA_OPTIMIZE_IDENTITY_ISSUER_URL=http://localhost:9090
        - CAMUNDA_OPTIMIZE_IDENTITY_ISSUER_BACKEND_URL=http://keycloak:8080/auth/realms/camunda-platform
        - CAMUNDA_OPTIMIZE_IDENTITY_CLIENTID=optimize
        - CAMUNDA_OPTIMIZE_IDENTITY_CLIENTSECRET=secret
        - CAMUNDA_OPTIMIZE_IDENTITY_AUDIENCE=optimize-api
        - OPTIMIZE_ELASTICSEARCH_HOST=localhost
        - OPTIMIZE_ELASTICSEARCH_HTTP_PORT=9200
        - CAMUNDA_OPTIMIZE_SECURITY_AUTH_COOKIE_SAME_SITE_ENABLED=false
        - CAMUNDA_OPTIMIZE_ENTERPRISE=false
        - CAMUNDA_OPTIMIZE_ZEEBE_ENABLED=true
        - CAMUNDA_OPTIMIZE_ZEEBE_NAME=zeebe-record
        - CAMUNDA_OPTIMIZE_ZEEBE_PARTITION_COUNT=1
        - CAMUNDA_OPTIMIZE_SHARING_ENABLED=true
        - CAMUNDA_OPTIMIZE_UI_LOGOUT_HIDDEN=true
        - SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_JWK_SET_URI=https://weblogin.cloud.company.com/.well-known/jwks.json
        - OPTIMIZE_API_ACCESS_TOKEN=secret
```
