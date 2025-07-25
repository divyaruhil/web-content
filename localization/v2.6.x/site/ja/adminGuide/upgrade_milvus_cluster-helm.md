---
id: upgrade_milvus_cluster-helm.md
label: Helm
order: 1
group: upgrade_milvus_cluster-operator.md
related_key: upgrade Milvus Cluster
summary: MilvusクラスタをHelm Chartでアップグレードする方法をご紹介します。
title: HelmチャートによるMilvusクラスタのアップグレード
---
<div class="tab-wrapper"><a href="/docs/ja/v2.6.x/upgrade_milvus_cluster-operator.md" class=''>Milvus</a><a href="/docs/ja/v2.6.x/upgrade_milvus_cluster-helm.md" class='active '>OperatorHelm</a></div>
<h1 id="Upgrade-Milvus-Cluster-with-Helm-Chart" class="common-anchor-header">HelmチャートによるMilvusクラスタのアップグレード<button data-href="#Upgrade-Milvus-Cluster-with-Helm-Chart" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h1><p>このガイドでは、Milvus Helmチャートを使用してMilvusクラスタをアップグレードする方法について説明します。</p>
<div class="alert note">
<p>Milvus 2.5.x(またはそれ以前のバージョン)から2.6.0-rc1へのアップグレードには、アーキテクチャ上の重要な変更が含まれるため、このアップグレードは<strong>元に戻すことができません</strong>。新しいコンポーネント(WoodpeckerやStreaming Nodeなど)が導入され、特定のコンポーネントが削除されたため、<strong>アップグレード完了後に以前のバージョンにロールバックすることはできません</strong>。2.6.0-rc1で導入されたアーキテクチャ上の変更の詳細については、<a href="/docs/ja/v2.6.x/architecture_overview.md">Milvusアーキテクチャの概要を</a>参照してください。</p>
</div>
<h2 id="Prerequisites" class="common-anchor-header">前提条件<button data-href="#Prerequisites" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><ul>
<li>Helmバージョン &gt;= 3.14.0</li>
<li>Kubernetes バージョン &gt;= 1.20.0</li>
</ul>
<div class="alert note">
<p>Milvus-Helmチャートバージョン4.2.21以降、依存関係としてpulsar-v3.xチャートを導入しました。後方互換性のため、helmをv3.14またはそれ以降のバージョンにアップグレードし、<code translate="no">helm upgrade</code> を使用するときは必ず<code translate="no">--reset-then-reuse-values</code> オプションを追加してください。</p>
</div>
<h2 id="Check-Milvus-Helm-Chart" class="common-anchor-header">Milvus Helmチャートのチェック<button data-href="#Check-Milvus-Helm-Chart" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>以下のコマンドを実行して、Milvusの新しいバージョンをチェックしてください。</p>
<pre><code translate="no"><span class="hljs-meta prompt_">$ </span><span class="language-bash">helm repo update zilliztech</span>
<span class="hljs-meta prompt_">$ </span><span class="language-bash">helm search repo zilliztech/milvus --versions</span>
<button class="copy-code-btn"></button></code></pre>
<div class="alert note">
<p><code translate="no">https://milvus-io.github.io/milvus-helm/</code> にある Milvus Helm Charts のレポはアーカイブされ、次のように<code translate="no">https://zilliztech.github.io/milvus-helm/</code> から更なるアップデートを取得することができます：</p>
<pre><code translate="no" class="language-shell">helm repo add zilliztech https://zilliztech.github.io/milvus-helm
helm repo update
<span class="hljs-meta prompt_"># </span><span class="language-bash">upgrade existing helm release</span>
helm upgrade my-release zilliztech/milvus --reset-then-reuse-values
<button class="copy-code-btn"></button></code></pre>
<p>アーカイブされたレポは4.0.31までのチャートで利用可能です。それ以降のリリースについては、代わりに新しいレポを使用してください。</p>
</div>
<pre><code translate="no">NAME                    CHART VERSION   APP VERSION             DESCRIPTION                                       
zilliztech/milvus       4.1.34          2.4.5                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.33          2.4.4                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.32          2.4.3                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.31          2.4.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.30          2.4.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.29          2.4.0                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.24          2.3.11                  Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.23          2.3.10                  Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.22          2.3.10                  Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.21          2.3.10                  Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.20          2.3.10                  Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.18          2.3.10                  Milvus is an open-source vector database built ... 
zilliztech/milvus       4.1.18          2.3.9                   Milvus is an open-source vector database built ...                                       
zilliztech/milvus       4.1.17          2.3.8                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.16          2.3.7                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.15          2.3.5                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.14          2.3.6                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.13          2.3.5                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.12          2.3.5                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.11          2.3.4                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.10          2.3.3                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.9           2.3.3                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.8           2.3.2                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.7           2.3.2                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.6           2.3.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.5           2.3.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.4           2.3.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.3           2.3.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.2           2.3.1                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.1           2.3.0                   Milvus is an open-source vector database built ...
zilliztech/milvus       4.1.0           2.3.0                   Milvus is an open-source vector database built ...
<button class="copy-code-btn"></button></code></pre>
<p>Milvusのアップグレードパスは以下のように選択できます：</p>
<div style="display: none;">- Milvus v2.2.3以降をv2.6.0-rc1にアップグレードする。</div>
<ul>
<li><p>v2.2.3以前のマイナーリリースからv2.6.0-rc1へのアップグレードのために、<a href="#Upgrade-Milvus-using-Helm">Helmを使用してMilvusをアップグレードする</a>。</p></li>
<li><p>Milvus v2.1.xからv2.6.0-rc1へのアップグレード前に<a href="#Migrate-the-metadata">メタデータを移行する</a>。</p></li>
</ul>
<div style="display: none;">
<h2 id="Conduct-a-rolling-upgrade" class="common-anchor-header">ローリングアップグレードの実施<button data-href="#Conduct-a-rolling-upgrade" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Milvus 2.2.3以降では、Milvusコーディネータをアクティブスタンバイ モードで動作するように設定し、コーディネータのローリングアップグレード機能を有効にすることで、コーディネータのアップグレード中にMilvusが受信したリクエストに応答できるようになりました。以前のリリースでは、アップグレード中にコーディネータを削除してから作成するため、サービスのダウンタイムが発生する可能性がありました。</p>
<p>ローリングアップグレードでは、コーディネータをアクティブスタンバイで動作させる必要があります。弊社が提供する<a href="https://raw.githubusercontent.com/milvus-io/milvus/master/deployments/upgrade/rollingUpdate.sh">スクリプトを</a>使用して、コーディネーターをアクティブスタンバイ モードで動作するように設定し、ローリングアップグレードを開始できます。</p>
<p>Kubernetesが提供するローリングアップデートの機能に基づいて、上記のスクリプトは、デプロイメントの依存関係に従って順序付けられたアップデートを強制します。さらに、Milvusはアップグレード中もそのコンポーネントに依存しているコンポーネントとの互換性を維持するメカニズムを実装し、潜在的なサービスのダウンタイムを大幅に削減します。</p>
<p>このスクリプトは、HelmとともにインストールされたMilvusのアップグレードにのみ適用されます。次の表は、スクリプトで使用可能なコマンドフラグの一覧です。</p>
<table>
<thead>
<tr><th>パラメータ</th><th>説明</th><th>デフォルト値</th><th>必須</th></tr>
</thead>
<tbody>
<tr><td><code translate="no">i</code></td><td>Milvusインスタンス名</td><td><code translate="no">None</code></td><td>真</td></tr>
<tr><td><code translate="no">n</code></td><td>Milvusがインストールされている名前空間</td><td><code translate="no">default</code></td><td>偽</td></tr>
<tr><td><code translate="no">t</code></td><td>対象Milvusバージョン</td><td><code translate="no">None</code></td><td>真</td></tr>
<tr><td><code translate="no">w</code></td><td>新しいMilvusイメージタグ</td><td><code translate="no">milvusdb/milvus:v2.2.3</code></td><td>真</td></tr>
<tr><td><code translate="no">o</code></td><td>操作方法</td><td><code translate="no">update</code></td><td>偽</td></tr>
</tbody>
</table>
<p>Milvusインスタンスのすべてのデプロイメントが正常な状態であることを確認したら、以下のコマンドを実行してMilvusインスタンスを2.6.0-rc1にアップグレードします。以下のコマンドを実行することで、Milvusインスタンスを2.6.0-rc1にアップグレードすることができます。</p>
<pre><code translate="no" class="language-shell">sh rollingUpdate.sh -n default -i my-release -o update -t 2.6.0-rc1 -w &#x27;milvusdb/milvus:v2.6.0-rc1&#x27;
<button class="copy-code-btn"></button></code></pre>
<div class="alert note">
<ol>
<li>このスクリプトはデプロイのアップグレード順序をハードコードしており、変更することはできません。</li>
<li>このスクリプトでは、<code translate="no">kubectl patch</code> を使用してデプロイメントを更新し、<code translate="no">kubectl rollout status</code> を使用してステータスを監視します。</li>
<li>スクリプトは<code translate="no">kubectl patch</code> を使って、デプロイメントの<code translate="no">app.kubernetes.io/version</code> ラベルを、コマンドの<code translate="no">-t</code> フラグの後に指定したラベルに更新します。</li>
</ol>
</div>
</div>
<h2 id="Upgrade-Milvus-using-Helm" class="common-anchor-header">Helmを使用したMilvusのアップグレード<button data-href="#Upgrade-Milvus-using-Helm" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Milvusをv2.2.3以前のマイナーリリースから最新のものにアップグレードするには、以下のコマンドを実行します：</p>
<pre><code translate="no" class="language-shell">helm repo update zilliztech
helm upgrade my-release zilliztech/milvus --reset-then-reuse-values --version=4.2.53 # use the helm chart version here
<button class="copy-code-btn"></button></code></pre>
<p>直前のコマンドでHelm chartのバージョンを使用してください。Helmチャートバージョンの取得方法については、「<a href="#Check-the-Milvus-version">Milvusバージョンの確認</a>」をご参照ください。</p>
<h2 id="Migrate-the-metadata" class="common-anchor-header">メタデータの移行<button data-href="#Migrate-the-metadata" class="anchor-icon" translate="no">
      <svg translate="no"
        aria-hidden="true"
        focusable="false"
        height="20"
        version="1.1"
        viewBox="0 0 16 16"
        width="16"
      >
        <path
          fill="#0092E4"
          fill-rule="evenodd"
          d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"
        ></path>
      </svg>
    </button></h2><p>Milvus 2.2.0からメタデータに互換性がなくなりました。以下の例はMilvus 2.1.4からMilvus 2.2.0へのアップグレードを想定しています。</p>
<h3 id="1-Check-the-Milvus-version" class="common-anchor-header">1.Milvusバージョンの確認</h3><p><code translate="no">$ helm list</code> を実行し、Milvusアプリのバージョンを確認します。<code translate="no">APP VERSION</code> 、2.1.4であることがわかります。</p>
<pre><code translate="no">NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART           APP VERSION    
<span class="hljs-keyword">new</span><span class="hljs-operator">-</span><span class="hljs-keyword">release</span>         <span class="hljs-keyword">default</span>     <span class="hljs-number">1</span>           <span class="hljs-number">2022</span><span class="hljs-number">-11</span><span class="hljs-number">-21</span> <span class="hljs-number">15</span>:<span class="hljs-number">41</span>:<span class="hljs-number">25.51539</span> <span class="hljs-operator">+</span><span class="hljs-number">0800</span> CST     deployed    milvus<span class="hljs-number">-3.2</span><span class="hljs-number">.18</span>   <span class="hljs-number">2.1</span><span class="hljs-number">.4</span> 
<button class="copy-code-btn"></button></code></pre>
<h3 id="2-Check-the-running-pods" class="common-anchor-header">2.起動しているPodの確認</h3><p><code translate="no">$ kubectl get pods</code> を実行して実行中のポッドを確認する。以下のように出力されます。</p>
<pre><code translate="no">NAME                                             READY   STATUS      RESTARTS   AGE
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>etcd<span class="hljs-number">-0</span>                               <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>etcd<span class="hljs-number">-1</span>                               <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>etcd<span class="hljs-number">-2</span>                               <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>datacoord<span class="hljs-number">-664</span>c58798d<span class="hljs-operator">-</span>fl75s    <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>datanode<span class="hljs-number">-5</span>f75686c55<span class="hljs-operator">-</span>xfg2r     <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>indexcoord<span class="hljs-number">-5</span>f98b97589<span class="hljs-number">-2</span>l48r   <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>indexnode<span class="hljs-number">-857</span>b4ddf98<span class="hljs-operator">-</span>vmd75    <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>proxy<span class="hljs-number">-6</span>c548f787f<span class="hljs-operator">-</span>scspp        <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>querycoord<span class="hljs-operator">-</span>c454f44cd<span class="hljs-operator">-</span>dwmwq    <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>querynode<span class="hljs-number">-76</span>bb4946d<span class="hljs-operator">-</span>lbrz6     <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>milvus<span class="hljs-operator">-</span>rootcoord<span class="hljs-number">-7764</span>c5b686<span class="hljs-number">-62</span>msm    <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>minio<span class="hljs-number">-0</span>                              <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>minio<span class="hljs-number">-1</span>                              <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>minio<span class="hljs-number">-2</span>                              <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>minio<span class="hljs-number">-3</span>                              <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>bookie<span class="hljs-number">-0</span>                      <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>bookie<span class="hljs-number">-1</span>                      <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>bookie<span class="hljs-number">-2</span>                      <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>bookie<span class="hljs-operator">-</span>init<span class="hljs-operator">-</span>tjxpj             <span class="hljs-number">0</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     Completed   <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>broker<span class="hljs-number">-0</span>                      <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>proxy<span class="hljs-number">-0</span>                       <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>init<span class="hljs-operator">-</span>c8vvc             <span class="hljs-number">0</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     Completed   <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>recovery<span class="hljs-number">-0</span>                    <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>zookeeper<span class="hljs-number">-0</span>                   <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">21</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>zookeeper<span class="hljs-number">-1</span>                   <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">20</span>m
my<span class="hljs-operator">-</span><span class="hljs-keyword">release</span><span class="hljs-operator">-</span>pulsar<span class="hljs-operator">-</span>zookeeper<span class="hljs-number">-2</span>                   <span class="hljs-number">1</span><span class="hljs-operator">/</span><span class="hljs-number">1</span>     <span class="hljs-keyword">Running</span>     <span class="hljs-number">0</span>          <span class="hljs-number">20</span>m
<button class="copy-code-btn"></button></code></pre>
<h3 id="3-Check-the-image-tag" class="common-anchor-header">3.イメージタグの確認</h3><p>ポッド<code translate="no">my-release-milvus-proxy-6c548f787f-scspp</code> のイメージタグを確認します。Milvusクラスタのリリースがv2.1.4であることがわかります。</p>
<pre><code translate="no" class="language-shell"><span class="hljs-meta prompt_">$ </span><span class="language-bash">kubectl get pods my-release-milvus-proxy-6c548f787f-scspp -o=jsonpath=<span class="hljs-string">&#x27;{$.spec.containers[0].image}&#x27;</span></span>
<span class="hljs-meta prompt_"># </span><span class="language-bash">milvusdb/milvus:v2.1.4</span>
<button class="copy-code-btn"></button></code></pre>
<h3 id="4-Migrate-the-metadata" class="common-anchor-header">4.メタデータの移行</h3><p>Milvus2.2での大きな変更点はセグメントインデックスのメタデータ構造です。そのため、Milvusをv2.1.xからv2.2.0にアップグレードする際には、Helmを使用してメタデータを移行する必要があります。以下は、メタデータを安全に移行するための<a href="https://github.com/milvus-io/milvus/blob/master/deployments/migrate-meta/migrate.sh">スクリプトです</a>。</p>
<p>このスクリプトはK8sクラスタにインストールされたMilvusにのみ適用されます。処理中にエラーが発生した場合は、まずロールバック操作で以前のバージョンにロールバックしてください。</p>
<p>以下の表はメタマイグレーションで実行できる操作の一覧です。</p>
<table>
<thead>
<tr><th>パラメータ</th><th>説明</th><th>デフォルト値</th><th>必須</th></tr>
</thead>
<tbody>
<tr><td><code translate="no">i</code></td><td>Milvusインスタンス名。</td><td><code translate="no">None</code></td><td>真</td></tr>
<tr><td><code translate="no">n</code></td><td>Milvusがインストールされている名前空間。</td><td><code translate="no">default</code></td><td>False</td></tr>
<tr><td><code translate="no">s</code></td><td>Milvusのバージョン。</td><td><code translate="no">None</code></td><td>真</td></tr>
<tr><td><code translate="no">t</code></td><td>インストール先のMilvusのバージョン。</td><td><code translate="no">None</code></td><td>真</td></tr>
<tr><td><code translate="no">r</code></td><td>Milvusメタのルートパス。</td><td><code translate="no">by-dev</code></td><td>偽</td></tr>
<tr><td><code translate="no">w</code></td><td>Milvusの新しい画像タグ。</td><td><code translate="no">milvusdb/milvus:v2.2.0</code></td><td>False</td></tr>
<tr><td><code translate="no">m</code></td><td>metaマイグレーションイメージタグ。</td><td><code translate="no">milvusdb/meta-migration:v2.2.0</code></td><td>False</td></tr>
<tr><td><code translate="no">o</code></td><td>メタマイグレーション操作</td><td><code translate="no">migrate</code></td><td>誤</td></tr>
<tr><td><code translate="no">d</code></td><td>マイグレーション完了後にマイグレーションポッドを削除するかどうか。</td><td><code translate="no">false</code></td><td>False</td></tr>
<tr><td><code translate="no">c</code></td><td>メタ・マイグレーションpvcのストレージ・クラス。</td><td><code translate="no">default storage class</code></td><td>False</td></tr>
<tr><td><code translate="no">e</code></td><td>milvusが使用するetcdエンポイント。</td><td><code translate="no">etcd svc installed with milvus</code></td><td>False</td></tr>
</tbody>
</table>
<h4 id="1-Migrate-the-metadata" class="common-anchor-header">1.メタデータの移行</h4><ol>
<li><a href="https://github.com/milvus-io/milvus/blob/master/deployments/migrate-meta/migrate.sh">移行スクリプトを</a>ダウンロードします。</li>
<li>Milvusコンポーネントを停止します。Milvusのetcdにライブセッションがあるとマイグレーションに失敗する可能性があります。</li>
<li>Milvusメタデータのバックアップを作成します。</li>
<li>Milvusメタデータを移行します。</li>
<li>新しいイメージでMilvusコンポーネントを起動する。</li>
</ol>
<h4 id="2-Upgrade-Milvus-from-v21x-to-220" class="common-anchor-header">2.Milvusをv2.1.xから2.2.0にアップグレードする。</h4><p>以下のコマンドは、Milvusをv2.1.4から2.2.0にアップグレードすることを前提としています。必要なバージョンに変更してください。</p>
<ol>
<li><p>Milvusインスタンス名、ソースMilvusバージョン、ターゲットMilvusバージョンを指定します。</p>
<pre><code translate="no">./migrate.sh -i my-release -s 2.1.4 -t 2.2.0
<button class="copy-code-btn"></button></code></pre></li>
<li><p>MilvusがデフォルトのK8s名前空間にインストールされていない場合は、<code translate="no">-n</code> で名前空間を指定します。</p>
<pre><code translate="no">./migrate.sh -i my-release -n milvus -s 2.1.4 -t 2.2.0
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Milvusがカスタム<code translate="no">rootpath</code> でインストールされている場合は、<code translate="no">-r</code> でルートパスを指定してください。</p>
<pre><code translate="no">./migrate<span class="hljs-selector-class">.sh</span> -<span class="hljs-selector-tag">i</span> my-release -n milvus -s <span class="hljs-number">2.1</span>.<span class="hljs-number">4</span> -t <span class="hljs-number">2.2</span>.<span class="hljs-number">0</span> -<span class="hljs-attribute">r</span> by-dev
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Milvusがカスタム<code translate="no">image</code> でインストールされている場合は、<code translate="no">-w</code> でイメージタグを指定してください。</p>
<pre><code translate="no">./migrate.sh -i my-release -n milvus -s 2.1.4 -t 2.2.0 -r by-dev -w milvusdb/milvus:v2.2.0
<button class="copy-code-btn"></button></code></pre></li>
<li><p>マイグレーション完了後にマイグレーションポッドを自動的に削除する場合は、<code translate="no">-d true</code> を設定します。</p>
<pre><code translate="no">./migrate.sh -i my-release -n milvus -s 2.1.4 -t 2.2.0 -w milvusdb/milvus:v2.2.0 -d <span class="hljs-literal">true</span>
<button class="copy-code-btn"></button></code></pre></li>
<li><p>マイグレーションに失敗した場合は、ロールバックしてマイグレーションをやり直します。</p>
<pre><code translate="no">./migrate.sh -i my-release -n milvus -s 2.1.4 -t 2.2.0 -r by-dev -o rollback -w milvusdb/milvus:v2.1.4
./migrate.sh -i my-release -n milvus -s 2.1.4 -t 2.2.0 -r by-dev -o migrate -w milvusdb/milvus:v2.2.0
<button class="copy-code-btn"></button></code></pre></li>
</ol>
