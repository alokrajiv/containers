---

copyright:
  years: 2014, 2018
lastupdated: "2018-10-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}



# 集群联网故障诊断
{: #cs_troubleshoot_network}

在使用 {{site.data.keyword.containerlong}} 时，请考虑用于对集群联网进行故障诊断的以下方法。
{: shortdesc}

通过 Ingress 连接到应用程序时遇到问题？请尝试[调试 Ingress](cs_troubleshoot_debug_ingress.html)。
{: tip}

## 无法通过 LoadBalancer 服务连接到应用程序
{: #cs_loadbalancer_fails}

{: tsSymptoms}
您已通过在集群中创建 LoadBalancer 服务来向公众公开应用程序。但尝试使用负载均衡器的公共 IP 地址连接到应用程序时，连接失败或超时。

{: tsCauses}
由于以下某种原因，LoadBalancer 服务可能未正常运行：

-   集群为免费集群，或者为仅具有一个工作程序节点的标准集群。
-   集群尚未完全部署。
-   LoadBalancer 服务的配置脚本包含错误。

{: tsResolve}
要对 LoadBalancer 服务进行故障诊断，请执行以下操作：

1.  检查是否设置了完全部署的标准集群，以及该集群是否至少有两个工作程序节点，以确保 LoadBalancer 服务具有高可用性。

  ```
  ibmcloud ks workers <cluster_name_or_ID>
  ```
  {: pre}

    在 CLI 输出中，确保工作程序节点的 **Status** 显示 **Ready**，并且 **Machine Type** 显示除了 **free** 之外的机器类型。

2.  检查 LoadBalancer 服务的配置文件是否准确。

    ```
apiVersion: v1
    kind: Service
    metadata:
      name: myservice
    spec:
      type: LoadBalancer
      selector:
        <selector_key>:<selector_value>
      ports:
       - protocol: TCP
         port: 8080
    ```
    {: pre}

    1.  检查是否已将 **LoadBalancer** 定义为服务类型。
    2.  在 LoadBalancer 服务的 `spec.selector` 部分中，确保 `<selector_key>` 和 `<selector_value>` 与部署 YAML 的 `spec.template.metadata.labels` 部分中使用的键/值对相同。如果标签不匹配，LoadBalancer 服务中的 **Endpoints** 部分将显示 **<none>**，并且无法从因特网访问应用程序。
    3.  检查是否使用的是应用程序侦听的**端口**。

3.  检查 LoadBalancer 服务，并查看 **Events** 部分以查找潜在错误。

    ```
    kubectl describe service <myservice>
    ```
    {: pre}

    查找以下错误消息：
    

    <ul><li><pre class="screen"><code>Clusters with one node must use services of type NodePort</code></pre></br>要使用 LoadBalancer 服务，您必须有至少包含两个工作程序节点的标准集群。
    </li>
    <li><pre class="screen"><code>No cloud provider IPs are available to fulfill the load balancer service request. Add a portable subnet to the cluster and try again</code></pre></br>此错误消息指示没有任何可移植公共 IP 地址可供分配给 LoadBalancer 服务。请参阅<a href="cs_subnets.html#subnets">向集群添加子网</a>，以了解有关如何为集群请求可移植公共 IP 地址的信息。有可移植的公共 IP 地址可供集群使用后，将自动创建 LoadBalancer 服务。
    </li>
    <li><pre class="screen"><code>Requested cloud provider IP <cloud-provider-ip> is not available. The following cloud provider IPs are available: <available-cloud-provider-ips></code></pre></br>您使用 **loadBalancerIP** 部分为 LoadBalancer 服务定义了可移植公共 IP 地址，但此可移植公共 IP 地址在可移植公共子网中不可用。在配置脚本的 **loadBalancerIP** 部分中，除去现有 IP 地址，然后添加其中一个可用的可移植公共 IP 地址。您还可以从脚本中除去 **loadBalancerIP** 部分，以便可以自动分配可用的可移植公共 IP 地址。</li>
    <li><pre class="screen"><code>No available nodes for load balancer services</code></pre>您没有足够的工作程序节点可部署 LoadBalancer 服务。一个原因可能是您已部署了包含多个工作程序节点的标准集群，但供应这些工作程序节点失败。
    </li>
    <ol><li>列出可用的工作程序节点。</br><pre class="codeblock"><code>kubectl get nodes</code></pre></li>
    <li>如果找到了至少两个可用的工作程序节点，请列出工作程序节点详细信息。</br><pre class="codeblock"><code>ibmcloud ks worker-get [&lt;cluster_name_or_ID&gt;] &lt;worker_ID&gt;</code></pre></li>
    <li>确保分别由 <code>kubectl get nodes</code> 和 <code>ibmcloud ks [&lt;cluster_name_or_ID&gt;] worker-get</code> 命令返回的工作程序节点的公用和专用 VLAN 标识相匹配。</li></ol></li></ul>

4.  如果使用定制域来连接到 LoadBalancer 服务，请确保定制域已映射到 LoadBalancer 服务的公共 IP 地址。
    1.  找到 LoadBalancer 服务的公共 IP 地址。

        ```
kubectl describe service <service_name> | grep "LoadBalancer Ingress"
        ```
        {: pre}

    2.  检查定制域是否已映射到指针记录 (PTR) 中 LoadBalancer 服务的可移植公共 IP 地址。

<br />


## 无法通过 Ingress 连接到应用程序
{: #cs_ingress_fails}

{: tsSymptoms}
您已通过为集群中的应用程序创建 Ingress 资源来向公众公开应用程序。但尝试使用 Ingress 应用程序负载均衡器 (ALB) 的公共 IP 地址或子域连接到应用程序时，连接失败或超时。

{: tsResolve}
首先，检查集群是否已完全部署并且每个专区至少有 2 个工作程序节点，以确保 ALB 的高可用性。
    ```
        ibmcloud ks workers <cluster_name_or_ID>
    ```
    {: pre}

在 CLI 输出中，确保工作程序节点的 **Status** 显示 **Ready**，并且 **Machine Type** 显示除了 **free** 之外的机器类型。

* 如果标准集群已完全部署并且每个专区至少有 2 个工作程序节点，但是 **Ingress 子域**不可用，请参阅[无法获取 Ingress ALB 的子域](cs_troubleshoot_network.html#cs_subnet_limit)。
* 对于其他问题，请遵循[调试 Ingress](cs_troubleshoot_debug_ingress.html) 中的步骤来对 Ingress 设置进行故障诊断。

<br />


## Ingress 应用程序负载均衡器私钥问题
{: #cs_albsecret_fails}

{: tsSymptoms}
将 Ingress 应用程序负载均衡器 (ALB) 私钥部署到集群后，在 {{site.data.keyword.cloudcerts_full_notm}} 中查看证书时，`Description` 字段未使用该私钥名称进行更新。

列出有关 ALB 私钥的信息时，阶段状态为 `*_failed`。例如，`create_failed`、`update_failed` 或 `delete_failed`。

{: tsResolve}
查看以下导致 ALB 私钥可能失败的原因以及对应的故障诊断步骤：

<table>
<caption>对 Ingress 应用程序负载均衡器私钥进行故障诊断</caption>
 <thead>
 <th>问题原因</th>
 <th>解决方法</th>
 </thead>
 <tbody>
 <tr>
 <td>您没有下载和更新证书数据所需的访问角色。</td>
 <td>请咨询帐户管理员，要求为您分配对 {{site.data.keyword.cloudcerts_full_notm}} 实例的**管理员**和**作者**角色。有关更多信息，请参阅 {{site.data.keyword.cloudcerts_short}} 的<a href="/docs/services/certificate-manager/access-management.html#managing-service-access-roles">管理服务访问</a>。</td>
 </tr>
 <tr>
 <td>创建、更新或除去时提供的证书 CRN 所属的帐户与集群不同。</td>
 <td>检查提供的证书 CRN 导入到的 {{site.data.keyword.cloudcerts_short}} 服务实例是否与集群部署在同一帐户中。</td>
 </tr>
 <tr>
 <td>创建时提供的证书 CRN 不正确。</td>
 <td><ol><li>检查提供的证书 CRN 字符串的准确性。</li><li>如果发现证书 CRN 是准确的，请尝试更新私钥：<code>ibmcloud ks alb-cert-deploy --update --cluster &lt;cluster_name_or_ID&gt; --secret-name &lt;secret_name&gt; --cert-crn &lt;certificate_CRN&gt;</code></li><li>如果此命令生成 <code>update_failed</code> 阶段状态，请除去私钥：<code>ibmcloud ks alb-cert-rm --cluster &lt;cluster_name_or_ID&gt; --secret-name &lt;secret_name&gt;</code></li><li>重新部署私钥：<code>ibmcloud ks alb-cert-deploy --cluster &lt;cluster_name_or_ID&gt; --secret-name &lt;secret_name&gt; --cert-crn &lt;certificate_CRN&gt;</code></li></ol></td>
 </tr>
 <tr>
 <td>更新时提供的证书 CRN 不正确。</td>
 <td><ol><li>检查提供的证书 CRN 字符串的准确性。</li><li>如果发现证书 CRN 是准确的，请除去私钥：<code>ibmcloud ks alb-cert-rm --cluster &lt;cluster_name_or_ID&gt; --secret-name &lt;secret_name&gt;</code></li><li>重新部署私钥：<code>ibmcloud ks alb-cert-deploy --cluster &lt;cluster_name_or_ID&gt; --secret-name &lt;secret_name&gt; --cert-crn &lt;certificate_CRN&gt;</code></li><li>尝试更新私钥：<code>ibmcloud ks alb-cert-deploy --update --cluster &lt;cluster_name_or_ID&gt; --secret-name &lt;secret_name&gt; --cert-crn &lt;certificate_CRN&gt;</code></li></ol></td>
 </tr>
 <tr>
 <td>{{site.data.keyword.cloudcerts_long_notm}} 服务遭遇停机时间。</td>
 <td>检查 {{site.data.keyword.cloudcerts_short}} 服务是否已启动并在运行。</td>
 </tr>
 </tbody></table>

<br />


## 无法获取 Ingress ALB 的子域
{: #cs_subnet_limit}

{: tsSymptoms}
运行 `ibmcloud ks cluster-get <cluster>` 时，集群处于 `normal` 状态，但没有 **Ingress 子域**可用。

您可能会看到类似于以下内容的错误消息。

```
There are already the maximum number of subnets permitted in this VLAN.
```
{: screen}

{: tsCauses}
在标准集群中，首次在某个专区中创建集群时，会自动在 IBM Cloud Infrastructure (SoftLayer) 帐户中供应该专区中的公用 VLAN 和专用 VLAN。在该专区中，会在指定的公用 VLAN 上请求 1 个公共可移植子网，并在指定的专用 VLAN 上请求 1 个专用可移植子网。对于 {{site.data.keyword.containerlong_notm}}，VLAN 限制为 40 个子网。如果某个专区中集群的 VLAN 已达到该限制，那么供应 **Ingress 子域**会失败。

要查看 VLAN 的子网数，请执行以下操作：
1.  在 [IBM Cloud Infrastructure (SoftLayer) 控制台](https://control.bluemix.net/)中，选择**网络** > **IP 管理** > **VLAN**。
2.  单击用于创建集群的 VLAN 的 **VLAN 编号**。查看**子网**部分以了解是否存在 40 个或更多子网。

{: tsResolve}
如果需要新的 VLAN，请通过[联系 {{site.data.keyword.Bluemix_notm}} 支持](/docs/infrastructure/vlans/order-vlan.html#order-vlans)进行订购。然后，[创建集群](cs_cli_reference.html#cs_cluster_create)以使用这一新的 VLAN。

如果有其他 VLAN 可用，那么可以在现有集群中[设置 VLAN 生成](/docs/infrastructure/vlans/vlan-spanning.html#vlan-spanning)。在此之后，即可将新的工作程序节点添加到集群，这些节点将使用具有可用子网的其他 VLAN。要检查是否已启用 VLAN 生成，请使用 `ibmcloud ks vlan-spanning-get` [命令](cs_cli_reference.html#cs_vlan_spanning_get)。

如果并未使用 VLAN 中的所有子网，那么可以在集群中复用子网。
1.  检查要使用的子网是否可用。**注**：使用的 Infrastructure 帐户可能在多个 {{site.data.keyword.Bluemix_notm}} 帐户之间共享。在这种情况下，即便运行 `ibmcloud ks subnets` 命令来查看 **Bound Cluster** 的子网，也只能看到您的集群的信息。请与 Infrastructure 帐户所有者核实以确保这些子网可用，并且未由其他任何帐户或团队使用。

2.  使用 `--no-subnet` 选项[创建集群](cs_cli_reference.html#cs_cluster_create)，以便该服务不会尝试创建新的子网。指定专区和具有可供复用的子网的 VLAN。

3.  使用 `ibmcloud ks cluster-subnet-add` [命令](cs_cli_reference.html#cs_cluster_subnet_add)将现有子网添加到集群。有关更多信息，请参阅[在 Kubernetes 集群中添加或复用定制和现有子网](cs_subnets.html#custom)。

<br />


## Ingress ALB 未部署在专区中
{: #cs_multizone_subnet_limit}

{: tsSymptoms}
具有多专区集群并运行 `ibmcloud ks albs <cluster>` 时，某个专区中未部署任何 ALB。例如，如果在 3 个专区中有工作程序节点，那么可能会看到类似以下内容的输出，其中公共 ALB 未部署到第三个专区。
```
ALB ID                                            Enabled   Status     Type      ALB IP   
private-cr96039a75fddb4ad1a09ced6699c88888-alb1   false     disabled   private   -   
private-cr96039a75fddb4ad1a09ced6699c88888-alb2   false     disabled   private   -   
private-cr96039a75fddb4ad1a09ced6699c88888-alb3   false     disabled   private   -   
public-cr96039a75fddb4ad1a09ced6699c88888-alb1    true      enabled    public    169.xx.xxx.xxx
public-cr96039a75fddb4ad1a09ced6699c88888-alb2    true      enabled    public    169.xx.xxx.xxx
```
{: screen}

{: tsCauses}
在每个专区中，会在指定的公用 VLAN 上请求 1 个公共可移植子网，并在指定的专用 VLAN 上请求 1 个专用可移植子网。对于 {{site.data.keyword.containerlong_notm}}，VLAN 限制为 40 个子网。如果某个专区中集群的公用 VLAN 已达到该限制，那么供应该专区的公共 Ingress ALB 会失败。

{: tsResolve}
要检查 VLAN 上的子网数量以及了解如何获取其他 VLAN 的步骤，请参阅[无法为 Ingress ALB 获取子域](#cs_subnet_limit)。

<br />


## 60 秒后，通过 WebSocket 的连接关闭
{: #cs_ingress_websocket}

{: tsSymptoms}
Ingress 服务公开使用 WebSocket 的应用程序。但是，客户机与 WebSocket 应用程序之间的连接会在它们之间不发送流量 60 秒后关闭。

{: tsCauses}
由于以下某个原因停止活动 60 秒后，与 WebSocket 应用程序的连接可能断开：

* 因特网连接具有一个代理或防火墙，不容许长时间连接。
* ALB 到 WebSocket 应用程序的超时终止连接。

{: tsResolve}
为避免连接在停止活动 60 秒后关闭：

1. 如果通过代理或防火墙连接到 WebSocket 应用程序，确保未将代理或防火墙配置为自动终止长时间连接。

2. 要保持连接活动，您可以增大超时值或者在应用程序中设置脉动信号。
<dl><dt>更改超时</dt>
<dd>增大 ALB 配置中 `proxy-read-timeout` 的值。例如，要将超时从 `60s` 更改为更大的值，例如，`300s`，请将以下[注释](cs_annotations.html#connection)添加到 Ingress 资源文件：`ingress.bluemix.net/proxy-read-timeout: "serviceName=<service_name> timeout=300s"`。将更改集群中所有公共 ALB 的超时。</dd>
<dt>设置脉动信号</dt>
<dd>如果不想要更改 ALB 的缺省读取超时值，请在 WebSocket 应用程序中设置脉动信号。在使用框架（例如，[WAMP ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://wamp-proto.org/)）设置脉动信号协议时，应用程序的上游服务器按照时间间隔定期发送“ping”消息，并且客户机以“pong”消息进行响应。将脉动信号间隔设置为 58 秒或更小，从而在实施 60 秒超时前，“ping/pong”流量保持连接打开。</dd></dl>

<br />


## 使用有污点的节点时源 IP 保留失败
{: #cs_source_ip_fails}

{: tsSymptoms}
您通过将服务配置文件中的 `externalTrafficPolicy` 更改为 `Local`，为 [ LoadBalancer](cs_loadbalancer.html#node_affinity_tolerations) 或 [Ingress ALB](cs_ingress.html#preserve_source_ip) 服务启用了源 IP 保留。但是，没有流量到达应用程序的后端服务。

{: tsCauses}
为 LoadBalancer 或 Ingress ALB 服务启用了源 IP 保留后，会保留客户机请求的源 IP 地址。服务仅将流量转发至同一工作程序节点上的应用程序 pod，以确保请求包的 IP 地址不变。通常，LoadBalancer 或 Ingress ALB 服务 pod 会部署到应用程序 pod 所部署到的工作程序节点上。但是，在某些情况下，服务 pod 和应用程序 pod 可能未安排到同一工作程序节点上。如果在工作程序节点上使用了 [Kubernetes 污点 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/)，那么将阻止任何没有污点容忍度的 pod 在有污点的工作程序节点上运行。根据使用的污点类型，源 IP 保留可能无效：

* **边缘节点污点**：您向集群中每个公用 VLAN 上的两个或更多工作程序节点[添加了 `dedicated=edge` 标签](cs_edge.html#edge_nodes)，以确保 Ingress 和负载均衡器 pod 仅部署到这些工作程序节点。然后，您还[污染了这些边缘节点](cs_edge.html#edge_workloads)，以防止其他任何工作负载在边缘节点上运行。但是，您未将边缘节点亲缘关系规则和容忍度添加到应用程序部署。因此，应用程序 pod 无法安排在服务 pod 所在的有污点节点上，所以没有任何流量到达应用程序的后端服务。

* **定制污点**：您在多个节点上使用了定制污点，以便只有具有该污点容忍度的应用程序 pod 可以部署到这些节点。您向应用程序和 LoadBalancer 或 Ingress 服务的部署添加了亲缘关系规则和容忍度，以便其 pod 仅部署到这些节点。但是，在 `ibm-system` 名称空间中自动创建的 `ibm-cloud-provider-ip` `keepalived` pod 会确保负载均衡器 pod 跟随应用程序 pod 部署到相同节点。这些 `keepalived` pod 没有您使用的定制污点的容忍度。因此，这些 pod 无法安排在运行应用程序 pod 的有污点节点上，所以没有任何流量到达应用程序的后端服务。

{: tsResolve}
通过选择下列其中一个选项来解决此问题：

* **边缘节点污点**：要确保负载均衡器和应用程序 pod 部署到有污点的边缘节点，请[向应用程序部署添加边缘节点亲缘关系规则和容忍度](cs_loadbalancer.html#edge_nodes)。缺省情况下，负载均衡器和 Ingress ALB pod 具有这些亲缘关系规则和容忍度。

* **定制污点**：除去 `keepalived` pod 没有其容忍度的定制污点。可以改为[将工作程序节点标记为边缘节点，然后污染这些边缘节点](cs_edge.html)。

如果完成了上述其中一个选项，但仍然无法安排 `keepalived` pod，那么可以获取有关 `keepalived` pod 的更多信息：

1. 获取 `keepalived` pod。
    ```
    kubectl get pods -n ibm-system
    ```
    {: pre}

2. 在输出中，查找 **Status** 为 `Pending` 的 `ibm-cloud-provider-ip` pod。示例：
    ```
    ibm-cloud-provider-ip-169-61-XX-XX-55967b5b8c-7zv9t     0/1       Pending   0          2m        <none>          <none>
    ibm-cloud-provider-ip-169-61-XX-XX-55967b5b8c-8ptvg     0/1       Pending   0          2m        <none>          <none>
    ```
    {:screen}

3. 描述每个 `keepalived` pod，并查找 **Events** 部分。解决列出的任何错误或警告消息。
    ```
    ibm-cloud-provider-ip-169-61-XX-XX-55967b5b8c-7zv9t -n ibm-system
    ```
    {: pre}

<br />


## 无法与 strongSwan Helm 图表建立 VPN 连接
{: #cs_vpn_fails}

{: tsSymptoms}
通过运行 `kubectl exec -n kube-system  $STRONGSWAN_POD -- ipsec status` 来检查 VPN 连接时，未看到阶段状态 `ESTABLISHED`，或者 VPN pod 处于 `ERROR` 状态或继续崩溃并重新启动。

{: tsCauses}
Helm 图表配置文件具有不正确的值、缺少值或有语法错误。

{: tsResolve}
尝试使用 strongSwan Helm 图表建立 VPN 连接时，很有可能 VPN 阶段状态一开始不是 `ESTABLISHED`。您可能需要检查多种类型的问题，并相应地更改配置文件。要对 strongSwan VPN 连接进行故障诊断，请执行以下操作：

1. 针对配置文件中的设置检查内部部署 VPN 端点设置。如果设置不匹配，请执行以下操作：

    <ol>
    <li>删除现有的 Helm 图表。</br><pre class="codeblock"><code>helm delete --purge <release_name></code></pre></li>
    <li>修正 <code>config.yaml</code> 文件中不正确的值，并保存更新的文件。</li>
    <li>安装新的 Helm 图表。</br><pre class="codeblock"><code>helm install -f config.yaml --namespace=kube-system --name=<release_name> bluemix/strongswan</code></pre></li>
    </ol>

2. 如果 VPN pod 处于 `ERROR` 状态或继续崩溃并重新启动，那么可能是因为在图表的配置映射中对 `ipsec.conf` 设置的参数验证问题。

    <ol>
    <li>检查 strongSwan pod 日志中是否有任何验证错误。</br><pre class="codeblock"><code>kubectl logs -n kube-system $STRONGSWAN_POD</code></pre></li>
    <li>如果日志包含验证错误，请删除现有 Helm 图表。</br><pre class="codeblock"><code>helm delete --purge <release_name></code></pre></li>
    <li>修正 `config.yaml` 文件中不正确的值，并保存更新的文件。</li>
    <li>安装新的 Helm 图表。</br><pre class="codeblock"><code>helm install -f config.yaml --namespace=kube-system --name=<release_name> bluemix/strongswan</code></pre></li>
    </ol>

3. 运行 strongSwan 图表定义中包含的 5 个 Helm 测试。

    <ol>
    <li>运行 Helm 测试。</br><pre class="codeblock"><code>helm test vpn</code></pre></li>
    <li>如果任何测试失败，请参阅[了解 Helm VPN 连接测试](cs_vpn.html#vpn_tests_table)，以获取有关每个测试的信息以及测试可能失败的原因。<b>注</b>：某些测试有一些要求，而这些要求在 VPN 配置中是可选设置。如果某些测试失败，失败可能是可接受的，具体取决于您是否指定了这些可选设置。</li>
    <li>通过查看测试 pod 的日志来查看失败测试的输出。<br><pre class="codeblock"><code>kubectl logs -n kube-system <test_program></code></pre></li>
    <li>删除现有的 Helm 图表。</br><pre class="codeblock"><code>helm delete --purge <release_name></code></pre></li>
    <li>修正 <code>config.yaml</code> 文件中不正确的值，并保存更新的文件。</li>
    <li>安装新的 Helm 图表。</br><pre class="codeblock"><code>helm install -f config.yaml --namespace=kube-system --name=<release_name> bluemix/strongswan</code></pre></li>
    <li>要检查更改，请执行以下操作：<ol><li>获取当前测试 pod。</br><pre class="codeblock"><code>kubectl get pods -a -n kube-system -l app=strongswan-test</code></pre></li><li>清除当前测试 pod。</br><pre class="codeblock"><code>kubectl delete pods -n kube-system -l app=strongswan-test</code></pre></li><li>重新运行测试。</br><pre class="codeblock"><code>helm test vpn</code></pre></li>
    </ol></ol>

4. 运行在 VPN pod 映像内打包的 VPN 调试工具。

    1. 设置 `STRONGSWAN_POD` 环境变量。

        ```
        export STRONGSWAN_POD=$(kubectl get pod -n kube-system -l app=strongswan,release=vpn -o jsonpath='{ .items[0].metadata.name }')
        ```
        {: pre}

    2. 运行调试工具。

        ```
        kubectl exec -n kube-system  $STRONGSWAN_POD -- vpnDebug
        ```
        {: pre}

        该工具在对常见联网问题运行各种测试时，会输出多页信息。以 `ERROR`、`WARNING`、`VERIFY` 或 `CHECK` 开头的输出行指示 VPN 连接可能存在错误。

    <br />


## 无法安装新的 strongSwan Helm 图表发行版
{: #cs_strongswan_release}

{: tsSymptoms}
您修改了 strongSwan Helm 图表，并尝试通过运行 `helm install -f config.yaml --namespace=kube-system --name=<new_release_name> bluemix/strongswan` 来安装新的发行版。但是，您看到以下错误：
```
错误：发布 <new_release_name> 失败：deployments.extensions "vpn-strongswan" 已存在
```
{: screen}

{: tsCauses}
此错误指示未完全卸载 strongSwan 图表的前发行版。

{: tsResolve}

1. 删除图表的前发行版。
    ```
    helm delete --purge <old_release_name>
    ```
    {: pre}

2. 删除先前发布版本的部署。删除部署和关联的 pod 最长需要 1 分钟。
    ```
    kubectl delete deploy -n kube-system vpn-strongswan
    ```
    {: pre}

3. 验证部署是否已删除。列表中未显示部署 `vpn-strongswan` 时说明该部署已删除。
    ```
    kubectl get deployments -n kube-system
    ```
    {: pre}

4. 使用新的发行版名称重新安装更新后的 strongSwan Helm 图表。
    ```
    helm install -f config.yaml --namespace=kube-system --name=<new_release_name> bluemix/strongswan
    ```
    {: pre}

<br />


## 在添加或删除工作程序节点后，strongSwan VPN 连接失败
{: #cs_vpn_fails_worker_add}

{: tsSymptoms}
先前已使用 strongSwan IPSec VPN 服务建立了有效的 VPN 连接。但是，在集群上添加或删除工作程序节点后，遇到了下列一种或多种症状：

* VPN 的阶段状态不为 `ESTABLISHED`
* 无法从内部部署网络访问新工作程序节点
* 无法从新工作程序节点上运行的 pod 访问远程网络

{: tsCauses}
如果已将工作程序节点添加到工作程序池：

* 工作程序节点在新的专用子网上供应，该子网未由现有 `localSubnetNAT` 或 `local.subnet` 设置通过 VPN 连接公开
* 无法将 VPN 路径添加到工作程序节点，因为工作程序具有未包含在现有 `tolerations` 或 `nodeSelector` 设置中的污点或标签
* VPN pod 在新的工作程序节点上运行，但该工作程序节点的公共 IP 地址不允许通过内部部署防火墙访问

如果删除了工作程序节点：

* 由于对现有 `tolerations` 或 `nodeSelector` 设置中的特定污点或标签存在限制，因此该工作程序节点是唯一在运行 VPN pod 的节点

{: tsResolve}
更新 Helm 图表值以反映工作程序节点更改：

1. 删除现有的 Helm 图表。

    ```
  helm delete --purge <release_name>
  ```
    {: pre}

2. 打开 strongSwan VPN 服务的配置文件。

    ```
    helm inspect values ibm/strongswan > config.yaml
    ```
    {: pre}

3. 检查以下设置并根据需要进行更改，以反映出已删除或已添加的工作程序节点。

    如果添加了工作程序节点：

    <table>
    <caption>工作程序节点设置</caption?
     <thead>
     <th>设置</th>
     <th>描述</th>
     </thead>
     <tbody>
     <tr>
     <td><code>localSubnetNAT</code></td>
     <td>添加的工作程序可能部署在新的专用子网上，该子网不同于其他工作程序节点所在的其他现有子网。如果是使用子网 NAT 来重新映射集群的专用本地 IP 地址，并且在新子网上添加了工作程序，请将新的子网 CIDR 添加到此设置。</td>
     </tr>
     <tr>
     <td><code>nodeSelector</code></td>
     <td>如果先前将 VPN pod 部署仅限于具有特定标签的工作程序，请确保添加的工作程序节点也具有该标签。</td>
     </tr>
     <tr>
     <td><code>tolerations</code></td>
     <td>如果添加的工作程序节点已有污点，请更改此设置以允许 VPN pod 在具有任何污点或特定污点的所有有污点的工作程序上运行。</td>
     </tr>
     <tr>
     <td><code>local.subnet</code></td>
     <td>添加的工作程序可能部署在新的专用子网上，该子网不同于其他工作程序所在的现有子网。如果应用程序是由专用网络上的 NodePort 或 LoadBalancer 服务公开的，并且应用程序位于添加的工作程序上，请将新的子网 CIDR 添加到此设置。**注**：如果将值添加到 `local.subnet`，请检查内部部署子网的 VPN 设置，以确定是否还必须更新这些设置。</td>
     </tr>
     </tbody></table>

    如果删除了工作程序节点：

    <table>
    <caption>工作程序节点设置</caption>
     <thead>
     <th>设置</th>
     <th>描述</th>
     </thead>
     <tbody>
     <tr>
     <td><code>localSubnetNAT</code></td>
     <td>如果是使用子网 NAT 来重新映射特定专用本地 IP 地址，请从此设置中除去来自旧工作程序的任何 IP 地址。如果是使用子网 NAT 来重新映射整个子网，并且子网上没有任何工作程序存在，请从此设置中除去该子网 CIDR。</td>
     </tr>
     <tr>
     <td><code>nodeSelector</code></td>
     <td>如果先前将 VPN pod 部署仅限于单个工作程序，并且删除了该工作程序，请将此设置更改为允许 VPN pod 在其他工作程序上运行。</td>
     </tr>
     <tr>
     <td><code>tolerations</code></td>
     <td>如果删除的工作程序没有污点，而唯一保留的工作程序有污点，请将此设置更改为允许 VPN pod 在具有任何污点或特定污点的工作程序上运行。</td>
     </tr>
     </tbody></table>

4. 使用更新的值安装新 Helm 图表。

    ```
    helm install -f config.yaml --namespace=kube-system --name=<release_name> ibm/strongswan
    ```
    {: pre}

5. 检查图表部署状态。当图表就绪时，输出顶部附近的 **STATUS** 字段的值为 `DEPLOYED`。

    ```
    helm status <release_name>
    ```
    {: pre}

6. 在某些情况下，可能需要更改内部部署设置和防火墙设置，以匹配对 VPN 配置文件的更改。

7. 启动 VPN。
    * 如果 VPN 连接是由集群启动的（`ipsec.auto` 设置为 `start`），请先在内部部署网关上启动 VPN，然后在集群上启动 VPN。
    * 如果 VPN 连接是由内部部署网关启动的（`ipsec.auto` 设置为 `auto`），请先在集群上启动 VPN，然后在内部部署网关上启动 VPN。

8. 设置 `STRONGSWAN_POD` 环境变量。

    ```
    export STRONGSWAN_POD=$(kubectl get pod -n kube-system -l app=strongswan,release=<release_name> -o jsonpath='{ .items[0].metadata.name }')
    ```
    {: pre}

9. 检查 VPN 的状态。

    ```
        kubectl exec -n kube-system  $STRONGSWAN_POD -- ipsec status
        ```
    {: pre}

    * 如果 VPN 连接的阶段状态为 `ESTABLISHED`，说明 VPN 连接成功。无需进一步操作。

    * 如果仍存在连接问题，请参阅[无法建立与 stronSwan Helm 图表的 VPN 连接](#cs_vpn_fails)，以进一步对 VPN 连接进行故障诊断。

<br />



## 无法检索 Calico 网络策略
{: #cs_calico_fails}

{: tsSymptoms}
尝试通过运行 `calicoctl get policy` 来查看集群中的 Calico 网络策略时，会收到下列其中一个意外结果或错误消息：
- 空列表
- 旧 Calico V2 策略的列表，而不是 V3 策略的列表
- `Failed to create Calico API client: syntax error in calicoctl.cfg: invalid config file: unknown APIVersion 'projectcalico.org/v3'`（创建 Calico API 客户机失败：calicoctl.cfg 中存在语法错误：配置文件无效：未知的 APIVersion“projectcalico.org/v3”）

尝试通过运行 `calicoctl get GlobalNetworkPolicy` 来查看集群中的 Calico 网络策略时，会收到下列其中一个意外结果或错误消息：
- 空列表
- `Failed to create Calico API client: syntax error in calicoctl.cfg: invalid config file: unknown APIVersion 'v1'`（创建 Calico API 客户机失败：calicoctl.cfg 中存在语法错误：配置文件无效：未知的 APIVersion“v1”）
- `Failed to create Calico API client: syntax error in calicoctl.cfg: invalid config file: unknown APIVersion 'projectcalico.org/v3'`（创建 Calico API 客户机失败：calicoctl.cfg 中存在语法错误：配置文件无效：未知的 APIVersion“projectcalico.org/v3”）
- `Failed to get resources: Resource type 'GlobalNetworkPolicy' is not supported`（获取资源失败：不支持资源类型“GlobalNetworkPolicy”）

{: tsCauses}
要使用 Calico 策略，下面四个因素必须全部符合要求：集群 Kubernetes 版本、Calico CLI 版本、Calico 配置文件语法和查看策略命令。其中一个或多个因素的版本不正确。

{: tsResolve}
如果集群处于 [Kubernetes V1.10 或更高版本](cs_versions.html)，那么必须使用 Calico CLI V3.1、`calicoctl.cfg` V3 配置文件语法以及 `calicoctl get GlobalNetworkPolicy` 和 `calicoctl get NetworkPolicy` 命令。

如果集群处于 [Kubernetes V1.9 或更低版本](cs_versions.html)，那么必须使用 Calico CLI V1.6.3、`calicoctl.cfg` V2 配置文件语法以及 `calicoctl get policy` 命令。

要确保所有 Calico 因素都符合要求，请执行以下操作：

1. 查看集群 Kubernetes 版本。
    ```
    ibmcloud ks cluster-get <cluster_name>
    ```
    {: pre}

    * 如果集群处于 Kubernetes V1.10 或更高版本：
        1. [安装和配置 Calico CLI V3.1.1](cs_network_policy.html#1.10_install)。配置包括手动更新 `calicoctl.cfg` 文件以使用 Calico V3 语法。
        2. 确保您创建并要应用于集群的任何策略都使用 [Calico V3 语法 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://docs.projectcalico.org/v3.1/reference/calicoctl/resources/networkpolicy)。如果在 Calico V2 语法中具有现有策略 `.yaml` 或 `.json` 文件，那么可以使用 [`calicoctl convert` 命令 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://docs.projectcalico.org/v3.1/reference/calicoctl/commands/convert) 将其转换为 Calico V3 语法。
        3. 要[查看策略](cs_network_policy.html#1.10_examine_policies)，请确保对于全局策略，使用 `calicoctl get GlobalNetworkPolicy`，对于作用域限定为特定名称空间的策略，使用 `calicoctl get NetworkPolicy --namespace<policy_namespace>`。

    * 如果集群处于 Kubernetes V1.9 或更低版本：
        1. [安装和配置 Calico CLI V1.6.3](cs_network_policy.html#1.9_install)。确保 `calicoctl.cfg` 文件使用 Calico V2 语法。
        2. 确保您创建并要应用于集群的任何策略都使用 [Calico V2 语法 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://docs.projectcalico.org/v2.6/reference/calicoctl/resources/policy)。
        3. 要[查看策略](cs_network_policy.html#1.9_examine_policies)，请确保使用的是 `calicoctl get policy`。

在将集群从 Kubernetes V1.9 或更低版本更新到 V1.10 或更高版本之前，请查看[准备更新到 Calico V3](cs_versions.html#110_calicov3)。
{: tip}

<br />


## 由于 VLAN 标识无效而无法添加工作程序节点
{: #suspended}

{: tsSymptoms}
您的 {{site.data.keyword.Bluemix_notm}} 帐户已暂挂，或者集群中的所有工作程序节点都已删除。重新激活帐户后，在尝试调整工作程序池大小或重新均衡工作程序池时，无法添加工作程序节点。您会看到类似于以下内容的错误消息：

```
SoftLayerAPIError(SoftLayer_Exception_Public)：无法获取标识为 #123456 的网络 VLAN。
```
{: screen}

{: tsCauses}
帐户暂挂时，会删除帐户内的工作程序节点。如果集群没有工作程序节点，那么 IBM Cloud Infrastructure (SoftLayer) 会回收关联的公用和专用 VLAN。但是，集群工作程序池在其元数据中仍具有先前的 VLAN 标识，在您重新均衡池或调整池大小时，会使用这些不可用的标识。由于 VLAN 不再与集群相关联，因此创建节点失败。

{: tsResolve}

可以[删除现有工作程序池](cs_cli_reference.html#cs_worker_pool_rm)，然后[创建新的工作程序池](cs_cli_reference.html#cs_worker_pool_create)。

或者，可以通过订购新 VLAN 并使用这些 VLAN 在现有工作程序池中创建新的工作程序节点来保留现有工作程序池。

开始之前：[登录到您的帐户。将相应的区域和（如果适用）资源组设定为目标。设置集群的上下文](cs_cli_install.html#cs_cli_configure)。

1.  要获取需要其新 VLAN 标识的专区，请记录以下命令输出中的 **Location**。**注**：如果集群是多专区集群，那么需要每个专区的 VLAN 标识。

    ```
    ibmcloud ks clusters
    ```
    {: pre}

2.  通过[联系 {{site.data.keyword.Bluemix_notm}} 支持](/docs/infrastructure/vlans/order-vlan.html#order-vlans)，获取集群所在的每个专区的新专用和公用 VLAN。

3.  记下每个专区的新专用和公用 VLAN 标识。

4.  记下工作程序池的名称。

    ```
    ibmcloud ks worker-pools --cluster <cluster_name_or_ID>
    ```
    {: pre}

5.  使用 `zone-network-set` [命令](cs_cli_reference.html#cs_zone_network_set)更改工作程序池网络元数据。

    ```
    ibmcloud ks zone-network-set --zone <zone> --cluster <cluster_name_or_ID> -- worker-pools <worker-pool> --private-vlan <private_vlan_ID> --public-vlan <public_vlan_ID>
    ```
    {: pre}

6.  **仅限多专区集群**：针对集群中的每个专区重复**步骤 5**。

7.  重新均衡工作程序池或调整其大小，以添加使用新 VLAN 标识的工作程序节点。例如：

    ```
    ibmcloud ks worker-pool-resize --cluster <cluster_name_or_ID> --worker-pool <worker_pool> --size-per-zone <number_of_workers_per_zone>
    ```
    {: pre}

8.  验证工作程序节点是否已创建。

    ```
    ibmcloud ks workers <cluster_name_or_ID> --worker-pool <worker_pool>
    ```
    {: pre}

<br />



## 获取帮助和支持
{: #ts_getting_help}

集群仍然有问题吗？
{: shortdesc}

-  在终端中，在 `ibmcloud` CLI 和插件更新可用时，会通知您。请确保保持 CLI 为最新，从而可使用所有可用命令和标志。

-   要查看 {{site.data.keyword.Bluemix_notm}} 是否可用，请[检查 {{site.data.keyword.Bluemix_notm}} 状态页面 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/bluemix/support/#status)。
-   在 [{{site.data.keyword.containerlong_notm}} Slack ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://ibm-container-service.slack.com) 中发布问题。

如果未将 IBM 标识用于 {{site.data.keyword.Bluemix_notm}} 帐户，请针对此 Slack [请求邀请](https://bxcs-slack-invite.mybluemix.net/)。
    {: tip}
-   请复查论坛，以查看是否有其他用户遇到相同的问题。使用论坛进行提问时，请使用适当的标记来标注您的问题，以方便 {{site.data.keyword.Bluemix_notm}} 开发团队识别。

    -   如果您有关于使用 {{site.data.keyword.containerlong_notm}} 开发或部署集群或应用程序的技术问题，请在 [Stack Overflow ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://stackoverflow.com/questions/tagged/ibm-cloud+containers) 上发布您的问题，并使用 `ibm-cloud`、`kubernetes` 和 `containers` 标记您的问题。
    -   有关服务的问题和入门指示信息，请使用 [IBM Developer Answers ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://developer.ibm.com/answers/topics/containers/?smartspace=bluemix) 论坛。请加上 `ibm-cloud` 和 `containers` 标记。
    有关使用论坛的更多详细信息，请参阅[获取帮助](/docs/get-support/howtogetsupport.html#using-avatar)。

-   通过开具凭单，与 IBM 支持联系。要了解有关开具 IBM 支持凭单或有关支持级别和凭单严重性的信息，请参阅[联系支持人员](/docs/get-support/howtogetsupport.html#getting-customer-support)。

{: tip}
报告问题时，请包含集群标识。要获取集群标识，请运行 `ibmcloud ks clusters`。

