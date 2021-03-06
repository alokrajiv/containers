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



# バージョンの変更ログ
{: #changelog}

{{site.data.keyword.containerlong}} Kubernetes クラスターに利用可能なメジャー、マイナー、パッチの更新に関するバージョン変更の情報を表示します。 変更には、Kubernetes および {{site.data.keyword.Bluemix_notm}} Provider コンポーネントへの更新が含まれます。
{:shortdesc}

メジャー・バージョン、マイナー・バージョン、およびパッチ・バージョンについて、およびマイナー・バージョン間のマイグレーション・アクションについて詳しくは、[Kubernetes のバージョン](cs_versions.html)を参照してください。
{: tip}

前のバージョンからの変更点については、以下の変更ログを参照してください。
-  バージョン 1.11 [変更ログ](#111_changelog)。
-  バージョン 1.10 [変更ログ](#110_changelog)。
-  バージョン 1.9 [変更ログ](#19_changelog)。
-  非推奨または非サポートのバージョンの変更ログの[アーカイブ](#changelog_archive)。

**注**: 一部の変更ログは_ワーカー・ノード・フィックスパック_ に関するものであり、ワーカー・ノードにのみ適用されます。[これらのパッチを適用して](cs_cli_reference.html#cs_worker_update)、ワーカー・ノードのセキュリティー・コンプライアンスを確実にする必要があります。他の変更ログは_マスター・フィックスパック_ に関するものであり、クラスター・マスターにのみ適用されます。マスターのフィックスパックは自動的に適用されない場合があります。[これらを手動で適用](cs_cli_reference.html#cs_cluster_update)することもできます。パッチのタイプについて詳しくは、[更新タイプ](cs_versions.html#update_types)を参照してください。

</br>

## バージョン 1.11 変更ログ
{: #111_changelog}

以下の変更点を確認します。


### マスター・フィックスパック 1.11.3_1527 (2018 年 10 月 15 日リリース) の変更ログ
{: #1113_1527}

<table summary="バージョン 1.11.3_1524 からの変更点">
<caption>バージョン 1.11.3_1524 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Calico 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ノードの障害を処理しやすくするために `calico-node` コンテナーの Readiness Probe が修正されました。</td>
</tr>
<tr>
<td>クラスター更新</td>
<td>該当なし</td>
<td>該当なし</td>
<td>サポートされないバージョンからのマスター更新時に、クラスター・アドオンの更新で発生していた問題が修正されました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.11.3_1525 (2018 年 10 月 10 日リリース) の変更ログ
{: #1113_1525}

<table summary="バージョン 1.11.3_1524 からの変更点">
<caption>バージョン 1.11.3_1524 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル</td>
<td>4.4.0-133</td>
<td>4.4.0-137</td>
<td>[CVE-2018-14633、CVE-2018-17182 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://changelogs.ubuntu.com/changelogs/pool/main/l/linux/linux_4.4.0-137.163/changelog) に対して、カーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
<tr>
<td>非アクティブ・セッションのタイムアウト</td>
<td>該当なし</td>
<td>該当なし</td>
<td>コンプライアンスの理由により非アクティブ・セッションのタイムアウトが 5 分に設定されました。</td>
</tr>
</tbody>
</table>


### 1.11.3_1524 (2018 年 10 月 2 日リリース) の変更ログ
{: #1113_1524}

<table summary="バージョン 1.11.3_1521 からの変更点">
<caption>バージョン 1.11.3_1521 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>containerd</td>
<td>1.1.3</td>
<td>1.1.4</td>
<td>[containerd リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/containerd/containerd/releases/tag/v1.1.4) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.11.3-91</td>
<td>v1.11.3-100</td>
<td>ロード・バランサーのエラー・メッセージ内の文書リンクが更新されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・クラス</td>
<td>該当なし</td>
<td>該当なし</td>
<td>IBM ファイル・ストレージ・クラスの重複 `reclaimPolicy` パラメーターが削除されました。<br><br>
また、クラスター・マスターの更新時に、デフォルトの IBM ファイル・ストレージ・クラスが変更されなくなりました。デフォルトのストレージ・クラスを変更する場合は、`kubectl patch storageclass <storageclass> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'` を実行して、`<storageclass>` をストレージ・クラスの名前に置き換えます。</td>
</tr>
</tbody>
</table>

### 1.11.3_1521 (2018 年 9 月 20 日リリース) の変更ログ
{: #1113_1521}

<table summary="バージョン 1.11.2_1516 からの変更点">
<caption>バージョン 1.11.2_1516 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.11.2-71</td>
<td>v1.11.3-91</td>
<td>Kubernetes 1.11.3 リリースをサポートするように更新されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・クラス</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードで提供されるデフォルトを使用するように、IBM ファイル・ストレージ・クラスの `mountOptions` が削除されました。<br><br>
また、クラスター・マスターの更新時に、デフォルトの IBM ファイル・ストレージ・クラスが `ibmc-file-bronze` のままになりました。デフォルトのストレージ・クラスを変更する場合は、`kubectl patch storageclass <storageclass> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'` を実行して、`<storageclass>` をストレージ・クラスの名前に置き換えます。</td>
</tr>
<tr>
<td>鍵管理サービス・プロバイダー</td>
<td>該当なし</td>
<td>該当なし</td>
<td>{{site.data.keyword.keymanagementservicefull}} をサポートするため、クラスターで Kubernetes 鍵管理サービス (KMS) プロバイダーを使用する機能が追加されました。[クラスターで {{site.data.keyword.keymanagementserviceshort}} を有効にする](cs_encrypt.html#keyprotect)と、すべての Kubernetes シークレットが暗号化されます。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.11.2</td>
<td>v1.11.3</td>
<td>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.11.3) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes DNS autoscaler</td>
<td>1.1.2-r2</td>
<td>1.2.0</td>
<td>[Kubernetes DNS autoscaler リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes-incubator/cluster-proportional-autoscaler/releases/tag/1.2.0) を参照してください。</td>
</tr>
<tr>
<td>ログの循環</td>
<td>該当なし</td>
<td>該当なし</td>
<td>90 日内に再ロードまたは更新されないワーカー・ノードで `logrotate` が失敗することを防止するため、`cronjobs` の代わりに `systemd` タイマーを使用するように切り替えられました。**注**: このマイナー・リリースの前のすべてのバージョンでは、ログが循環されないため、cron ジョブが失敗した後に 1 次ディスクが満杯になります。ワーカー・ノードが更新も再ロードもされずに 90 日間アクティブだった場合、cron ジョブは失敗します。ログで 1 次ディスクが満杯になった場合、ワーカー・ノードは失敗状態になります。ワーカー・ノードを修正するには、`ibmcloud ks worker-reload` [コマンド](cs_cli_reference.html#cs_worker_reload)または `ibmcloud ks worker-update` [コマンド](cs_cli_reference.html#cs_worker_update)を使用できます。</td>
</tr>
<tr>
<td>ルート・パスワードの有効期限</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードのルート・パスワードは、コンプライアンスの理由により 90 日後に有効期限が切れます。自動化ツールがワーカー・ノードに root としてログインする必要がある場合や、root として実行される cron ジョブを使用している場合は、ワーカー・ノードにログインして `chage -M -1 root` を実行することによって、パスワード有効期限を無効にすることができます。**注**: セキュリティー・コンプライアンスに関する要件で、root として実行したり、パスワード有効期限を削除したりすることを避ける必要がある場合は、有効期限を無効にしないでください。代わりに、少なくとも 90 日ごとにワーカー・ノードを[更新](cs_cli_reference.html#cs_worker_update)または[再ロード](cs_cli_reference.html#cs_worker_reload)してください。</td>
</tr>
<tr>
<td>ワーカー・ノード・ランタイム・コンポーネント (`kubelet`、`kube-proxy`、`containerd`)</td>
<td>該当なし</td>
<td>該当なし</td>
<td>1 次ディスクでのランタイム・コンポーネントの従属関係が削除されました。この機能強化によって、1 次ディスクが満杯になった場合にワーカー・ノードが失敗することが回避されます。</td>
</tr>
<tr>
<td>Systemd</td>
<td>該当なし</td>
<td>該当なし</td>
<td>一時的なマウント装置が無制限に増えることを防止するため、それらを定期的に削除します。このアクションは、[Kubernetes 問題 57345 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/issues/57345) に対処するためのものです。</td>
</tr>
</tbody>
</table>

### 1.11.2_1516 (2018 年 9 月 4 日リリース) の変更ログ
{: #1112_1516}

<table summary="バージョン 1.11.2_1514 からの変更点">
<caption>バージョン 1.11.2_1514 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Calico</td>
<td>v3.1.3</td>
<td>v3.2.1</td>
<td>[Calico リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://docs.projectcalico.org/v3.2/releases/#v321) を参照してください。</td>
</tr>
<tr>
<td>containerd</td>
<td>1.1.2</td>
<td>1.1.3</td>
<td>[`containerd` リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/containerd/containerd/releases/tag/v1.1.3) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.11.2-60</td>
<td>v1.11.2-71</td>
<td>`externalTrafficPolicy` が `local` に設定されて、ロード・バランサー・サービスの更新をより良好に処理できるようにクラウド・プロバイダー構成が変更されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>IBM 提供のファイル・ストレージ・クラスのマウント・オプションから、デフォルトの NFS バージョンが削除されました。 ホストのオペレーティング・システムによって、NFS バージョンが、IBM Cloud インフラストラクチャー (SoftLayer) の NFS サーバーとネゴシエーションされるようになりました。 特定の NFS バージョンを手動で設定する場合、またはホストのオペレーティング・システムによってネゴシエーションされた PV の NFS バージョンを変更する場合は、[デフォルトの NFS バージョンの変更](cs_storage_file.html#nfs_version_class)を参照してください。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.11.2_1514 (2018 年 8 月 23 日リリース) の変更ログ
{: #1112_1514}

<table summary="バージョン 1.11.2_1513 からの変更点">
<caption>バージョン 1.11.2_1513 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>`systemd`</td>
<td>229</td>
<td>230</td>
<td>`cgroup` リークを修正するように `systemd` が更新されました。</td>
</tr>
<tr>
<td>カーネル</td>
<td>4.4.0-127</td>
<td>4.4.0-133</td>
<td>[CVE-2018-3620、CVE-2018-3646 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://usn.ubuntu.com/3741-1/) に関連するカーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
</tbody>
</table>

### 1.11.2_1513 (2018 年 8 月 14 日リリース) の変更ログ
{: #1112_1513}

<table summary="バージョン 1.10.5_1518 からの変更点">
<caption>バージョン 1.10.5_1518 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>containerd</td>
<td>該当なし</td>
<td>1.1.2</td>
<td>`containerd` は、Kubernetes の新しいコンテナー・ランタイムとして Docker を置き換えるものです。 [`containerd` リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/containerd/containerd/releases/tag/v1.1.2) を参照してください。 実行する必要があるアクションについては、[コンテナー・ランタイムとしての `containerd` へのマイグレーション](cs_versions.html#containerd)を参照してください。</td>
</tr>
<tr>
<td>Docker</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`containerd` は、パフォーマンスを向上させるために、Kubernetes の新しいコンテナー・ランタイムとして Docker を置き換えるものです。 実行する必要があるアクションについては、[コンテナー・ランタイムとしての `containerd` へのマイグレーション](cs_versions.html#containerd)を参照してください。</td>
</tr>
<tr>
<td>etcd</td>
<td>v3.2.14</td>
<td>v3.2.18</td>
<td>[etcd リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/coreos/etcd/releases/v3.2.18) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.10.5-118</td>
<td>v1.11.2-60</td>
<td>Kubernetes 1.11 リリースをサポートするように更新されました。 さらに、ロード・バランサー・ポッドで、新しいポッドの優先度クラス `ibm-app-cluster-critical` が使用されるようになりました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン</td>
<td>334</td>
<td>338</td>
<td>`incubator` のバージョンが 1.8 に更新されました。 ファイル・ストレージは、選択した特定のゾーンにプロビジョンされます。 複数ゾーン・クラスターを使用しており、[地域とゾーンのラベルを追加](cs_storage_basics.html#multizone)する必要がある場合を除いて、既存の (静的) PV インスタンスのラベルを更新することはできません。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.10.5</td>
<td>v1.11.2</td>
<td>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.11.2) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>クラスターの Kubernetes API サーバーの OpenID Connect 構成が、{{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM) アクセス・グループをサポートするように更新されました。 クラスターの Kubernetes API サーバーの `--enable-admission-plugins` オプションに `Priority` が追加され、ポッドの優先度をサポートするようにクラスターが構成されるようになりました。 詳しくは、以下を参照してください。
<ul><li>[IAM アクセス・グループ](cs_users.html#rbac)</li>
<li>[ポッドの優先度の構成](cs_pod_priority.html#pod_priority)</li></ul></td>
</tr>
<tr>
<td>Kubernetes Heapster</td>
<td>v1.5.2</td>
<td>v.1.5.4</td>
<td>`heapster-nanny` コンテナーのリソース限度が増やされました。 [Kubernetes Heapster リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/heapster/releases/tag/v1.5.4) を参照してください。</td>
</tr>
<tr>
<td>ロギング構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>コンテナー・ログ・ディレクトリーが、以前の `/var/lib/docker/containers/` から `/var/log/pods/` になりました。</td>
</tr>
</tbody>
</table>

<br />


## バージョン 1.10 変更ログ
{: #110_changelog}

以下の変更点を確認します。

### マスター・フィックスパック 1.10.8_1527 (2018 年 10 月 15 日リリース) の変更ログ
{: #1108_1527}

<table summary="バージョン 1.10.8_1524 からの変更点">
<caption>バージョン 1.10.8_1524 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Calico 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ノードの障害を処理しやすくするために `calico-node` コンテナーの Readiness Probe が修正されました。</td>
</tr>
<tr>
<td>クラスター更新</td>
<td>該当なし</td>
<td>該当なし</td>
<td>サポートされないバージョンからのマスター更新時に、クラスター・アドオンの更新で発生していた問題が修正されました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.10.8_1525 (2018 年 10 月 10 日リリース) の変更ログ
{: #1108_1525}

<table summary="バージョン 1.10.8_1524 からの変更点">
<caption>バージョン 1.10.8_1524 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル</td>
<td>4.4.0-133</td>
<td>4.4.0-137</td>
<td>[CVE-2018-14633、CVE-2018-17182 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://changelogs.ubuntu.com/changelogs/pool/main/l/linux/linux_4.4.0-137.163/changelog) に対して、カーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
<tr>
<td>非アクティブ・セッションのタイムアウト</td>
<td>該当なし</td>
<td>該当なし</td>
<td>コンプライアンスの理由により非アクティブ・セッションのタイムアウトが 5 分に設定されました。</td>
</tr>
</tbody>
</table>


### 1.10.8_1524 (2018 年 10 月 2 日リリース) の変更ログ
{: #1108_1524}

<table summary="バージョン 1.10.7_1520 からの変更点">
<caption>バージョン 1.10.7_1520 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>鍵管理サービス・プロバイダー</td>
<td>該当なし</td>
<td>該当なし</td>
<td>{{site.data.keyword.keymanagementservicefull}} をサポートするため、クラスターで Kubernetes 鍵管理サービス (KMS) プロバイダーを使用する機能が追加されました。[クラスターで {{site.data.keyword.keymanagementserviceshort}} を有効にする](cs_encrypt.html#keyprotect)と、すべての Kubernetes シークレットが暗号化されます。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.10.7</td>
<td>v1.10.8</td>
<td>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.10.8) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes DNS autoscaler</td>
<td>1.1.2-r2</td>
<td>1.2.0</td>
<td>[Kubernetes DNS autoscaler リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes-incubator/cluster-proportional-autoscaler/releases/tag/1.2.0) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.10.7-146</td>
<td>v1.10.8-172</td>
<td>Kubernetes 1.10.8 リリースをサポートするように更新されました。 また、ロード・バランサーのエラー・メッセージ内の文書リンクが更新されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・クラス</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードで提供されるデフォルトを使用するように、IBM ファイル・ストレージ・クラスの `mountOptions` が削除されました。IBM ファイル・ストレージ・クラスの重複 `reclaimPolicy` パラメーターが削除されました。<br><br>
また、クラスター・マスターの更新時に、デフォルトの IBM ファイル・ストレージ・クラスが変更されなくなりました。デフォルトのストレージ・クラスを変更する場合は、`kubectl patch storageclass <storageclass> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'` を実行して、`<storageclass>` をストレージ・クラスの名前に置き換えます。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.10.7_1521 (2018 年 9 月 20 日リリース) の変更ログ
{: #1107_1521}

<table summary="バージョン 1.10.7_1520 からの変更点">
<caption>バージョン 1.10.7_1520 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>ログの循環</td>
<td>該当なし</td>
<td>該当なし</td>
<td>90 日内に再ロードまたは更新されないワーカー・ノードで `logrotate` が失敗することを防止するため、`cronjobs` の代わりに `systemd` タイマーを使用するように切り替えられました。**注**: このマイナー・リリースの前のすべてのバージョンでは、ログが循環されないため、cron ジョブが失敗した後に 1 次ディスクが満杯になります。ワーカー・ノードが更新も再ロードもされずに 90 日間アクティブだった場合、cron ジョブは失敗します。ログで 1 次ディスクが満杯になった場合、ワーカー・ノードは失敗状態になります。ワーカー・ノードを修正するには、`ibmcloud ks worker-reload` [コマンド](cs_cli_reference.html#cs_worker_reload)または `ibmcloud ks worker-update` [コマンド](cs_cli_reference.html#cs_worker_update)を使用できます。</td>
</tr>
<tr>
<td>ワーカー・ノード・ランタイム・コンポーネント (`kubelet`、`kube-proxy`、`docker`)</td>
<td>該当なし</td>
<td>該当なし</td>
<td>1 次ディスクでのランタイム・コンポーネントの従属関係が削除されました。この機能強化によって、1 次ディスクが満杯になった場合にワーカー・ノードが失敗することが回避されます。</td>
</tr>
<tr>
<td>ルート・パスワードの有効期限</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードのルート・パスワードは、コンプライアンスの理由により 90 日後に有効期限が切れます。自動化ツールがワーカー・ノードに root としてログインする必要がある場合や、root として実行される cron ジョブを使用している場合は、ワーカー・ノードにログインして `chage -M -1 root` を実行することによって、パスワード有効期限を無効にすることができます。**注**: セキュリティー・コンプライアンスに関する要件で、root として実行したり、パスワード有効期限を削除したりすることを避ける必要がある場合は、有効期限を無効にしないでください。代わりに、少なくとも 90 日ごとにワーカー・ノードを[更新](cs_cli_reference.html#cs_worker_update)または[再ロード](cs_cli_reference.html#cs_worker_reload)してください。</td>
</tr>
<tr>
<td>Systemd</td>
<td>該当なし</td>
<td>該当なし</td>
<td>一時的なマウント装置が無制限に増えることを防止するため、それらを定期的に削除します。このアクションは、[Kubernetes 問題 57345 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/issues/57345) に対処するためのものです。</td>
</tr>
<tr>
<td>Docker</td>
<td>該当なし</td>
<td>該当なし</td>
<td>デフォルトの Docker ブリッジが無効にされたため、`172.17.0.0/16` IP 範囲がプライベート経路で使用されるようになりました。ホストで `docker` コマンドを直接実行することによって、または Docker ソケットをマウントするポッドを使用することによって、ワーカー・ノードで Docker コンテナーを作成している場合は、以下のオプションから選択してください。<ul><li>コンテナー作成時に外部ネットワーク接続を確実にするため、`docker build . --network host` を実行します。</li>
<li>コンテナー作成時に使用するネットワークを明示的に作成するため、`docker network create` を実行して、このネットワークを使用します。</li></ul>
**注**: Docker ソケットまたは Docker に直接依存していますか? [コンテナー・ランタイムとして `docker` の代わりに `containerd` にマイグレーションして](cs_versions.html#containerd)、クラスターで Kubernetes バージョン 1.11 以降が実行されるように準備してください。</td>
</tr>
</tbody>
</table>

### 1.10.7_1520 (2018 年 9 月 4 日リリース) の変更ログ
{: #1107_1520}

<table summary="バージョン 1.10.5_1519 からの変更点">
<caption>バージョン 1.10.5_1519 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Calico</td>
<td>v3.1.3</td>
<td>v3.2.1</td>
<td>Calico [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://docs.projectcalico.org/v3.2/releases/#v321) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.10.5-118</td>
<td>v1.10.7-146</td>
<td>Kubernetes 1.10.7 リリースをサポートするように更新されました。 さらに、`externalTrafficPolicy` が `local` に設定されて、ロード・バランサー・サービスの更新をより良好に処理できるようにクラウド・プロバイダー構成が変更されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン</td>
<td>334</td>
<td>338</td>
<td>incubator のバージョンが 1.8 に更新されました。 ファイル・ストレージは、選択した特定のゾーンにプロビジョンされます。 複数ゾーン・クラスターを使用しており、地域とゾーンのラベルを追加する必要がある場合を除いて、既存の (静的) PV インスタンスのラベルを更新することはできません。<br><br> IBM 提供のファイル・ストレージ・クラスのマウント・オプションから、デフォルトの NFS バージョンが削除されました。 ホストのオペレーティング・システムによって、NFS バージョンが、IBM Cloud インフラストラクチャー (SoftLayer) の NFS サーバーとネゴシエーションされるようになりました。 特定の NFS バージョンを手動で設定する場合、またはホストのオペレーティング・システムによってネゴシエーションされた PV の NFS バージョンを変更する場合は、[デフォルトの NFS バージョンの変更](cs_storage_file.html#nfs_version_class)を参照してください。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.10.5</td>
<td>v1.10.7</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.10.7) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes Heapster 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`heapster-nanny` コンテナーのリソース限度が増やされました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.10.5_1519 (2018 年 8 月 23 日リリース) の変更ログ
{: #1105_1519}

<table summary="バージョン 1.10.5_1518 からの変更点">
<caption>バージョン 1.10.5_1518 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>`systemd`</td>
<td>229</td>
<td>230</td>
<td>`cgroup` リークを修正するように `systemd` が更新されました。</td>
</tr>
<tr>
<td>カーネル</td>
<td>4.4.0-127</td>
<td>4.4.0-133</td>
<td>[CVE-2018-3620、CVE-2018-3646 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://usn.ubuntu.com/3741-1/) に関連するカーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.10.5_1518 (2018 年 8 月 13 日リリース) の変更ログ
{: #1105_1518}

<table summary="バージョン 1.10.5_1517 からの変更点">
<caption>バージョン 1.10.5_1517 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Ubuntu パッケージ</td>
<td>該当なし</td>
<td>該当なし</td>
<td>インストール済み Ubuntu パッケージが更新されました。</td>
</tr>
</tbody>
</table>

### 1.10.5_1517 (2018 年 7 月 27 日リリース) の変更ログ
{: #1105_1517}

<table summary="バージョン 1.10.3_1514 からの変更点">
<caption>バージョン 1.10.3_1514 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Calico</td>
<td>v3.1.1</td>
<td>v3.1.3</td>
<td>Calico [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://docs.projectcalico.org/v3.1/releases/#v313) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.10.3-85</td>
<td>v1.10.5-118</td>
<td>Kubernetes 1.10.5 リリースをサポートするように更新されました。 また、LoadBalancer サービス `create failure` イベントで、ポータブル・サブネット・エラーが含まれるようになりました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン</td>
<td>320</td>
<td>334</td>
<td>永続ボリューム作成のタイムアウトが 15 分から 30 分に増やされました。 デフォルトの請求タイプが `hourly` に変更されました。 事前定義ストレージ・クラスにマウント・オプションが追加されました。 IBM Cloud インフラストラクチャー (SoftLayer) アカウントの NFS ファイル・ストレージ・インスタンスで、**「メモ」**フィールドが JSON 形式に変更され、PV がデプロイされる Kubernetes 名前空間が追加されました。 マルチゾーン・クラスターをサポートするため、永続ボリュームにゾーンと地域のラベルが追加されました。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.10.3</td>
<td>v1.10.5</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.10.5) を参照してください。</td>
</tr>
<tr>
<td>カーネル</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ハイパフォーマンス・ネットワーキング・ワークロードのために、ワーカー・ノード・ネットワーク設定がわずかに改善されました。</td>
</tr>
<tr>
<td>OpenVPN クライアント</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kube-system` 名前空間で実行される OpenVPN クライアント `vpn` デプロイメントは、Kubernetes `addon-manager` によって管理されるようになりました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.10.3_1514 (2018 年 7 月 3 日リリース) の変更ログ
{: #1103_1514}

<table summary="バージョン 1.10.3_1513 からの変更点">
<caption>バージョン 1.10.3_1513 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ハイパフォーマンス・ネットワーキング・ワークロードのために、`sysctl` が最適化されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.10.3_1513 (2018 年 6 月 21 日リリース) の変更ログ
{: #1103_1513}

<table summary="バージョン 1.10.3_1512 からの変更点">
<caption>バージョン 1.10.3_1512 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Docker</td>
<td>該当なし</td>
<td>該当なし</td>
<td>非暗号化マシン・タイプの場合、ワーカー・ノードを再ロードまたは更新するときに新しいファイル・システムを取得することによって、2 次ディスクがクリーンアップされるようになりました。</td>
</tr>
</tbody>
</table>

### 1.10.3_1512 (2018 年 6 月 12 日リリース) の変更ログ
{: #1103_1512}

<table summary="バージョン 1.10.1_1510 からの変更点">
<caption>バージョン 1.10.1_1510 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubernetes</td>
<td>v1.10.1</td>
<td>v1.10.3</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.10.3) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>クラスターの Kubernetes API サーバーの `--enable-admission-plugins` オプションに `PodSecurityPolicy` が追加され、ポッドのセキュリティー・ポリシーをサポートするようにクラスターが構成されるようになりました。 詳しくは、[ポッド・セキュリティー・ポリシーの構成](cs_psp.html) を参照してください。</td>
</tr>
<tr>
<td>Kubelet 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kubelet` HTTPS エンドポイントを認証する API ベアラー・トークンとサービス・アカウント・トークンをサポートするため、`--authentication-token-webhook` オプションが使用可能になりました。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.10.1-52</td>
<td>v1.10.3-85</td>
<td>Kubernetes 1.10.3 リリースをサポートするように更新されました。</td>
</tr>
<tr>
<td>OpenVPN クライアント</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kube-system` 名前空間で実行される OpenVPN クライアント `vpn` デプロイメントに `livenessProbe` が追加されました。</td>
</tr>
<tr>
<td>カーネル更新</td>
<td>4.4.0-116</td>
<td>4.4.0-127</td>
<td>[CVE-2018-3639 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3639) に対して、カーネル更新を使用して新規ワーカー・ノード・イメージが導入されました。</td>
</tr>
</tbody>
</table>



### ワーカー・ノード・フィックスパック 1.10.1_1510 (2018 年 5 月 18 日リリース) の変更ログ
{: #1101_1510}

<table summary="バージョン 1.10.1_1509 からの変更点">
<caption>バージョン 1.10.1_1509 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ブロック・ストレージ・プラグインを使用したときに発生するバグに対応するための修正です。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.10.1_1509 (2018 年 5 月 16 日リリース) の変更ログ
{: #1101_1509}

<table summary="バージョン 1.10.1_1508 からの変更点">
<caption>バージョン 1.10.1_1508 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kubelet` ルート・ディレクトリーに保管したデータが、ワーカー・ノード・マシンのより大きい 2 次ディスクに保存されるようになりました。</td>
</tr>
</tbody>
</table>

### 1.10.1_1508 (2018 年 5 月 1 日リリース) の変更ログ
{: #1101_1508}

<table summary="バージョン 1.9.7_1510 からの変更点">
<caption>バージョン 1.9.7_1510 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Calico</td>
<td>v2.6.5</td>
<td>v3.1.1</td>
<td>Calico [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://docs.projectcalico.org/v3.1/releases/#v311) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes Heapster</td>
<td>v1.5.0</td>
<td>v1.5.2</td>
<td>Kubernetes Heapster [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/heapster/releases/tag/v1.5.2) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.9.7</td>
<td>v1.10.1</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.10.1) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>クラスターの Kubernetes API サーバーの <code>--enable-admission-plugins</code> オプションに <code>StorageObjectInUseProtection</code> が追加されました。</td>
</tr>
<tr>
<td>Kubernetes DNS</td>
<td>1.14.8</td>
<td>1.14.10</td>
<td>Kubernetes DNS [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/dns/releases/tag/1.14.10) を参照してください。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.9.7-102</td>
<td>v1.10.1-52</td>
<td>Kubernetes 1.10 リリースをサポートするように更新されました。</td>
</tr>
<tr>
<td>GPU サポート</td>
<td>該当なし</td>
<td>該当なし</td>
<td>スケジューリングおよび実行で、[グラフィックス・プロセッシング・ユニット (GPU) コンテナー・ワークロード](cs_app.html#gpu_app)がサポートされるようになりました。 使用可能な GPU マシン・タイプのリストについては、[ワーカー・ノード用のハードウェア](cs_clusters_planning.html#shared_dedicated_node)を参照してください。 詳しくは、[GPU のスケジュール ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) に関する Kubernetes の資料を参照してください。</td>
</tr>
</tbody>
</table>

<br />


## バージョン 1.9 変更ログ
{: #19_changelog}

以下の変更点を確認します。

### マスター・フィックスパック 1.9.10_1530 (2018 年 10 月 15 日リリース) の変更ログ
{: #1910_1530}

<table summary="バージョン 1.9.10_1527 からの変更点">
<caption>バージョン 1.9.10_1527 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>クラスター更新</td>
<td>該当なし</td>
<td>該当なし</td>
<td>サポートされないバージョンからのマスター更新時に、クラスター・アドオンの更新で発生していた問題が修正されました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.9.10_1528 (2018 年 10 月 10 日リリース) の変更ログ
{: #1910_1528}

<table summary="バージョン 1.9.10_1527 からの変更点">
<caption>バージョン 1.9.10_1527 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル</td>
<td>4.4.0-133</td>
<td>4.4.0-137</td>
<td>[CVE-2018-14633、CVE-2018-17182 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://changelogs.ubuntu.com/changelogs/pool/main/l/linux/linux_4.4.0-137.163/changelog) に対して、カーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
<tr>
<td>非アクティブ・セッションのタイムアウト</td>
<td>該当なし</td>
<td>該当なし</td>
<td>コンプライアンスの理由により非アクティブ・セッションのタイムアウトが 5 分に設定されました。</td>
</tr>
</tbody>
</table>


### 1.9.10_1527 (2018 年 10 月 2 日リリース) の変更ログ
{: #1910_1527}

<table summary="バージョン 1.9.10_1523 からの変更点">
<caption>バージョン 1.9.10_1523 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.9.10-192</td>
<td>v1.9.10-219</td>
<td>ロード・バランサーのエラー・メッセージ内の文書リンクが更新されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・クラス</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードで提供されるデフォルトを使用するように、IBM ファイル・ストレージ・クラスの `mountOptions` が削除されました。IBM ファイル・ストレージ・クラスの重複 `reclaimPolicy` パラメーターが削除されました。<br><br>
また、クラスター・マスターの更新時に、デフォルトの IBM ファイル・ストレージ・クラスが変更されなくなりました。デフォルトのストレージ・クラスを変更する場合は、`kubectl patch storageclass <storageclass> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'` を実行して、`<storageclass>` をストレージ・クラスの名前に置き換えます。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.9.10_1524 (2018 年 9 月 20 日リリース) の変更ログ
{: #1910_1524}

<table summary="バージョン 1.9.10_1523 からの変更点">
<caption>バージョン 1.9.10_1523 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>ログの循環</td>
<td>該当なし</td>
<td>該当なし</td>
<td>90 日内に再ロードまたは更新されないワーカー・ノードで `logrotate` が失敗することを防止するため、`cronjobs` の代わりに `systemd` タイマーを使用するように切り替えられました。**注**: このマイナー・リリースの前のすべてのバージョンでは、ログが循環されないため、cron ジョブが失敗した後に 1 次ディスクが満杯になります。ワーカー・ノードが更新も再ロードもされずに 90 日間アクティブだった場合、cron ジョブは失敗します。ログで 1 次ディスクが満杯になった場合、ワーカー・ノードは失敗状態になります。ワーカー・ノードを修正するには、`ibmcloud ks worker-reload` [コマンド](cs_cli_reference.html#cs_worker_reload)または `ibmcloud ks worker-update` [コマンド](cs_cli_reference.html#cs_worker_update)を使用できます。</td>
</tr>
<tr>
<td>ワーカー・ノード・ランタイム・コンポーネント (`kubelet`、`kube-proxy`、`docker`)</td>
<td>該当なし</td>
<td>該当なし</td>
<td>1 次ディスクでのランタイム・コンポーネントの従属関係が削除されました。この機能強化によって、1 次ディスクが満杯になった場合にワーカー・ノードが失敗することが回避されます。</td>
</tr>
<tr>
<td>ルート・パスワードの有効期限</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードのルート・パスワードは、コンプライアンスの理由により 90 日後に有効期限が切れます。自動化ツールがワーカー・ノードに root としてログインする必要がある場合や、root として実行される cron ジョブを使用している場合は、ワーカー・ノードにログインして `chage -M -1 root` を実行することによって、パスワード有効期限を無効にすることができます。**注**: セキュリティー・コンプライアンスに関する要件で、root として実行したり、パスワード有効期限を削除したりすることを避ける必要がある場合は、有効期限を無効にしないでください。代わりに、少なくとも 90 日ごとにワーカー・ノードを[更新](cs_cli_reference.html#cs_worker_update)または[再ロード](cs_cli_reference.html#cs_worker_reload)してください。</td>
</tr>
<tr>
<td>Systemd</td>
<td>該当なし</td>
<td>該当なし</td>
<td>一時的なマウント装置が無制限に増えることを防止するため、それらを定期的に削除します。このアクションは、[Kubernetes 問題 57345 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/issues/57345) に対処するためのものです。</td>
</tr>
<tr>
<td>Docker</td>
<td>該当なし</td>
<td>該当なし</td>
<td>デフォルトの Docker ブリッジが無効にされたため、`172.17.0.0/16` IP 範囲がプライベート経路で使用されるようになりました。ホストで `docker` コマンドを直接実行することによって、または Docker ソケットをマウントするポッドを使用することによって、ワーカー・ノードで Docker コンテナーを作成している場合は、以下のオプションから選択してください。<ul><li>コンテナー作成時に外部ネットワーク接続を確実にするため、`docker build . --network host` を実行します。</li>
<li>コンテナー作成時に使用するネットワークを明示的に作成するため、`docker network create` を実行して、このネットワークを使用します。</li></ul>
**注**: Docker ソケットまたは Docker に直接依存していますか? [コンテナー・ランタイムとして `docker` の代わりに `containerd` にマイグレーションして](cs_versions.html#containerd)、クラスターで Kubernetes バージョン 1.11 以降が実行されるように準備してください。</td>
</tr>
</tbody>
</table>

### 1.9.10_1523 (2018 年 9 月 4 日リリース) の変更ログ
{: #1910_1523}

<table summary="バージョン 1.9.9_1522 からの変更点">
<caption>バージョン 1.9.9_1522 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.9.9-167</td>
<td>v1.9.10-192</td>
<td>Kubernetes 1.9.10 リリースをサポートするように更新されました。 さらに、`externalTrafficPolicy` が `local` に設定されて、ロード・バランサー・サービスの更新をより良好に処理できるようにクラウド・プロバイダー構成が変更されました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン</td>
<td>334</td>
<td>338</td>
<td>incubator のバージョンが 1.8 に更新されました。 ファイル・ストレージは、選択した特定のゾーンにプロビジョンされます。 複数ゾーン・クラスターを使用しており、地域とゾーンのラベルを追加する必要がある場合を除いて、既存の (静的) PV インスタンスのラベルを更新することはできません。<br><br>IBM 提供のファイル・ストレージ・クラスのマウント・オプションから、デフォルトの NFS バージョンが削除されました。 ホストのオペレーティング・システムによって、NFS バージョンが、IBM Cloud インフラストラクチャー (SoftLayer) の NFS サーバーとネゴシエーションされるようになりました。 特定の NFS バージョンを手動で設定する場合、またはホストのオペレーティング・システムによってネゴシエーションされた PV の NFS バージョンを変更する場合は、[デフォルトの NFS バージョンの変更](cs_storage_file.html#nfs_version_class)を参照してください。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.9.9</td>
<td>v1.9.10</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.9.10) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes Heapster 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`heapster-nanny` コンテナーのリソース限度が増やされました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.9.9_1522 (2018 年 8 月 23 日リリース) の変更ログ
{: #199_1522}

<table summary="バージョン 1.9.9_1521 からの変更点">
<caption>バージョン 1.9.9_1521 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>`systemd`</td>
<td>229</td>
<td>230</td>
<td>`cgroup` リークを修正するように `systemd` が更新されました。</td>
</tr>
<tr>
<td>カーネル</td>
<td>4.4.0-127</td>
<td>4.4.0-133</td>
<td>[CVE-2018-3620、CVE-2018-3646 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://usn.ubuntu.com/3741-1/) に関連するカーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.9.9_1521 (2018 年 8 月 13 日リリース) の変更ログ
{: #199_1521}

<table summary="バージョン 1.9.9_1520 からの変更点">
<caption>バージョン 1.9.9_1520 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Ubuntu パッケージ</td>
<td>該当なし</td>
<td>該当なし</td>
<td>インストール済み Ubuntu パッケージが更新されました。</td>
</tr>
</tbody>
</table>

### 1.9.9_1520 (2018 年 7 月 27 日リリース) の変更ログ
{: #199_1520}

<table summary="バージョン 1.9.8_1517 からの変更点">
<caption>バージョン 1.9.8_1517 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.9.8-141</td>
<td>v1.9.9-167</td>
<td>Kubernetes 1.9.9 リリースをサポートするように更新されました。 また、LoadBalancer サービス `create failure` イベントで、ポータブル・サブネット・エラーが含まれるようになりました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン</td>
<td>320</td>
<td>334</td>
<td>永続ボリューム作成のタイムアウトが 15 分から 30 分に増やされました。 デフォルトの請求タイプが `hourly` に変更されました。 事前定義ストレージ・クラスにマウント・オプションが追加されました。 IBM Cloud インフラストラクチャー (SoftLayer) アカウントの NFS ファイル・ストレージ・インスタンスで、**「メモ」**フィールドが JSON 形式に変更され、PV がデプロイされる Kubernetes 名前空間が追加されました。 マルチゾーン・クラスターをサポートするため、永続ボリュームにゾーンと地域のラベルが追加されました。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.9.8</td>
<td>v1.9.9</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.9.9) を参照してください。</td>
</tr>
<tr>
<td>カーネル</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ハイパフォーマンス・ネットワーキング・ワークロードのために、ワーカー・ノード・ネットワーク設定がわずかに改善されました。</td>
</tr>
<tr>
<td>OpenVPN クライアント</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kube-system` 名前空間で実行される OpenVPN クライアント `vpn` デプロイメントは、Kubernetes `addon-manager` によって管理されるようになりました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.9.8_1517 (2018 年 7 月 3 日リリース) の変更ログ
{: #198_1517}

<table summary="バージョン 1.9.8_1516 からの変更点">
<caption>バージョン 1.9.8_1516 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ハイパフォーマンス・ネットワーキング・ワークロードのために、`sysctl` が最適化されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.9.8_1516 (2018 年 6 月 21 日リリース) の変更ログ
{: #198_1516}

<table summary="バージョン 1.9.8_1515 からの変更点">
<caption>バージョン 1.9.8_1515 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Docker</td>
<td>該当なし</td>
<td>該当なし</td>
<td>非暗号化マシン・タイプの場合、ワーカー・ノードを再ロードまたは更新するときに新しいファイル・システムを取得することによって、2 次ディスクがクリーンアップされるようになりました。</td>
</tr>
</tbody>
</table>

### 1.9.8_1515 (2018 年 6 月 19 日リリース) の変更ログ
{: #198_1515}

<table summary="バージョン 1.9.7_1513 からの変更点">
<caption>バージョン 1.9.7_1513 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubernetes</td>
<td>v1.9.7</td>
<td>v1.9.8</td>
<td>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.9.8) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>クラスターの Kubernetes API サーバーの --admission-control オプションに PodSecurityPolicy が追加され、ポッドのセキュリティー・ポリシーをサポートするようにクラスターが構成されるようになりました。 詳しくは、[ポッド・セキュリティー・ポリシーの構成](cs_psp.html) を参照してください。</td>
</tr>
<tr>
<td>IBM Cloud Provider</td>
<td>v1.9.7-102</td>
<td>v1.9.8-141</td>
<td>Kubernetes 1.9.8 リリースをサポートするように更新されました。</td>
</tr>
<tr>
<td>OpenVPN クライアント</td>
<td>該当なし</td>
<td>該当なし</td>
<td><code>kube-system</code> 名前空間で実行される OpenVPN クライアント <code>vpn</code> デプロイメントに <code>livenessProbe</code> が追加されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.9.7_1513 (2018 年 6 月 11 日リリース) の変更ログ
{: #197_1513}

<table summary="バージョン 1.9.7_1512 からの変更点">
<caption>バージョン 1.9.7_1512 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル更新</td>
<td>4.4.0-116</td>
<td>4.4.0-127</td>
<td>[CVE-2018-3639 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3639) に対して、カーネル更新を使用して新規ワーカー・ノード・イメージが導入されました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.9.7_1512 (2018 年 5 月 18 日リリース) の変更ログ
{: #197_1512}

<table summary="バージョン 1.9.7_1511 からの変更点">
<caption>バージョン 1.9.7_1511 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ブロック・ストレージ・プラグインを使用したときに発生するバグに対応するための修正です。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.9.7_1511 (2018 年 5 月 16 日リリース) の変更ログ
{: #197_1511}

<table summary="バージョン 1.9.7_1510 からの変更点">
<caption>バージョン 1.9.7_1510 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kubelet` ルート・ディレクトリーに保管したデータが、ワーカー・ノード・マシンのより大きい 2 次ディスクに保存されるようになりました。</td>
</tr>
</tbody>
</table>

### 1.9.7_1510 (2018 年 4 月 30 日リリース) の変更ログ
{: #197_1510}

<table summary="バージョン 1.9.3_1506 からの変更点">
<caption>バージョン 1.9.3_1506 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubernetes</td>
<td>v1.9.3</td>
<td>v1.9.7	</td>
<td><p>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.9.7) を参照してください。 このリリースで、[CVE-2017-1002101 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-1002101) および [CVE-2017-1002102 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-1002102) の脆弱性に対応しています。</p><p><strong>注</strong>: `secret`、`configMap`、`downwardAPI`、および投影ボリュームは、読み取り専用でマウントされるようになりました。 これまでは、システムによって自動的に元の状態に戻されることがあるこれらのボリュームに、アプリがデータを書き込むことができました。 アプリが以前の非セキュアな動作に依存している場合は、適切に変更してください。</p></td>
</tr>
<tr>
<td>Kubernetes 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>クラスターの Kubernetes API サーバーの `--runtime-config` オプションに `admissionregistration.k8s.io/v1alpha1=true` が追加されました。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.9.3-71</td>
<td>v1.9.7-102</td>
<td>`NodePort` と `LoadBalancer` サービスは、`service.spec.externalTrafficPolicy` を `Local` に設定すると、[クライアント・ソース IP の保持](cs_loadbalancer.html#node_affinity_tolerations)をサポートするようになりました。</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>以前のクラスターの[エッジ・ノード](cs_edge.html#edge)耐障害性セットアップを修正します。</td>
</tr>
</tbody>
</table>

<br />


## アーカイブ
{: #changelog_archive}

サポートされない Kubernetes バージョン:
*  [バージョン 1.8](#18_changelog)
*  [バージョン 1.7](#17_changelog)

### バージョン 1.8 変更ログ (サポート対象外)
{: #18_changelog}

以下の変更点を確認します。

### ワーカー・ノード・フィックスパック 1.8.15_1521 (2018 年 9 月 20 日リリース) の変更ログ
{: #1815_1521}

<table summary="バージョン 1.8.15_1520 からの変更点">
<caption>バージョン 1.8.15_1520 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>ログの循環</td>
<td>該当なし</td>
<td>該当なし</td>
<td>90 日内に再ロードまたは更新されないワーカー・ノードで `logrotate` が失敗することを防止するため、`cronjobs` の代わりに `systemd` タイマーを使用するように切り替えられました。**注**: このマイナー・リリースの前のすべてのバージョンでは、ログが循環されないため、cron ジョブが失敗した後に 1 次ディスクが満杯になります。ワーカー・ノードが更新も再ロードもされずに 90 日間アクティブだった場合、cron ジョブは失敗します。ログで 1 次ディスクが満杯になった場合、ワーカー・ノードは失敗状態になります。ワーカー・ノードを修正するには、`ibmcloud ks worker-reload` [コマンド](cs_cli_reference.html#cs_worker_reload)または `ibmcloud ks worker-update` [コマンド](cs_cli_reference.html#cs_worker_update)を使用できます。</td>
</tr>
<tr>
<td>ワーカー・ノード・ランタイム・コンポーネント (`kubelet`、`kube-proxy`、`docker`)</td>
<td>該当なし</td>
<td>該当なし</td>
<td>1 次ディスクでのランタイム・コンポーネントの従属関係が削除されました。この機能強化によって、1 次ディスクが満杯になった場合にワーカー・ノードが失敗することが回避されます。</td>
</tr>
<tr>
<td>ルート・パスワードの有効期限</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ワーカー・ノードのルート・パスワードは、コンプライアンスの理由により 90 日後に有効期限が切れます。自動化ツールがワーカー・ノードに root としてログインする必要がある場合や、root として実行される cron ジョブを使用している場合は、ワーカー・ノードにログインして `chage -M -1 root` を実行することによって、パスワード有効期限を無効にすることができます。**注**: セキュリティー・コンプライアンスに関する要件で、root として実行したり、パスワード有効期限を削除したりすることを避ける必要がある場合は、有効期限を無効にしないでください。代わりに、少なくとも 90 日ごとにワーカー・ノードを[更新](cs_cli_reference.html#cs_worker_update)または[再ロード](cs_cli_reference.html#cs_worker_reload)してください。</td>
</tr>
<tr>
<td>Systemd</td>
<td>該当なし</td>
<td>該当なし</td>
<td>一時的なマウント装置が無制限に増えることを防止するため、それらを定期的に削除します。このアクションは、[Kubernetes 問題 57345 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/issues/57345) に対処するためのものです。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.8.15_1520 (2018 年 8 月 23 日リリース) の変更ログ
{: #1815_1520}

<table summary="バージョン 1.8.15_1519 からの変更点">
<caption>バージョン 1.8.15_1519 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>`systemd`</td>
<td>229</td>
<td>230</td>
<td>`cgroup` リークを修正するように `systemd` が更新されました。</td>
</tr>
<tr>
<td>カーネル</td>
<td>4.4.0-127</td>
<td>4.4.0-133</td>
<td>[CVE-2018-3620、CVE-2018-3646 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://usn.ubuntu.com/3741-1/) に関連するカーネル更新を使用してワーカー・ノード・イメージが更新されました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.8.15_1519 (2018 年 8 月 13 日リリース) の変更ログ
{: #1815_1519}

<table summary="バージョン 1.8.15_1518 からの変更点">
<caption>バージョン 1.8.15_1518 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Ubuntu パッケージ</td>
<td>該当なし</td>
<td>該当なし</td>
<td>インストール済み Ubuntu パッケージが更新されました。</td>
</tr>
</tbody>
</table>

### 1.8.15_1518 (2018 年 7 月 27 日リリース) の変更ログ
{: #1815_1518}

<table summary="バージョン 1.8.13_1516 からの変更点">
<caption>バージョン 1.8.13_1516 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.8.13-176</td>
<td>v1.8.15-204</td>
<td>Kubernetes 1.8.15 リリースをサポートするように更新されました。 また、LoadBalancer サービス `create failure` イベントで、ポータブル・サブネット・エラーが含まれるようになりました。</td>
</tr>
<tr>
<td>IBM ファイル・ストレージ・プラグイン</td>
<td>320</td>
<td>334</td>
<td>永続ボリューム作成のタイムアウトが 15 分から 30 分に増やされました。 デフォルトの請求タイプが `hourly` に変更されました。 事前定義ストレージ・クラスにマウント・オプションが追加されました。 IBM Cloud インフラストラクチャー (SoftLayer) アカウントの NFS ファイル・ストレージ・インスタンスで、**「メモ」**フィールドが JSON 形式に変更され、PV がデプロイされる Kubernetes 名前空間が追加されました。 マルチゾーン・クラスターをサポートするため、永続ボリュームにゾーンと地域のラベルが追加されました。</td>
</tr>
<tr>
<td>Kubernetes</td>
<td>v1.8.13</td>
<td>v1.8.15</td>
<td>Kubernetes [リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.8.15) を参照してください。</td>
</tr>
<tr>
<td>カーネル</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ハイパフォーマンス・ネットワーキング・ワークロードのために、ワーカー・ノード・ネットワーク設定がわずかに改善されました。</td>
</tr>
<tr>
<td>OpenVPN クライアント</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kube-system` 名前空間で実行される OpenVPN クライアント `vpn` デプロイメントは、Kubernetes `addon-manager` によって管理されるようになりました。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.8.13_1516 (2018 年 7 月 3 日リリース) の変更ログ
{: #1813_1516}

<table summary="バージョン 1.8.13_1515 からの変更点">
<caption>バージョン 1.8.13_1515 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ハイパフォーマンス・ネットワーキング・ワークロードのために、`sysctl` が最適化されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.8.13_1515 (2018 年 6 月 21 日リリース) の変更ログ
{: #1813_1515}

<table summary="バージョン 1.8.13_1514 からの変更点">
<caption>バージョン 1.8.13_1514 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Docker</td>
<td>該当なし</td>
<td>該当なし</td>
<td>非暗号化マシン・タイプの場合、ワーカー・ノードを再ロードまたは更新するときに新しいファイル・システムを取得することによって、2 次ディスクがクリーンアップされるようになりました。</td>
</tr>
</tbody>
</table>

### 1.8.13_1514 (2018 年 6 月 19 日リリース) の変更ログ
{: #1813_1514}

<table summary="バージョン 1.8.11_1512 からの変更点">
<caption>バージョン 1.8.11_1512 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubernetes</td>
<td>v1.8.11</td>
<td>v1.8.13</td>
<td>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.8.13) を参照してください。</td>
</tr>
<tr>
<td>Kubernetes 構成</td>
<td>該当なし</td>
<td>該当なし</td>
<td>クラスターの Kubernetes API サーバーの --admission-control オプションに PodSecurityPolicy が追加され、ポッドのセキュリティー・ポリシーをサポートするようにクラスターが構成されるようになりました。 詳しくは、[ポッド・セキュリティー・ポリシーの構成](cs_psp.html) を参照してください。</td>
</tr>
<tr>
<td>IBM Cloud Provider</td>
<td>v1.8.11-126</td>
<td>v1.8.13-176</td>
<td>Kubernetes 1.8.13 リリースをサポートするように更新されました。</td>
</tr>
<tr>
<td>OpenVPN クライアント</td>
<td>該当なし</td>
<td>該当なし</td>
<td><code>kube-system</code> 名前空間で実行される OpenVPN クライアント <code>vpn</code> デプロイメントに <code>livenessProbe</code> が追加されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.8.11_1512 (2018 年 6 月 11 日リリース) の変更ログ
{: #1811_1512}

<table summary="バージョン 1.8.11_1511 からの変更点">
<caption>バージョン 1.8.11_1511 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル更新</td>
<td>4.4.0-116</td>
<td>4.4.0-127</td>
<td>[CVE-2018-3639 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3639) に対して、カーネル更新を使用して新規ワーカー・ノード・イメージが導入されました。</td>
</tr>
</tbody>
</table>


### ワーカー・ノード・フィックスパック 1.8.11_1511 (2018 年 5 月 18 日リリース) の変更ログ
{: #1811_1511}

<table summary="バージョン 1.8.11_1510 からの変更点">
<caption>バージョン 1.8.11_1510 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ブロック・ストレージ・プラグインを使用したときに発生するバグに対応するための修正です。</td>
</tr>
</tbody>
</table>

### ワーカー・ノード・フィックスパック 1.8.11_1510 (2018 年 5 月 16 日リリース) の変更ログ
{: #1811_1510}

<table summary="バージョン 1.8.11_1509 からの変更点">
<caption>バージョン 1.8.11_1509 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kubelet` ルート・ディレクトリーに保管したデータが、ワーカー・ノード・マシンのより大きい 2 次ディスクに保存されるようになりました。</td>
</tr>
</tbody>
</table>


### 1.8.11_1509 (2018 年 4 月 19 日リリース) の変更ログ
{: #1811_1509}

<table summary="バージョン 1.8.8_1507 からの変更点">
<caption>バージョン 1.8.8_1507 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubernetes</td>
<td>v1.8.8</td>
<td>v1.8.11	</td>
<td><p>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.8.11) を参照してください。 このリリースで、[CVE-2017-1002101 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-1002101) および [CVE-2017-1002102 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-1002102) の脆弱性に対応しています。</p><p><strong>注</strong>: `secret`、`configMap`、`downwardAPI`、および投影ボリュームは、読み取り専用でマウントされるようになりました。 これまでは、システムによって自動的に元の状態に戻されることがあるこれらのボリュームに、アプリがデータを書き込むことができました。 アプリが以前の非セキュアな動作に依存している場合は、適切に変更してください。</p></td>
</tr>
<tr>
<td>一時停止コンテナーのイメージ</td>
<td>3.0</td>
<td>3.1</td>
<td>継承したオーファン・ゾンビ・プロセスを削除します。</td>
</tr>
<tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.8.8-86</td>
<td>v1.8.11-126</td>
<td>`NodePort` と `LoadBalancer` サービスは、`service.spec.externalTrafficPolicy` を `Local` に設定すると、[クライアント・ソース IP の保持](cs_loadbalancer.html#node_affinity_tolerations)をサポートするようになりました。</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>以前のクラスターの[エッジ・ノード](cs_edge.html#edge)耐障害性セットアップを修正します。</td>
</tr>
</tbody>
</table>

<br />


### バージョン 1.7 変更ログ (サポート対象外)
{: #17_changelog}

以下の変更点を確認します。

#### ワーカー・ノード・フィックスパック 1.7.16_1514 (2018 年 6 月 11 日リリース) の変更ログ
{: #1716_1514}

<table summary="バージョン 1.7.16_1513 からの変更点">
<caption>バージョン 1.7.16_1513 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>カーネル更新</td>
<td>4.4.0-116</td>
<td>4.4.0-127</td>
<td>[CVE-2018-3639 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3639) に対して、カーネル更新を使用して新規ワーカー・ノード・イメージが導入されました。</td>
</tr>
</tbody>
</table>

#### ワーカー・ノード・フィックスパック 1.7.16_1513 (2018 年 5 月 18 日リリース) の変更ログ
{: #1716_1513}

<table summary="バージョン 1.7.16_1512 からの変更点">
<caption>バージョン 1.7.16_1512 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>ブロック・ストレージ・プラグインを使用したときに発生するバグに対応するための修正です。</td>
</tr>
</tbody>
</table>

#### ワーカー・ノード・フィックスパック 1.7.16_1512 (2018 年 5 月 16 日リリース) の変更ログ
{: #1716_1512}

<table summary="バージョン 1.7.16_1511 からの変更点">
<caption>バージョン 1.7.16_1511 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubelet</td>
<td>該当なし</td>
<td>該当なし</td>
<td>`kubelet` ルート・ディレクトリーに保管したデータが、ワーカー・ノード・マシンのより大きい 2 次ディスクに保存されるようになりました。</td>
</tr>
</tbody>
</table>

#### 1.7.16_1511 (2018 年 4 月 19 日リリース) の変更ログ
{: #1716_1511}

<table summary="バージョン 1.7.4_1509 からの変更点">
<caption>バージョン 1.7.4_1509 からの変更点</caption>
<thead>
<tr>
<th>コンポーネント</th>
<th>以前</th>
<th>現在</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>Kubernetes</td>
<td>v1.7.4</td>
<td>v1.7.16	</td>
<td><p>[Kubernetes リリース・ノート ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/kubernetes/kubernetes/releases/tag/v1.7.16) を参照してください。 このリリースで、[CVE-2017-1002101 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-1002101) および [CVE-2017-1002102 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-1002102) の脆弱性に対応しています。</p><p><strong>注</strong>: `secret`、`configMap`、`downwardAPI`、および投影ボリュームは、読み取り専用でマウントされるようになりました。 これまでは、システムによって自動的に元の状態に戻されることがあるこれらのボリュームに、アプリがデータを書き込むことができました。 アプリが以前の非セキュアな動作に依存している場合は、適切に変更してください。</p></td>
</tr>
<td>{{site.data.keyword.Bluemix_notm}} Provider</td>
<td>v1.7.4-133</td>
<td>v1.7.16-17</td>
<td>`NodePort` と `LoadBalancer` サービスは、`service.spec.externalTrafficPolicy` を `Local` に設定すると、[クライアント・ソース IP の保持](cs_loadbalancer.html#node_affinity_tolerations)をサポートするようになりました。</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>以前のクラスターの[エッジ・ノード](cs_edge.html#edge)耐障害性セットアップを修正します。</td>
</tr>
</tbody>
</table>
