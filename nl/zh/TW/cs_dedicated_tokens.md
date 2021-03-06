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





# 建立 {{site.data.keyword.Bluemix_dedicated_notm}} 映像檔登錄的 {{site.data.keyword.registryshort_notm}} 記號
{: #cs_dedicated_tokens}

針對您用於單一及可擴充群組並與 {{site.data.keyword.containerlong}} 中的叢集搭配使用的映像檔登錄，建立不會過期的記號。
{:shortdesc}

1.  要求現行階段作業的永久性登錄記號。此記號會授與對現行名稱空間中映像檔的存取權。
    ```
    ibmcloud cr token-add --description "<description>" --non-expiring -q
    ```
    {: pre}

2.  驗證 Kubernetes 密碼。

    ```
    kubectl describe secrets
    ```
    {: pre}

    您可以利用此密碼來使用 {{site.data.keyword.containerlong}}。

3.  建立用來儲存記號資訊的 Kubernetes 密碼。

    ```
    kubectl --namespace <kubernetes_namespace> create secret docker-registry <secret_name>  --docker-server=<registry_url> --docker-username=token --docker-password=<token_value> --docker-email=<docker_email>
    ```
    {: pre}

    <table>
    <caption>瞭解這個指令的元件</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="構想圖示"/> 瞭解這個指令的元件</th>
    </thead>
    <tbody>
    <tr>
    <td><code>--namespace &lt;kubernetes_namespace&gt;</code></td>
    <td>必要。您要使用密碼並在其中部署容器之叢集的 Kubernetes 名稱空間。執行 <code>kubectl get namespaces</code>，以列出叢集裡的所有名稱空間。</td>
    </tr>
    <tr>
    <td><code>&lt;secret_name&gt;</code></td>
    <td>必要。您要用於 imagePullSecret 的名稱。</td>
    </tr>
    <tr>
    <td><code>--docker-server=&lt;registry_url&gt;</code></td>
    <td>必要。已設定名稱空間的映像檔登錄的 URL：<code>registry.&lt;dedicated_domain&gt;</code></li></ul></td>
    </tr>
    <tr>
    <td><code>--docker-username=token</code></td>
    <td>必要。請不要變更此值。</td>
    </tr>
    <tr>
    <td><code>--docker-password=&lt;token_value&gt;</code></td>
    <td>必要。先前所擷取的登錄記號值。</td>
    </tr>
    <tr>
    <td><code>--docker-email=&lt;docker-email&gt;</code></td>
    <td>必要。如果您有 Docker 電子郵件位址，請輸入它。否則，請輸入虛構的電子郵件位址，例如 a@b.c。此電子郵件是建立 Kubernetes 密碼的必要項目，但在建立之後就不再使用。</td>
    </tr>
    </tbody></table>

4.  建立會參照 imagePullSecret 的 Pod。

    1.  開啟偏好的文字編輯器，然後建立名為 mypod.yaml 的 Pod 配置 Script。
    2.  定義您要用來存取登錄的 Pod 及 imagePullSecret。若要使用名稱空間中的專用映像檔：

        ```
        apiVersion: v1
        kind: Pod
        metadata:
          name: <pod_name>
        spec:
          containers:
            - name: <container_name>
              image: registry.<dedicated_domain>/<my_namespace>/<my_image>:<tag>
          imagePullSecrets:
            - name: <secret_name>
        ```
        {: codeblock}

        <table>
        <caption>瞭解 YAML 檔案元件</caption>
        <thead>
        <th colspan=2><img src="images/idea.png" alt="構想圖示"/> 瞭解 YAML 檔案元件</th>
        </thead>
        <tbody>
        <tr>
        <td><code>&lt;pod_name&gt;</code></td>
        <td>您要建立的 Pod 的名稱。</td>
        </tr>
        <tr>
        <td><code>&lt;container_name&gt;</code></td>
        <td>您要部署至叢集的容器的名稱。</td>
        </tr>
        <tr>
        <td><code>&lt;my_namespace&gt;</code></td>
        <td>在其中儲存映像檔的名稱空間。若要列出可用的名稱空間，請執行 `ibmcloud cr namespace-list`。</td>
        </tr>
        <td><code>&lt;my_image&gt;</code></td>
        <td>您要使用的映像檔的名稱。若要列出 {{site.data.keyword.Bluemix_notm}} 帳戶中的可用映像檔，請執行 <code>ibmcloud cr image-list</code>。</td>
        </tr>
        <tr>
        <td><code>&lt;tag&gt;</code></td>
        <td>您要使用的映像檔的版本。如果未指定任何標籤，預設會使用標記為<strong>最新</strong>的映像檔。</td>
        </tr>
        <tr>
        <td><code>&lt;secret_name&gt;</code></td>
        <td>您先前建立的 imagePullSecret 的名稱。</td>
        </tr>
        </tbody></table>

    3.  儲存變更。

    4.  在叢集裡建立部署。

          ```
          kubectl apply -f mypod.yaml -n <namespace>
          ```
          {: pre}
