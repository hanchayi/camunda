<!--
https://docs.camunda.io/docs/self-managed/platform-deployment/overview/
 -->

# Camunda Platform 8 安装概览

本章包含为想要部署和运行 Camunda Platform 8 Self-Managed 的​​用户提供的信息，通常是在您的自控云（公共或私有）甚至您自己的硬件上。

## 组件

Camunda Platform 8包含以下组件
- Zeebe 代理和网关
- Operate (需要 Elasticsearch)
- Tasklist (需要 Elasticsearch)
- Connectors
- Optimize (需要 Elasticsearch)
- Identity (需要 Keycloak)
- Web Modeler (需要 Identity, Keycloak, 和 PostgreSQL)

除 Web Modeler 之外的所有组件都是单个 Java 应用程序。
根据您的需要，您可能不需要上述所有组件即可成功使用 Camunda Platform 8。

## 支持的环境
有关支持的环境（例如 Java 或 Elasticsearch 版本）的详细信息，请参阅[支持的环境](/reference/supported-environments)。

## 部署方式

您有以下选项以自我管理的方式运行上述组件：
- [Helm/Kubernetes](/self-managed/platform-deployment/helm-kubernetes/overview/): 我们强烈建议使用 Kubernetes 在生产环境中运行 Camunda Platform 8。除了现有的 Kubernetes，我们还正式支持各种供应商，如 Red Hat OpenShift 和 Amazon EKS。此外，将 Kubernetes 与 Minikube 或 KIND 结合使用可能是一个有趣的环境，可以在开发人员机器上本地运行 Camunda Platform 8。

- [Docker](/self-managed/platform-deployment/docker/): 您可以运行提供的组件的 Docker 映像，也可以在生产环境中运行。为了您的方便，我们提供了一个 Docker Compose 配置来在开发者机器上运行 Camunda Platform 8。请注意，Docker Compose 配置并未针对生产使用进行优化，而是针对本地开发进行了优化。

- [手动](/self-managed/platform-deployment/manual/): 如果本地或虚拟机提供受支持的 Java 虚拟机 (JVM)，您可以在本地或虚拟机上运行 Java 应用程序。这使您可以在虚拟机或裸机上运行 Camunda，并提供极大的灵活性。但是，您需要配置组件的详细信息才能自己正确交互。我们认为这是最后的手段。请注意，Zeebe 的生产使用不支持 Windows/Mac。

## 部署建议

正如您在下面看到的，我们尽可能推荐 SaaS，因为 Camunda 会完成繁重的工作并为您提供一切服务。这让您高枕无忧，可以专注于重要的工作。如果 SaaS 不是一个选项，我们对您应该如何安装 Camunda Platform 8 有强烈的意见，具体取决于目标（生产或开发）。

### 生产

对于生产使用，如果 Camunda 提供的 SaaS 不适合您，我们强烈建议您使用真正的 Kubernetes 集群和我们的 Helm 图表

我们支持以下生产部署选项（顺序表示偏好）：

1. SaaS
2. Helm/Kubernetes 独立于托管位置，例如 OpenShift、EKS 或 GKE
3. Docker 映像以及您选择的基础架构即代码 (IaC) 工具
4. 手动使用您选择的基础设施即代码 (IaC) 工具。

### 开发

对于开发用途，如果 Camunda 提供的 SaaS 不适合您，我们强烈建议您在 KIND 上使用我们的 Helm 图表。这些 Helm 图表经过实战测试，可为您提供接近生产的体验。

我们支持以下开发部署选项（顺序表示偏好）：
1. Sass
2. Helm/Kubernetes
3. 如果您只需要 Zeebe 代理，则手动作为最后的手段。我们不建议以这种方式设置整个工具链。

## 获取帮助

如果您对使用 Zeebe 进行部署有任何疑问或反馈，我们鼓励您访问：

- [用户论坛](https://forum.camunda.io/)
- [Slack](https://camunda-slack-invite.herokuapp.com/)

