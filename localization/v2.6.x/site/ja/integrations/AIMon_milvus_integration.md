---
id: AIMon_milvus_integration.md
summary: このチュートリアルでは、質問に答える検索支援世代（RAG）チャットボットの構築をお手伝いします。
title: AIMonとmilvusでLLM出願の検索品質を向上させる
---
<p><a href="https://colab.research.google.com/drive/1GqAhNZ6_Fm3PN_wX69MiBe7Pc6g2PjtK#scrollTo=cf2bee4f-c0b2-49e1-8a9c-3688c2d5fb55" target="_parent"><img translate="no" src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a></p>
<h1 id="Improve-retrieval-quality-of-your-LLM-Application-with-AIMon-and-Milvus" class="common-anchor-header">AIMonとmilvusでLLM出願の検索品質を向上させる<button data-href="#Improve-retrieval-quality-of-your-LLM-Application-with-AIMon-and-Milvus" class="anchor-icon" translate="no">
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
    </button></h1><h2 id="Overview" class="common-anchor-header">概要<button data-href="#Overview" class="anchor-icon" translate="no">
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
    </button></h2><p>このチュートリアルでは、<a href="https://meetingbank.github.io/">会議バンクのデータセットに関する</a>質問に回答する検索拡張世代（RAG）チャットボットを構築するお手伝いをします。</p>
<p>このチュートリアルでは以下のことを学びます：</p>
<ul>
<li>会議バンクデータセットに関連するユーザーの質問に答えるLLMアプリケーションを構築する。</li>
<li>LLMアプリケーションの品質を定義し、測定します。</li>
<li>ハイブリッド検索を使用したベクトルDBと最先端の再ランカーという2つのインクリメンタルアプローチを使用してアプリケーションの品質を向上させる。</li>
</ul>
<h2 id="Tech-Stack" class="common-anchor-header">技術スタック<button data-href="#Tech-Stack" class="anchor-icon" translate="no">
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
    </button></h2><h4 id="Vector-Database" class="common-anchor-header"><em>ベクトルデータベース</em></h4><p>このアプリケーションでは、<a href="https://milvus.io/">Milvusを</a>使用して、テキスト、画像、動画などの大規模な非構造化データを管理・検索する。</p>
<h4 id="LLM-Framework" class="common-anchor-header"><em>LLMフレームワーク</em></h4><p>LlamaIndexは、オープンソースのデータオーケストレーションフレームワークであり、プライベートデータとLLMの統合を容易にすることで、大規模言語モデル（LLM）アプリケーションの構築を簡素化し、RAG（Retrieval-Augmented Generation）パイプラインを通じて、文脈を考慮した生成AIアプリケーションを可能にする。このチュートリアルではLlamaIndexを使用します。LlamaIndexは柔軟性に優れ、より低レベルのAPIを抽象化できるからです。</p>
<h4 id="LLM-Output-Quality-Evaluation" class="common-anchor-header"><em>LLM出力品質の評価</em></h4><p><a href="https://www.aimon.ai">AIMonは</a>、幻覚、文脈品質問題、LLMの命令順守、検索品質、その他のLLMの信頼性タスクに対する独自の判定モデルを提供します。LLMアプリケーションの品質判定にAIMonを使用します。</p>
<pre><code translate="no" class="language-shell"><span class="hljs-meta prompt_">$ </span><span class="language-bash">pip3 install -U gdown requests aimon llama-index-core llama-index-vector-stores-milvus pymilvus&gt;=2.4.2 llama-index-postprocessor-aimon-rerank llama-index-embeddings-openai llama-index-llms-openai datasets fuzzywuzzy --quiet</span>
<button class="copy-code-btn"></button></code></pre>
<h1 id="Pre-requisites" class="common-anchor-header">前提条件<button data-href="#Pre-requisites" class="anchor-icon" translate="no">
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
    </button></h1><ol>
<li><p><a href="https://docs.aimon.ai/quickstart">こちらからAIMonアカウントに</a>サインアップしてください。</p>
<p>このシークレットをColab Secrets（左パネルの「鍵」マーク）に追加してください。 Google以外のColab環境にいる場合は、googleのColab関連コードをご自身で置き換えてください。</p>
<ul>
<li>AIMON_API_KEY</li>
</ul></li>
<li><p><a href="https://platform.openai.com/docs/overview">こちらからOpenAIのアカウントに</a>サインアップし、Colab secretsに以下のキーを追加してください：</p>
<ul>
<li>OPENAI_API_KEY</li>
</ul></li>
</ol>
<h3 id="Required-API-keys" class="common-anchor-header">必要なAPIキー</h3><pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> os

<span class="hljs-comment"># Check if the secrets are accessible</span>
<span class="hljs-keyword">from</span> google.colab <span class="hljs-keyword">import</span> userdata

<span class="hljs-comment"># Get this from the AIMon UI</span>
aimon_key = userdata.get(<span class="hljs-string">&quot;AIMON_API_KEY&quot;</span>)

openai_key = userdata.get(<span class="hljs-string">&quot;OPENAI_API_KEY&quot;</span>)

<span class="hljs-comment"># Set OpenAI key as an environment variable as well</span>
os.environ[<span class="hljs-string">&quot;OPENAI_API_KEY&quot;</span>] = openai_key
<button class="copy-code-btn"></button></code></pre>
<h2 id="Utility-Functions" class="common-anchor-header">ユーティリティ関数<button data-href="#Utility-Functions" class="anchor-icon" translate="no">
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
    </button></h2><p>このセクションでは、このノートブックを通して使用するユーティリティ関数を紹介します。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> openai <span class="hljs-keyword">import</span> OpenAI

oai_client = OpenAI(api_key=openai_key)


<span class="hljs-keyword">def</span> <span class="hljs-title function_">query_openai_with_context</span>(<span class="hljs-params">query, context_documents, model=<span class="hljs-string">&quot;gpt-4o-mini&quot;</span></span>):
    <span class="hljs-string">&quot;&quot;&quot;
    Sends a query along with context documents to the OpenAI API and returns the parsed response.

    :param api_key: OpenAI API key
    :param query: The user&#x27;s query as a string
    :param context_documents: A list of strings representing context documents
    :param model: The OpenAI model to use (default is &#x27;o3-mini&#x27;)
    :return: Response text from the OpenAI API
    &quot;&quot;&quot;</span>

    <span class="hljs-comment"># Combine context documents into a single string</span>
    context_text = <span class="hljs-string">&quot;\n\n&quot;</span>.join(context_documents)

    <span class="hljs-comment"># Construct the messages payload</span>
    messages = [
        {
            <span class="hljs-string">&quot;role&quot;</span>: <span class="hljs-string">&quot;system&quot;</span>,
            <span class="hljs-string">&quot;content&quot;</span>: <span class="hljs-string">&quot;You are an AI assistant that provides accurate and helpful answers.&quot;</span>,
        },
        {<span class="hljs-string">&quot;role&quot;</span>: <span class="hljs-string">&quot;user&quot;</span>, <span class="hljs-string">&quot;content&quot;</span>: <span class="hljs-string">f&quot;Context:\n<span class="hljs-subst">{context_text}</span>\n\nQuestion:\n<span class="hljs-subst">{query}</span>&quot;</span>},
    ]

    <span class="hljs-comment"># Call OpenAI API</span>
    completion = oai_client.chat.completions.create(model=model, messages=messages)

    <span class="hljs-comment"># Extract and return the response text</span>
    <span class="hljs-keyword">return</span> completion.choices[<span class="hljs-number">0</span>].message.content
<button class="copy-code-btn"></button></code></pre>
<h1 id="Dataset" class="common-anchor-header">データセット<button data-href="#Dataset" class="anchor-icon" translate="no">
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
    </button></h1><p>既存のデータセットを補足するために、アメリカの主要6都市の市議会から作成されたベンチマークデータセットである<a href="https://meetingbank.github.io/">MeetingBank</a>データセットを使用します。このデータセットには、1,366の会議、3,579時間以上のビデオ、議事録のPDF文書、議題、その他のメタデータが含まれています。</p>
<p>今回の演習では、より小規模なデータセットを作成した。それは<a href="https://drive.google.com/drive/folders/1v3vJahKtadi_r-8VJAsDd2eaiSRenmsa?usp=drive_link">ここに</a>あります。</p>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Delete the dataset folder if it already exists</span>

<span class="hljs-keyword">import</span> shutil

folder_path = <span class="hljs-string">&quot;/content/meetingbank_train_split.hf&quot;</span>

<span class="hljs-keyword">if</span> os.path.exists(folder_path):
    <span class="hljs-keyword">try</span>:
        shutil.rmtree(folder_path)
        <span class="hljs-built_in">print</span>(<span class="hljs-string">f&quot;Folder &#x27;<span class="hljs-subst">{folder_path}</span>&#x27; and its contents deleted successfully.&quot;</span>)
    <span class="hljs-keyword">except</span> Exception <span class="hljs-keyword">as</span> e:
        <span class="hljs-built_in">print</span>(<span class="hljs-string">f&quot;Error deleting folder &#x27;<span class="hljs-subst">{folder_path}</span>&#x27;: <span class="hljs-subst">{e}</span>&quot;</span>)
<span class="hljs-keyword">else</span>:
    <span class="hljs-built_in">print</span>(<span class="hljs-string">f&quot;Folder &#x27;<span class="hljs-subst">{folder_path}</span>&#x27; does not exist.&quot;</span>)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Folder '/content/meetingbank_train_split.hf' does not exist.
</code></pre>
<pre><code translate="no" class="language-shell"><span class="hljs-meta prompt_"># </span><span class="language-bash">Download the dataset locally</span>
<span class="hljs-meta prompt_">$ </span><span class="language-bash">gdown https://drive.google.com/uc?<span class="hljs-built_in">id</span>=1bs4kwwiD30DUeCjuqEdOeixCuI-3i9F5</span>
<span class="hljs-meta prompt_">$ </span><span class="language-bash">gdown https://drive.google.com/uc?<span class="hljs-built_in">id</span>=1fkxaS8eltjfkzws5BRXpVXnxl2Qxwy5F</span>
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Downloading...
From: https://drive.google.com/uc?id=1bs4kwwiD30DUeCjuqEdOeixCuI-3i9F5
To: /content/meetingbank_train_split.tar.gz
100% 1.87M/1.87M [00:00&lt;00:00, 104MB/s]
Downloading...
From: https://drive.google.com/uc?id=1fkxaS8eltjfkzws5BRXpVXnxl2Qxwy5F
To: /content/score_metrics_relevant_examples_2.csv
100% 163k/163k [00:00&lt;00:00, 69.6MB/s]
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> tarfile
<span class="hljs-keyword">from</span> datasets <span class="hljs-keyword">import</span> load_from_disk


tar_file_path = <span class="hljs-string">&quot;/content/meetingbank_train_split.tar.gz&quot;</span>
extract_path = <span class="hljs-string">&quot;/content/&quot;</span>

<span class="hljs-comment"># Extract the file</span>
<span class="hljs-keyword">with</span> tarfile.<span class="hljs-built_in">open</span>(tar_file_path, <span class="hljs-string">&quot;r:gz&quot;</span>) <span class="hljs-keyword">as</span> tar:
    tar.extractall(path=extract_path)

<span class="hljs-built_in">print</span>(<span class="hljs-string">f&quot;Extracted to: <span class="hljs-subst">{extract_path}</span>&quot;</span>)

train_split = load_from_disk(extract_path + <span class="hljs-string">&quot;meetingbank_train_split.hf&quot;</span>)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Extracted to: /content/
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-built_in">len</span>(train_split)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">258
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Total number of token across the entire set of transcripts</span>
<span class="hljs-comment"># This is approximately 15M tokens in size</span>
total_tokens = <span class="hljs-built_in">sum</span>(<span class="hljs-built_in">len</span>(example[<span class="hljs-string">&quot;transcript&quot;</span>].split()) <span class="hljs-keyword">for</span> example <span class="hljs-keyword">in</span> train_split)
<span class="hljs-built_in">print</span>(<span class="hljs-string">f&quot;Total number of tokens in train split: <span class="hljs-subst">{total_tokens}</span>&quot;</span>)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Total number of tokens in train split: 996944
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># number of words ~= # of tokens</span>
<span class="hljs-built_in">len</span>(train_split[<span class="hljs-number">1</span>][<span class="hljs-string">&quot;transcript&quot;</span>].split())
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">3137
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Show the first 500 characters of the transcript</span>
train_split[<span class="hljs-number">1</span>][<span class="hljs-string">&quot;transcript&quot;</span>][:<span class="hljs-number">500</span>]
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">&quot;An assessment has called out council bill 161 for an amendment. Madam Secretary, will you please put 161 on the screen? Councilman Lopez, will you make a motion to take 161 out of order? Want to remind everyone this motion is not debatable. Thank you, Mr. President. I move to take Council Bill 161 series of 2017. Out of order. All right. It's been moved the second it. Madam Secretary, roll call. SUSSMAN All right, Black. CLARK All right. Espinosa. Flynn. Gilmore. Herndon. Cashman can eat. LOPEZ &quot;
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Average number of tokens per transcript</span>
<span class="hljs-keyword">import</span> statistics

statistics.mean(<span class="hljs-built_in">len</span>(example[<span class="hljs-string">&quot;transcript&quot;</span>].split()) <span class="hljs-keyword">for</span> example <span class="hljs-keyword">in</span> train_split)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">3864.124031007752
</code></pre>
<h3 id="Analysis" class="common-anchor-header">分析</h3><p>258のトランスクリプトがあり、これらのトランスクリプト全体で合計約1Mのトークンがあります。トランスクリプトあたりの平均トークン数は3864です。</p>
<table>
<thead>
<tr><th>メトリック</th><th>値</th></tr>
</thead>
<tbody>
<tr><td>トランスクリプト数</td><td>258</td></tr>
<tr><td>トランスクリプト内のトークン総数</td><td>1M</td></tr>
<tr><td>平均# トランスクリプトあたりのトークン数</td><td>3864</td></tr>
</tbody>
</table>
<h3 id="Queries" class="common-anchor-header">クエリー</h3><p>以下は、上記のトランスクリプトに対して実行する12のクエリーです。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> pandas <span class="hljs-keyword">as</span> pd

queries_df = pd.read_csv(<span class="hljs-string">&quot;/content/score_metrics_relevant_examples_2.csv&quot;</span>)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-built_in">len</span>(queries_df[<span class="hljs-string">&quot;Query&quot;</span>])
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">12
</code></pre>
<pre><code translate="no" class="language-python">queries_df[<span class="hljs-string">&quot;Query&quot;</span>].to_list()
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">['What was the key decision in the meeting?',
 'What are the next steps for the team?',
 'Summarize the meeting in 10 words.',
 'What were the main points of discussion?',
 'What decision was made regarding the project?',
 'What were the outcomes of the meeting?',
 'What was discussed in the meeting?',
 'What examples were discussed for project inspiration?',
 'What considerations were made for the project timeline?',
 'Who is responsible for completing the tasks?',
 'What were the decisions made in the meeting?',
 'What did the team decide about the project timeline?']
</code></pre>
<h1 id="Metric-Definition" class="common-anchor-header">メトリックの定義<button data-href="#Metric-Definition" class="anchor-icon" translate="no">
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
    </button></h1><p>この品質スコア指標は、上記のクエリセットに対するLLMの応答がどの程度優れているかを理解するのに役立ちます。アプリケーションの品質を測定するために、一連のクエリを実行し、これらすべてのクエリの品質スコアを集計します。</p>
<p>LLMアプリケーション品質スコアは、AIMonの3つの個別品質メトリクスの組み合わせです：</p>
<ol>
<li><strong>幻覚スコア</strong>（hall_score）：生成されたテキストが提供されたコンテキストに根拠があるかどうかをチェックする。スコアが1.0に近いほど幻覚の兆候が強く、0.0に近いほど幻覚の兆候が低いことを意味する。したがって、最終的な品質スコアを計算する際には、ここでは（1.0-hall_score）を使用する。</li>
<li><strong>指示遵守スコア</strong>（ia_score）：提供されたすべての明示的指示がLLMによって遵守されたかどうかをチェックする。ia_scoreが高いほど、指示の遵守度が高い。スコアが低いほど、指示の遵守度が低い。</li>
<li><strong>検索関連度スコア</strong>（rr_score）：検索された文書がクエリに関連しているかどうかをチェックする。スコアが100.0に近いほどクエリとの関連性が高く、0.0に近いほどクエリとの関連性が低いことを意味する。</li>
</ol>
<p><code translate="no">quality_score = 0.35 * (1.0 - hall_score) + 0.35 * ia_score + 0.3 * rr_score</code></p>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># We will check the LLM response against these instructions</span>
instructions_to_evaluate = <span class="hljs-string">&quot;&quot;&quot;
1. Ensure that the response answers all parts of the query completely.
2. Ensure that the length of the response is under 50 words.
3. The response must not contain any abusive language or toxic content.
4. The response must be in a friendly tone.
&quot;&quot;&quot;</span>
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">def</span> <span class="hljs-title function_">compute_quality_score</span>(<span class="hljs-params">aimon_response</span>):
    retrieval_rel_scores = aimon_response.detect_response.retrieval_relevance[<span class="hljs-number">0</span>][
        <span class="hljs-string">&quot;relevance_scores&quot;</span>
    ]
    avg_retrieval_relevance_score = (
        statistics.mean(retrieval_rel_scores) <span class="hljs-keyword">if</span> <span class="hljs-built_in">len</span>(retrieval_rel_scores) &gt; <span class="hljs-number">0</span> <span class="hljs-keyword">else</span> <span class="hljs-number">0.0</span>
    )
    hall_score = aimon_response.detect_response.hallucination[<span class="hljs-string">&quot;score&quot;</span>]
    ia_score = aimon_response.detect_response.instruction_adherence[<span class="hljs-string">&quot;score&quot;</span>]
    <span class="hljs-keyword">return</span> (
        <span class="hljs-number">0.35</span> * (<span class="hljs-number">1.0</span> - hall_score)
        + <span class="hljs-number">0.35</span> * ia_score
        + <span class="hljs-number">0.3</span> * (avg_retrieval_relevance_score / <span class="hljs-number">100</span>)
    ) * <span class="hljs-number">100.0</span>
<button class="copy-code-btn"></button></code></pre>
<h1 id="Setup-AIMon" class="common-anchor-header">AIMonのセットアップ<button data-href="#Setup-AIMon" class="anchor-icon" translate="no">
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
    </button></h1><p>前述したように、AIMonはLLMアプリケーションの品質を判定するために使用される。<a href="https://docs.aimon.ai/">ドキュメントはこちらにあります</a>。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> aimon <span class="hljs-keyword">import</span> Detect

aimon_config = {
    <span class="hljs-string">&quot;hallucination&quot;</span>: {<span class="hljs-string">&quot;detector_name&quot;</span>: <span class="hljs-string">&quot;default&quot;</span>},
    <span class="hljs-string">&quot;instruction_adherence&quot;</span>: {<span class="hljs-string">&quot;detector_name&quot;</span>: <span class="hljs-string">&quot;default&quot;</span>},
    <span class="hljs-string">&quot;retrieval_relevance&quot;</span>: {<span class="hljs-string">&quot;detector_name&quot;</span>: <span class="hljs-string">&quot;default&quot;</span>},
}
task_definition = <span class="hljs-string">&quot;&quot;&quot;
Your task is to grade the relevance of context document against a specified user query.
The domain here is a meeting transcripts.
&quot;&quot;&quot;</span>

values_returned = [
    <span class="hljs-string">&quot;context&quot;</span>,
    <span class="hljs-string">&quot;user_query&quot;</span>,
    <span class="hljs-string">&quot;instructions&quot;</span>,
    <span class="hljs-string">&quot;generated_text&quot;</span>,
    <span class="hljs-string">&quot;task_definition&quot;</span>,
]

detect = Detect(
    values_returned=values_returned,
    api_key=userdata.get(<span class="hljs-string">&quot;AIMON_API_KEY&quot;</span>),
    config=aimon_config,
    publish=<span class="hljs-literal">True</span>,  <span class="hljs-comment"># This publishes results to the AIMon UI</span>
    application_name=<span class="hljs-string">&quot;meeting_bot_app&quot;</span>,
    model_name=<span class="hljs-string">&quot;OpenAI-gpt-4o-mini&quot;</span>,
)
<button class="copy-code-btn"></button></code></pre>
<h1 id="1-Simple-brute-force-approach" class="common-anchor-header">1.単純な総当りアプローチ<button data-href="#1-Simple-brute-force-approach" class="anchor-icon" translate="no">
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
    </button></h1><p>この最初の単純なアプローチでは、与えられたクエリと文書をマッチさせるためにレーベンシュタイン距離を使う。最もマッチした上位3つの文書が、回答のためのコンテキストとしてLLMに送られる。</p>
<p><strong>注意：このセルの実行には約3分かかります。</strong></p>
<p>待っている間、お好きな飲み物をお楽しみください :)</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> fuzzywuzzy <span class="hljs-keyword">import</span> process
<span class="hljs-keyword">import</span> time

<span class="hljs-comment"># List of documents</span>
documents = [t[<span class="hljs-string">&quot;transcript&quot;</span>] <span class="hljs-keyword">for</span> t <span class="hljs-keyword">in</span> train_split]


<span class="hljs-meta">@detect</span>
<span class="hljs-keyword">def</span> <span class="hljs-title function_">get_fuzzy_match_response</span>(<span class="hljs-params">query, docs</span>):
    response = query_openai_with_context(query, docs)
    <span class="hljs-keyword">return</span> docs, query, instructions_to_evaluate, response, task_definition


st = time.time()
quality_scores_bf = []
avg_retrieval_rel_scores_bf = []
responses = {}
<span class="hljs-keyword">for</span> user_query <span class="hljs-keyword">in</span> queries_df[<span class="hljs-string">&quot;Query&quot;</span>].to_list():
    best_match = process.extractBests(user_query, documents)
    matched_docs = [b[<span class="hljs-number">0</span>][:<span class="hljs-number">2000</span>] <span class="hljs-keyword">for</span> b <span class="hljs-keyword">in</span> best_match]
    _, _, _, llm_res, _, aimon_response = get_fuzzy_match_response(
        user_query, matched_docs[:<span class="hljs-number">1</span>]
    )
    <span class="hljs-comment"># These show the average retrieval relevance scores per query.</span>
    retrieval_rel_scores = aimon_response.detect_response.retrieval_relevance[<span class="hljs-number">0</span>][
        <span class="hljs-string">&quot;relevance_scores&quot;</span>
    ]
    avg_retrieval_rel_score_per_query = (
        statistics.mean(retrieval_rel_scores) <span class="hljs-keyword">if</span> <span class="hljs-built_in">len</span>(retrieval_rel_scores) &gt; <span class="hljs-number">0</span> <span class="hljs-keyword">else</span> <span class="hljs-number">0.0</span>
    )
    avg_retrieval_rel_scores_bf.append(avg_retrieval_rel_score_per_query)
    <span class="hljs-built_in">print</span>(
        <span class="hljs-string">&quot;Avg. Retrieval relevance score across chunks: {} for query: {}&quot;</span>.<span class="hljs-built_in">format</span>(
            avg_retrieval_rel_score_per_query, user_query
        )
    )
    quality_scores_bf.append(compute_quality_score(aimon_response))
    responses[user_query] = llm_res
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Time taken: {} seconds&quot;</span>.<span class="hljs-built_in">format</span>(time.time() - st))
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">/usr/local/lib/python3.11/dist-packages/fuzzywuzzy/fuzz.py:11: UserWarning: Using slow pure-python SequenceMatcher. Install python-Levenshtein to remove this warning
  warnings.warn('Using slow pure-python SequenceMatcher. Install python-Levenshtein to remove this warning')


Avg. Retrieval relevance score across chunks: 14.276227385821016 for query: What was the key decision in the meeting?
Avg. Retrieval relevance score across chunks: 13.863050225148754 for query: What are the next steps for the team?
Avg. Retrieval relevance score across chunks: 9.684561480011666 for query: Summarize the meeting in 10 words.
Avg. Retrieval relevance score across chunks: 15.117995085759617 for query: What were the main points of discussion?
Avg. Retrieval relevance score across chunks: 15.017772942191954 for query: What decision was made regarding the project?
Avg. Retrieval relevance score across chunks: 14.351198844659052 for query: What were the outcomes of the meeting?
Avg. Retrieval relevance score across chunks: 17.26337936069342 for query: What was discussed in the meeting?
Avg. Retrieval relevance score across chunks: 14.45748737410213 for query: What examples were discussed for project inspiration?
Avg. Retrieval relevance score across chunks: 14.69838895812785 for query: What considerations were made for the project timeline?
Avg. Retrieval relevance score across chunks: 11.528360411352168 for query: Who is responsible for completing the tasks?
Avg. Retrieval relevance score across chunks: 16.55915192723114 for query: What were the decisions made in the meeting?
Avg. Retrieval relevance score across chunks: 14.995106827925042 for query: What did the team decide about the project timeline?
Time taken: 169.34546852111816 seconds
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># This is the average quality score.</span>
avg_quality_score_bf = statistics.mean(quality_scores_bf)
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Average Quality score for brute force approach: {}&quot;</span>.<span class="hljs-built_in">format</span>(avg_quality_score_bf))
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Average Quality score for brute force approach: 51.750446187242254
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># This is the average retrieval relevance score.</span>
avg_retrieval_rel_score_bf = statistics.mean(avg_retrieval_rel_scores_bf)
<span class="hljs-built_in">print</span>(
    <span class="hljs-string">&quot;Average retrieval relevance score for brute force approach: {}&quot;</span>.<span class="hljs-built_in">format</span>(
        avg_retrieval_rel_score_bf
    )
)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Average retrieval relevance score for brute force approach: 14.31772340191865
</code></pre>
<p>これは<strong>ベースラインの</strong>LLMアプリの品質スコアです。AIMon<a href="https://www.app.aimon.ai/llmapps?source=sidebar&amp;stage=production">UIでは</a>、AIMonが計算した幻覚スコアなどの個々のメトリクスを見ることもできます。</p>
<h1 id="2-Use-a-VectorDB-Milvus-for-document-retrieval" class="common-anchor-header">2.ドキュメント検索にVectorDB（milvus）を使う<button data-href="#2-Use-a-VectorDB-Milvus-for-document-retrieval" class="anchor-icon" translate="no">
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
    </button></h1><p>今度は、ベクトルDBを追加することで品質スコアを向上させる。これは、以前のアプローチと比較して、クエリのレイテンシを改善するのにも役立つ。</p>
<p>注意すべき主なコンポーネントは2つある：インジェストとRAGベースのQ&amp;Aである。インジェストパイプラインは、Meeting Bankデータセットからトランスクリプトを処理し、Milvus Vectorデータベースに格納する。RAG Q&amp;Aパイプラインは、まずベクターストアから関連ドキュメントを取得することで、ユーザクエリを処理する。これらの文書はLLMが回答を生成するための基礎文書として使用される。AIMonを活用して品質スコアを計算し、<a href="https://docs.aimon.ai/detectors/hallucination">幻覚</a>、<a href="https://docs.aimon.ai/detectors/instruction_adherence">指示の順守</a>、<a href="https://docs.aimon.ai/checker-models/context_relevance">文脈の関連</a>性についてアプリケーションを継続的に監視する。これらは、上記の<code translate="no">quality</code> スコアの定義に使用したのと同じ3つのメトリクスです。</p>
<p>
  
   <span class="img-wrapper"> <img translate="no" src="/docs/v2.6.x/assets/aimon-workflow.png" alt="workflow" class="doc-image" id="workflow" />
   </span> <span class="img-wrapper"> <span>ワークフロー</span> </span></p>
<p>以下は、ドキュメントの前処理と埋め込みを計算するためのユーティリティ関数です。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> json
<span class="hljs-keyword">import</span> requests
<span class="hljs-keyword">import</span> pandas <span class="hljs-keyword">as</span> pd
<span class="hljs-keyword">from</span> llama_index.core <span class="hljs-keyword">import</span> Document


<span class="hljs-comment">## Function to preprocess text.</span>
<span class="hljs-keyword">def</span> <span class="hljs-title function_">preprocess_text</span>(<span class="hljs-params">text</span>):
    text = <span class="hljs-string">&quot; &quot;</span>.join(text.split())
    <span class="hljs-keyword">return</span> text


<span class="hljs-comment">## Function to process all URLs and create LlamaIndex Documents.</span>
<span class="hljs-keyword">def</span> <span class="hljs-title function_">extract_and_create_documents</span>(<span class="hljs-params">transcripts</span>):

    documents = []

    <span class="hljs-keyword">for</span> indx, t <span class="hljs-keyword">in</span> <span class="hljs-built_in">enumerate</span>(transcripts):
        <span class="hljs-keyword">try</span>:
            clean_text = preprocess_text(t)
            doc = Document(text=clean_text, metadata={<span class="hljs-string">&quot;index&quot;</span>: indx})
            documents.append(doc)
        <span class="hljs-keyword">except</span> Exception <span class="hljs-keyword">as</span> e:
            <span class="hljs-built_in">print</span>(<span class="hljs-string">f&quot;Failed to process transcript number <span class="hljs-subst">{indx}</span>: <span class="hljs-subst">{<span class="hljs-built_in">str</span>(e)}</span>&quot;</span>)

    <span class="hljs-keyword">return</span> documents


documents = extract_and_create_documents(train_split[<span class="hljs-string">&quot;transcript&quot;</span>])
<button class="copy-code-btn"></button></code></pre>
<p>Open AIベースの埋め込み計算モデルをセットアップする。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> llama_index.embeddings.openai <span class="hljs-keyword">import</span> OpenAIEmbedding

embedding_model = OpenAIEmbedding(
    model=<span class="hljs-string">&quot;text-embedding-3-small&quot;</span>, embed_batch_size=<span class="hljs-number">100</span>, max_retries=<span class="hljs-number">3</span>
)
<button class="copy-code-btn"></button></code></pre>
<p>このセルでは、<code translate="no">documents</code> の埋め込みを計算し、MilvusVectorStoreにインデックスを作成する。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> llama_index.core <span class="hljs-keyword">import</span> VectorStoreIndex, StorageContext
<span class="hljs-keyword">from</span> llama_index.vector_stores.milvus <span class="hljs-keyword">import</span> MilvusVectorStore

vector_store = MilvusVectorStore(
    uri=<span class="hljs-string">&quot;./aimon_embeddings.db&quot;</span>,
    collection_name=<span class="hljs-string">&quot;meetingbanks&quot;</span>,
    dim=<span class="hljs-number">1536</span>,
    overwrite=<span class="hljs-literal">True</span>,
)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

index = VectorStoreIndex.from_documents(
    documents=documents, storage_context=storage_context
)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">2025-04-10 20:40:51,855 [DEBUG][_create_connection]: Created new connection using: 24fee991f1f64fadb3461a7d99fcd4dd (async_milvus_client.py:600)


Execution time: 38.74 seconds
</code></pre>
<p>VectorDBのインデックスがセットアップされたので、ユーザーのクエリに答えるために活用します。以下のセルでは、retrieverを作成し、LLMをセットアップし、ユーザーの質問に答えるためにretrieverとLLMとインターフェースするLLamaIndex Query Engineを構築する。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> llama_index.core.retrievers <span class="hljs-keyword">import</span> VectorIndexRetriever
<span class="hljs-keyword">from</span> llama_index.core.query_engine <span class="hljs-keyword">import</span> RetrieverQueryEngine

retriever = VectorIndexRetriever(index=index, similarity_top_k=<span class="hljs-number">5</span>)

<span class="hljs-comment"># The system prompt that will be used for the LLM</span>
system_prompt = <span class="hljs-string">&quot;&quot;&quot;
                Please be professional and polite.
                Answer the user&#x27;s question in a single line.
                &quot;&quot;&quot;</span>
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment">## OpenAI&#x27;s LLM, we will use GPT-4o-mini here since it is a fast and cheap LLM</span>
<span class="hljs-keyword">from</span> llama_index.llms.openai <span class="hljs-keyword">import</span> OpenAI

llm = OpenAI(model=<span class="hljs-string">&quot;gpt-4o-mini&quot;</span>, temperature=<span class="hljs-number">0.1</span>, system_prompt=system_prompt)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> llama_index.core.query_engine <span class="hljs-keyword">import</span> RetrieverQueryEngine

query_engine = RetrieverQueryEngine.from_args(retriever, llm)
<button class="copy-code-btn"></button></code></pre>
<p>この時点で、クエリーエンジン、レトリーバー、LLMのセットアップが完了した。次に、品質スコアを測定するためのAIMonをセットアップします。上の前のセルで作成したのと同じ<code translate="no">@detect</code> デコレーターを使用します。<code translate="no">ask_and_validate</code> 、AIMonがLLamaIndexから取得したドキュメントの "ノード "とインターフェースするためのコードを追加しただけだ。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> logging


<span class="hljs-meta">@detect</span>
<span class="hljs-keyword">def</span> <span class="hljs-title function_">ask_and_validate</span>(<span class="hljs-params">user_query, user_instructions, query_engine=query_engine</span>):

    response = query_engine.query(user_query)

    <span class="hljs-comment">## Nested function to retrieve context and relevance scores from the LLM response.</span>
    <span class="hljs-keyword">def</span> <span class="hljs-title function_">get_source_docs</span>(<span class="hljs-params">chat_response</span>):
        contexts = []
        relevance_scores = []
        <span class="hljs-keyword">if</span> <span class="hljs-built_in">hasattr</span>(chat_response, <span class="hljs-string">&quot;source_nodes&quot;</span>):
            <span class="hljs-keyword">for</span> node <span class="hljs-keyword">in</span> chat_response.source_nodes:
                <span class="hljs-keyword">if</span> (
                    <span class="hljs-built_in">hasattr</span>(node, <span class="hljs-string">&quot;node&quot;</span>)
                    <span class="hljs-keyword">and</span> <span class="hljs-built_in">hasattr</span>(node.node, <span class="hljs-string">&quot;text&quot;</span>)
                    <span class="hljs-keyword">and</span> <span class="hljs-built_in">hasattr</span>(node, <span class="hljs-string">&quot;score&quot;</span>)
                    <span class="hljs-keyword">and</span> node.score <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> <span class="hljs-literal">None</span>
                ):
                    contexts.append(node.node.text)
                    relevance_scores.append(node.score)
                <span class="hljs-keyword">elif</span> (
                    <span class="hljs-built_in">hasattr</span>(node, <span class="hljs-string">&quot;text&quot;</span>)
                    <span class="hljs-keyword">and</span> <span class="hljs-built_in">hasattr</span>(node, <span class="hljs-string">&quot;score&quot;</span>)
                    <span class="hljs-keyword">and</span> node.score <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> <span class="hljs-literal">None</span>
                ):
                    contexts.append(node.text)
                    relevance_scores.append(node.score)
                <span class="hljs-keyword">else</span>:
                    logging.info(<span class="hljs-string">&quot;Node does not have required attributes.&quot;</span>)
        <span class="hljs-keyword">else</span>:
            logging.info(<span class="hljs-string">&quot;No source_nodes attribute found in the chat response.&quot;</span>)
        <span class="hljs-keyword">return</span> contexts, relevance_scores

    context, relevance_scores = get_source_docs(response)
    <span class="hljs-keyword">return</span> context, user_query, user_instructions, response.response, task_definition
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Quick check to ensure everything is working with the vector DB</span>
ask_and_validate(<span class="hljs-string">&quot;Councilman Lopez&quot;</span>, instructions_to_evaluate)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">([&quot;I know in in New Mexico on some of the reservations, there are people actually doing filming, too, now of some of the elders to make sure that that history is documented and passed on, because it isn't translated in many of the history books you get in your public education system. So I again, just am happy to support this and again commend Councilman Lopez for his efforts in our Indian commission and the work that you all have done with our entire entire community. Thank you, Mr. President. Thank you, Councilwoman. Councilman Lopez, I see you back up. Yeah. You know, I wanted to really emphasize the 10th, Monday, the 10th and proclamation that will be here in the quarters we'd love for. And I wanted to make sure the community because we do have community folks, I want to make sure that we come back on the 10th because we would like to give not only the proclamation, but a copy of the bill over. Right. And and ceremoniously and also just for the community. I know this Saturday I didn't mention this, but the Saturday is going to be, in addition to all the events, a rally at the Capitol at 1130. I mean, 130, 130. You mean marches coming from all over the city and they're going to be here. Good celebration of all directions, all nations. And that that's really when you when you look at the what it really means is all directions all nations for went. Thank you. Thank you, Councilman Lopez. Madam Secretary. Raquel Lopez. Hi. New Ortega I Black High Clerk by Espinosa. By. Flynn. Hi Gilmore I Herndon in Cashman. I can eat. Mr. President. I please close voting in US results. 12 eyes. 12 eyes conceal. 801 passes. Thank you. Thank you. Thank you. You don't get many claps for votes anymore. Thank you. All right. We are moving to the Bloc votes. All other bills for introductions are now ordered published. Councilman, clerk, will you please put the resolutions for adoption and the bills for final considerations? Consideration for final passage on the floor. Thank you, Mr. President. I move that resolutions be adopted and bills on final consideration be placed upon final consideration, and do pass in a block for the following items. 539 811 816. 812. 813. 814. 820. 821. 822. 800. 815. Eight 1724. 761 797. All right. It has been moved. And second, it councilmembers. Please remember that this is a consent or block vote and you will need to vote I or otherwise. This is your last chance to call out an item for a separate vote. Madam Secretary, roll call. Black. I Clark. II. Espinosa, i Flynn, i Gilmore. I Herndon I Cashman. I can eat. I knew. I. Ortega, I. Mr. President. I. Please close the voting. Announce a results. 11 Eyes. 11 Eyes. The resolution resolutions have been adopted and bills have been placed on final consideration and do pass. Tonight there will be a required public hearing for Council Bill 430 Changes on a classification of four Geneva Court and Martin Luther King Jr Boulevard.&quot;,
  &quot;Thank you, gentlemen. Lopez, can you please place Council Bill 376 on the floor for a vote? Thank you, Madam President. I move that council bill three 76/3 of 2015 be placed upon final, final consideration and do pass. Thank you. It's been moved and seconded. Comments by members of council. Councilwoman Fox. Thank you, Madam President. This is an ordinance that lends money to a developer for relocation costs of a project that's very important along Morrison Road. I approve of the project. I even approve of doing this relocation cost. But I am not willing to do is to lend more money to this specific developer. In a previous deal we had not only a financial deal with the developer, but there were two subsequent amendments to that deal, both of which were to the benefit of the developer, not the taxpayer. And so I am very picky about who I lend money to, and I'm going to say no today. Okay. Thank you, Councilman Lopez. Thank you, Madam President. I do have something to say about this council, bill. Yes? This is Saint Charles Holding Company as the developer of the site. Here's the problem. The problem is this site has been blighted for decades. And in this site, it's not like it's been empty. There have been folks who are living in these conditions that have been substandard and Denver and it's just not right. And we've talked about it for eight years. We looked at opportunities to what can we do to help improve the living conditions here for folks. And there was a lot of unanswered questions and a lot of people who how we had the ability to do it but are afraid to take the risk, afraid to do, afraid to come forward and basically not participate at all. That was true up into the point where Saint Charles Town Company and I think Charles Holding Company here said, you know, we'll do it. Will help will help not only improve the conditions here at this site by acquiring it, but will help trigger the Federal Relocation Act with the city. The city said, we will do this with you. There are folks who are living there who, because of this development, will be able to finally live anywhere else, be able to get benefits for it, relocation costs. And when all these units are built at 60%, am I going to be able to have first refusal, meaning they get the first choice to come back and this is how it should happen. And we can't rely just on VHA or some of the nonprofit folks who are already up. You know, they have their hands full. They don't have enough resources. They're begging for money. They're all fighting over the same pot of money, the same federal pot of money. It should we should actually be working with folks who are in the for profit development side that are willing to do this. And they've done it before on Alameda and Sheridan with those altos down. I mean, it's a very good project, filled a huge need in this city for affordable housing. That's what this does. And now affordable housing in the kind where you know that nobody takes care of and it's forgotten about. And when you complain, you either get kicked out or you just deal with it. Right. But this is the kind of housing these are the kind of units, units that will be maintained that are high quality standard of living, exactly what folks are needing and deserving in this neighborhood. And these are the folks that are willing to do the work. They've been doing the work with the community. It takes partnership from the city. This will help finalize those costs, help those folks find a place to live that way. They're not on the street while this develops or when they come back. I guarantee everybody is going to be standing there wanting to cut that ribbon. So that's what this is all about. And I urge my colleagues to support it. Thank you. Thank you, Councilman Lopez. Madam Secretary. Roll call. Fats? No. Lemon Lopez Monteiro. Hi, Nevitt. Hi, Ortega. Hi, Rob. Shepherd Sussman. Hi, Brooks. Hi, Brown. Hi, Sussman. There's no opportunity for me to click on I. Okay, I'll do it. Madam President. I voted. I call to him. He says, When were you able to vote? No, there's no. I voted for her. Okay. And what was the vote? Yes.&quot;,
  &quot;This year we may talk trash once in a while, but a manager got an AHA. You run a very good shop with a great manager at his helm. So thank you. Thank you, Councilman Lopez, Councilwoman Monteiro. And thank you, Madam President. I also want to take the opportunity to extend my appreciation. I wish I knew all 1100 employees. But here's what I do know that public works does everything from Keep Denver Beautiful this far as graffiti, graffiti removal all the way to major projects. I'm very mindful of the role that Public Works played in the redevelopment of Denver Union Station and the work that you're doing currently regarding I-70 and the National Western Stock Show. And we couldn't we couldn't do wouldn't be responsive if we didn't have the help of solid waste. Also, permitting and enforcement have worked with a lot of people, their street maintenance. And then of course, Nancy, I have an inbox of a lot of emails that you and I have, so I'm going to have to start deleting some of those. I also want to extend my thank you to host Cornell for all of the work that you do and that and for your steadfast, steadfast leadership. And also to George Delaney, who you're there when Jose is away from the helm. And I really appreciate that. So congratulations again. Let me see if I got these names right. Jason, Chloe, Adrian, Luis, Jeremy, Cindy, Patrick and Ron. So I hope I got everybody's name. Thank you. Thank you, Counselor Monteiro. All right. Looks like we're ready for the vote. Rob, I. Shepherd, I. But I. Herndon, I can eat lemon. Lopez All right. Monteiro I love it. Hi, Ortega. Hi. Madam President. I am close to voting out the results. 11 eyes. 11 eyes. The proclamation is adopted, Councilman Roberts or somebody would like to call up to the podium. Yes, Madam President, I expected my colleagues to support this, but I hope no one out there was sort of insulted with some of the comments. I would like to call up the interesting, sexy, cool and strong executive director of the Public Works Department, which is an interesting, sexy, cool and strong department. Good one. Councilwoman Rob. Cook. I'm single network secretary, director of Public Works and I want to thank on behalf of Public Works this proclamation. It is truly an honor to be part of this organization and work with these 1100 people that I would say are fully committed not only to the council priority by the mayor's priorities, but also the stakeholders priorities, and be able to mix all these priorities together and come up with public works priority, which is to create a smart city, meaning a sustainable city, a city that provides mobility in a safe way and attractive city resiliency, and also a process to be the most transparent process that we can deliver. Somebody says, I was reading this book the other day. Somebody says that the public space is the the visible face of society. And I do believe that. I think that that's how we judge cities when we go around the world and come back. I like to talk a little bit, spend a little bit of time talking about the ten employees that we're honoring tonight, because I think it's very important to mention exactly what these employees have been working and being part of. Jason Rediker from Capital Management recently designed two very critical storm sewer projects. One of them have been in neighborhood. And the other one is at first and university, which is under construction in your district. Chloe Thompson from Finance Administration. She is one of our first black built from the academy. She has worked very hard to improve and develop new models for for the financial track in streamlining contracts, contracting and putting in place a more efficient procurement process. Adrian Goldman from Fleet Management. Adrian was very close with our fleet technicians downloading software that helps to better diagnose vehicles and speed up the repair process. Lewis Gardner From Right of Way Enforcement in permitting, Lewis is a very diligent vehicle investigator who goes above and beyond to assist not only the public but also the the in the agency. He volunteers to maintain city's vehicle inventory and has taken the lead role a role a role in making sure that the motors workshop is free of hazardous materials and and mark problems. Jeremy Hammer from right of way services. He's our lead on floodplain issues. He's responsible for very complex flood floodplains and drainage issues.&quot;,
  &quot;So. So I think that this is a fitting combination to have these tonight. And I will be happily voting in support of this. Thank you. Thank you, Councilwoman. Councilman Espinosa, I saw you click in. I did, but I. I'll reserve my comments. Okay, great. We have. Let's see. No other comments. Councilman Ortega. Sorry, is shown on my screen. I'm not sure why it's not on yours. Thank you. I just wanted to make a few brief comments as well and thank Councilman Lopez for his efforts in working with the community to bring this forward. And I know this is something that has been in the works for a very long time. So thank you for your efforts. I just. Think that it's important also to mention the role that our Native American community played in. You heard me talk about DIA earlier. When we were moving forward with the construction of the highway, one of the things that happened was we worked with some of the tribal leaders to do a ground blessing on the site. As you all know, that used to be part of the old Sand Creek Massacre corridor. And I thought it was extremely important for Denver to do that. And the interesting thing about the event was the media wanted to know when and where it was going to take place. And I worked with Mayor Webb at the time to ensure that that happened. I didn't attend it. We made sure the media didn't know when and where it was because it was, you know, a very traditional sacred event that needed to take place and to, you know, pray for the lives of of the souls who were lost in that massacre. The other thing that Councilman Lopez talked about was the the history of where Denver started. It started with our Native American community right at the at the core of the Confluence Park. The city acknowledges that to the degree of seeing a number of the the parks, I mean, not the parks, but the roads down in the lower downtown and platte valley area named after some of the tribal leaders. We want to wynkoop a little raven. I remember when the naming of little raven was being proposed. Our public works department was recommending that that be called 29th Street. And I just you know, I was the councilperson of the district at the time. And I said, how do we make these decisions about what streets, what we're naming our streets? And I said, What other names did you look at? And they mentioned Little Raven. And this was when they were bringing through the committee process to do the official naming. And I said, I want it named Little Raven. And so when when that official, you know, name was put up on the street, we actually had some of the tribal leaders from the Cheyenne tribe there, and they actually were given a street sign that they were able to take and put up on display in their community. So just being part of so many of the things that have happened in this city is exciting. I worked at the state capitol when the Commission in Indian Affairs was created in George Brown's office. The lieutenant governors, it's been part of that office. I worked there and had the benefit of going to a peace treaty signing ceremony between the U.S. and the Comanches, who had been at war with each other for for 100 years. And a lot of these things, as Councilman Lopez said, are not written in our history books. You know, you in and one of the things that's occurring and those of you who have not taken the time to talk to your elders and record some of the history so that you pass it on to, you know, our children is is so important. I know in in New Mexico on some of the reservations, there are people actually doing filming, too, now of some of the elders to make sure that that history is documented and passed on, because it isn't translated in many of the history books you get in your public education system. So I again, just am happy to support this and again commend Councilman Lopez for his efforts in our Indian commission and the work that you all have done with our entire entire community. Thank you, Mr. President. Thank you, Councilwoman. Councilman Lopez, I see you back up. Yeah. You know, I wanted to really emphasize the 10th, Monday, the 10th and proclamation that will be here in the quarters we'd love for.&quot;,
  &quot;President. I call Madam Speaker, close voting. Announce the results. 3913 eyes. Constable 898 has been amended. Councilman Lopez, please. We need a motion to pass as amended now. Mr. President, I move that council bill 898 series of 2016 be moved and be passed on final, final consideration as amended. Okay. It has been moved in second. It comes from members of council. It comes from our take as this from the prior. It was just hasn't gone away. All right. Madam Secretary, roll call. Can each I. LOPEZ All right. New ORTEGA High Assessment by Black. Clark by Espinosa. FLYNN Hi. Gilmore I Herndon. I Cashman. Hi, Mr. President. I Please close the voting and ask for results. 3913 Eyes Council Bill 898 has passed as amended. Okay, just want to make sure looking down the road, make sure there are no other items that need to be called out. We're ready for the block votes. All other bills for introduction are order published. We are now ready. So council members, please remember that this is a consent block vote and you will need to vote. Otherwise this is your last chance to call out an item for a separate votes. Guzman Lopez, will you please put the resolutions for adoption and the bills for final consideration for final passage on the floor? We put them both at the same time. Yeah. The read through. That's what we did last week. Yeah. And it's easy if you do it from the screen. All right. I motion to approve the consent agenda. So the motion would be. No. No, do I. Do I run through all those resolutions and bills? Yep. Just all of them at once. Yep. All right. Back in my day, we brought it on. Oh, I'm just kidding. All right, Mr. President. Okay. I move that. Our series of 2016, the following resolutions 1000 982 998, 1000 to 8, 79, 33, nine, 34, nine, 92 and 93, 96, 99, 1003. And the following bills for consideration to series at 2016 979 nine 8947 nine 5959 961 974, nine, 75 and 85 831 972 973. And 1978 be released upon. Of do pass in block. Okay. Madam Secretary, I think he got all of them. Yes. Would you concur? Okay, great. Rook for. Black Eye Clerk. By. Vanessa Flynn I. Gilmore, i. Herndon, i. Catherine Kennedy I. Lopez I knew Ortega i susman i. Mr. President. I 3939 resolutions have been adopted and bills have been placed upon final consideration and do pass tonight. Council is scheduled to sit as the quasi Judicial Board of Equalization to consider reduction of total cost assessments for the one local maintenance district.&quot;],
 'Councilman Lopez',
 '\n1. Ensure that the response answers all parts of the query completely.\n2. Ensure that the length of the response is under 50 words.\n3. The response must not contain any abusive language or toxic content.\n4. The response must be in a friendly tone.\n',
 'Councilman Lopez has been actively involved in community efforts, particularly regarding the documentation of Native American history and supporting housing development projects.',
 '\nYour task is to grade the relevance of context document against a specified user query.\nThe domain here is a meeting transcripts.\n',
 DetectResult(
   status=200,
   detect_response=avg_context_doc_length: 18190.0
 hallucination: {
     &quot;is_hallucinated&quot;: &quot;False&quot;,
     &quot;score&quot;: 0.0696,
     &quot;sentences&quot;: [
         {
             &quot;score&quot;: 0.0696,
             &quot;text&quot;: &quot;Councilman Lopez has been actively involved in community efforts, particularly
 regarding the documentation of Native American history and supporting housing development projects.&quot;
         }
     ]
 }
 instruction_adherence: {
     &quot;results&quot;: [
         {
             &quot;adherence&quot;: true,
             &quot;detailed_explanation&quot;: &quot;The response addresses components related to Councilman Lopez's
 community involvement and specific areas such as the documentation of Native American history and
 housing projects, thus answering the query completely.&quot;,
             &quot;instruction&quot;: &quot;Ensure that the response answers all parts of the query completely.&quot;
         },
         {
             &quot;adherence&quot;: true,
             &quot;detailed_explanation&quot;: &quot;The response contains 23 words, which is under the specified
 limit of 50 words.&quot;,
             &quot;instruction&quot;: &quot;Ensure that the length of the response is under 50 words.&quot;
         },
         {
             &quot;adherence&quot;: true,
             &quot;detailed_explanation&quot;: &quot;The response uses neutral and positive language without any
 abusive or toxic content.&quot;,
             &quot;instruction&quot;: &quot;The response must not contain any abusive language or toxic content.&quot;
         },
         {
             &quot;adherence&quot;: true,
             &quot;detailed_explanation&quot;: &quot;The tone of the response is friendly and informative,
 highlighting Councilman Lopez's positive contributions to the community.&quot;,
             &quot;instruction&quot;: &quot;The response must be in a friendly tone.&quot;
         }
     ],
     &quot;score&quot;: 1.0
 }
 retrieval_relevance: [
     {
         &quot;explanations&quot;: [
             &quot;Document 1 discusses Councilman Lopez's efforts in the Indian commission and his
 involvement in community events, directly referencing his name and contributions. However, the
 document is lengthy and contains a lot of extraneous information about unrelated topics, which
 dilutes the focus on Councilman Lopez and makes it less relevant to a query specifically seeking
 information about him.&quot;,
             &quot;2. In Document 2, Councilman Lopez is mentioned in relation to a council bill and his
 comments on a development project, which shows his active role in council discussions. The document,
 however, focuses more on the specific project and other council members' opinions rather than
 providing substantial information about Councilman Lopez himself, leading to a lower relevance
 score.&quot;,
             &quot;3. Document 3 acknowledges Councilman Lopez in the context of public works and city
 management, which shows that he is recognized for his contributions. However, the document primarily
 discusses public works and does not delve deeply into Councilman Lopez's specific actions or
 achievements, making it less relevant to the query.&quot;,
             &quot;4. In Document 4, Councilman Lopez is commended for his efforts in the community and
 for working with the Native American community, indicating his involvement in significant local
 issues. Yet, the document is more focused on the broader context of community history and events,
 which detracts from a focused discussion on Councilman Lopez himself.&quot;,
             &quot;5. Document 5 mentions Councilman Lopez in the context of voting on a council bill and
 procedural matters, showcasing his active participation in council decisions. However, it lacks
 detailed insights into his specific contributions or perspectives regarding the bills, making it
 less informative for someone looking for in-depth information about Councilman Lopez.&quot;
         ],
         &quot;query&quot;: &quot;Councilman Lopez&quot;,
         &quot;relevance_scores&quot;: [
             35.66559540012122,
             37.18941956657886,
             33.50108754888339,
             33.29029488991324,
             38.80187100744479
         ]
     }
 ],
   publish_response=[]
 ))
</code></pre>
<p><code translate="no">queries_df</code> 、LlamaIndexクエリーエンジンを通してすべてのクエリーを実行し、AIMonを使って総合的な品質スコアを計算してみましょう。</p>
<p><strong>注意：これには約2分かかります</strong></p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> time

quality_scores_vdb = []
avg_retrieval_rel_scores_vdb = []
responses_adb = {}
ast = time.time()
<span class="hljs-keyword">for</span> user_query <span class="hljs-keyword">in</span> queries_df[<span class="hljs-string">&quot;Query&quot;</span>].to_list():
    _, _, _, llm_res, _, aimon_response = ask_and_validate(
        user_query, instructions_to_evaluate
    )
    <span class="hljs-comment"># These show the average retrieval relevance scores per query. Compare this to the previous brute force method.</span>
    retrieval_rel_scores = aimon_response.detect_response.retrieval_relevance[<span class="hljs-number">0</span>][
        <span class="hljs-string">&quot;relevance_scores&quot;</span>
    ]
    avg_retrieval_rel_score_per_query = (
        statistics.mean(retrieval_rel_scores) <span class="hljs-keyword">if</span> <span class="hljs-built_in">len</span>(retrieval_rel_scores) &gt; <span class="hljs-number">0</span> <span class="hljs-keyword">else</span> <span class="hljs-number">0.0</span>
    )
    avg_retrieval_rel_scores_vdb.append(avg_retrieval_rel_score_per_query)
    <span class="hljs-built_in">print</span>(
        <span class="hljs-string">&quot;Avg. Retrieval relevance score across chunks: {} for query: {}&quot;</span>.<span class="hljs-built_in">format</span>(
            avg_retrieval_rel_score_per_query, user_query
        )
    )
    quality_scores_vdb.append(compute_quality_score(aimon_response))
    responses_adb[user_query] = llm_res
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Time elapsed: {} seconds&quot;</span>.<span class="hljs-built_in">format</span>(time.time() - ast))
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Avg. Retrieval relevance score across chunks: 19.932596854170086 for query: What was the key decision in the meeting?
Avg. Retrieval relevance score across chunks: 19.332469976717874 for query: What are the next steps for the team?
Avg. Retrieval relevance score across chunks: 13.695729082342893 for query: Summarize the meeting in 10 words.
Avg. Retrieval relevance score across chunks: 20.276701279455835 for query: What were the main points of discussion?
Avg. Retrieval relevance score across chunks: 19.642715112968148 for query: What decision was made regarding the project?
Avg. Retrieval relevance score across chunks: 17.880496811886246 for query: What were the outcomes of the meeting?
Avg. Retrieval relevance score across chunks: 23.53911458826815 for query: What was discussed in the meeting?
Avg. Retrieval relevance score across chunks: 17.665638657211105 for query: What examples were discussed for project inspiration?
Avg. Retrieval relevance score across chunks: 18.13388221868742 for query: What considerations were made for the project timeline?
Avg. Retrieval relevance score across chunks: 18.955595009379778 for query: Who is responsible for completing the tasks?
Avg. Retrieval relevance score across chunks: 22.840146597476263 for query: What were the decisions made in the meeting?
Avg. Retrieval relevance score across chunks: 19.665652140639054 for query: What did the team decide about the project timeline?
Time elapsed: 125.75674271583557 seconds
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># This is the average quality score.</span>
avg_quality_score_vdb = statistics.mean(quality_scores_vdb)
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Average Quality score for vector DB approach: {}&quot;</span>.<span class="hljs-built_in">format</span>(avg_quality_score_vdb))
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Average Quality score for vector DB approach: 67.1800392915634
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># This is the average retrieval relevance score.</span>
avg_retrieval_rel_score_vdb = statistics.mean(avg_retrieval_rel_scores_vdb)
<span class="hljs-built_in">print</span>(
    <span class="hljs-string">&quot;Average retrieval relevance score for vector DB approach: {}&quot;</span>.<span class="hljs-built_in">format</span>(
        avg_retrieval_rel_score_vdb
    )
)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Average retrieval relevance score for vector DB approach: 19.296728194100236
</code></pre>
<h2 id="🎉-Quality-Score-improved" class="common-anchor-header">品質スコアが向上した！<button data-href="#🎉-Quality-Score-improved" class="anchor-icon" translate="no">
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
    </button></h2><p>RAGベースのQAシステムを使用した後、すべてのクエリの総合品質スコアが向上したことに注目してください。</p>
<h1 id="3-Add-Re-ranking-to-your-retrieval" class="common-anchor-header">3.検索に再ランク付けを追加する<button data-href="#3-Add-Re-ranking-to-your-retrieval" class="anchor-icon" translate="no">
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
    </button></h1><p>次に、AIMonのLlamaIndex<a href="https://docs.llamaindex.ai/en/latest/examples/node_postprocessor/AIMonRerank/">ポストプロセッサの再ランク統合を</a>使用して、AIMonの<a href="https://docs.aimon.ai/retrieval#domain-adaptable-re-ranking">ドメイン適応可能な再ランクを</a>追加します。</p>
<p>下図に示すように、再ランク付けは、より高度なクエリ-文書マッチング機能を使用することで、最も関連性の高い文書を上位にバブルアップするのに役立ちます。AIMonのリランカーのユニークな特徴は、ドメインごとにカスタマイズできることです。LLMのプロンプトエンジニアリングと同様に、<code translate="no">task_definition</code> フィールドを使用して、ドメインごとにリランカーのパフォーマンスをカスタマイズすることができます。この最先端のリランカーは、超低レイテンシー（～2kのコンテキストに対して）で動作し、そのパフォーマンスはMTEBリランキングリーダーボードのトップ5にランクインしている。</p>
<p><img translate="no" src="https://raw.githubusercontent.com/devvratbhardwaj/images/refs/heads/main/AIMon_Reranker.svg" alt="Diagram depicting working of AIMon reranker"/></p>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Setup AIMon&#x27;s reranker</span>

<span class="hljs-keyword">from</span> llama_index.postprocessor.aimon_rerank <span class="hljs-keyword">import</span> AIMonRerank

<span class="hljs-comment"># This is a simple task_definition, you can polish and customize it for your use cases as needed</span>
task_definition = <span class="hljs-string">&quot;&quot;&quot;
Your task is to match documents for a specific query.
The documents are transcripts of meetings of city councils of 6 major U.S. cities.
&quot;&quot;&quot;</span>

aimon_rerank = AIMonRerank(
    top_n=<span class="hljs-number">2</span>,
    api_key=userdata.get(<span class="hljs-string">&quot;AIMON_API_KEY&quot;</span>),
    task_definition=task_definition,
)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Setup a new query engine but now with a reranker added as a post processor after retrieval</span>

query_engine_with_reranking = RetrieverQueryEngine.from_args(
    retriever, llm, node_postprocessors=[aimon_rerank]
)
<button class="copy-code-btn"></button></code></pre>
<p>もう一度クエリを実行し、全体的な品質スコアを再計算して、改善があるかどうかを見てみましょう。</p>
<p><strong>AIMonの再ランキングは、レスポンスを生成するためにLLMに送信する必要があるコンテキスト・ドキュメントの量を減らし、ネットワークI/OとLLMトークンの処理コスト（お金と時間）の点で効率的なオペレーションを実現するため、追加のレイテンシー・オーバヘッドを追加すべきではありません。</strong></p>
<p><strong>注意：このステップには2分かかります</strong></p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> time

qual_scores_rr = []
avg_retrieval_rel_scores_rr = []
responses_adb_rr = {}
ast_rr = time.time()
<span class="hljs-keyword">for</span> user_query <span class="hljs-keyword">in</span> queries_df[<span class="hljs-string">&quot;Query&quot;</span>].to_list():
    _, _, _, llm_res, _, aimon_response = ask_and_validate(
        user_query, instructions_to_evaluate, query_engine=query_engine_with_reranking
    )
    <span class="hljs-comment"># These show the average retrieval relevance scores per query. Compare this to the previous method without the re-ranker</span>
    retrieval_rel_scores = aimon_response.detect_response.retrieval_relevance[<span class="hljs-number">0</span>][
        <span class="hljs-string">&quot;relevance_scores&quot;</span>
    ]
    avg_retrieval_rel_score_per_query = (
        statistics.mean(retrieval_rel_scores) <span class="hljs-keyword">if</span> <span class="hljs-built_in">len</span>(retrieval_rel_scores) &gt; <span class="hljs-number">0</span> <span class="hljs-keyword">else</span> <span class="hljs-number">0.0</span>
    )
    avg_retrieval_rel_scores_rr.append(avg_retrieval_rel_score_per_query)
    <span class="hljs-built_in">print</span>(
        <span class="hljs-string">&quot;Avg. Retrieval relevance score across chunks: {} for query: {}&quot;</span>.<span class="hljs-built_in">format</span>(
            avg_retrieval_rel_score_per_query, user_query
        )
    )
    qual_scores_rr.append(compute_quality_score(aimon_response))
    responses_adb_rr[user_query] = llm_res
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Time elapsed: {} seconds&quot;</span>.<span class="hljs-built_in">format</span>(time.time() - ast_rr))
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Avg. Retrieval relevance score across chunks: 36.436465411440366 for query: What was the key decision in the meeting?
Avg. Retrieval relevance score across chunks: 38.804003013309085 for query: What are the next steps for the team?
Avg. Retrieval relevance score across chunks: 45.29209086832342 for query: Summarize the meeting in 10 words.
Avg. Retrieval relevance score across chunks: 36.979413900164815 for query: What were the main points of discussion?
Avg. Retrieval relevance score across chunks: 41.149971422535714 for query: What decision was made regarding the project?
Avg. Retrieval relevance score across chunks: 36.57368907582921 for query: What were the outcomes of the meeting?
Avg. Retrieval relevance score across chunks: 42.34540670899989 for query: What was discussed in the meeting?
Avg. Retrieval relevance score across chunks: 33.857591391574715 for query: What examples were discussed for project inspiration?
Avg. Retrieval relevance score across chunks: 38.419397677952816 for query: What considerations were made for the project timeline?
Avg. Retrieval relevance score across chunks: 42.91262631898647 for query: Who is responsible for completing the tasks?
Avg. Retrieval relevance score across chunks: 41.417109763746396 for query: What were the decisions made in the meeting?
Avg. Retrieval relevance score across chunks: 43.34866213159572 for query: What did the team decide about the project timeline?
Time elapsed: 97.93312644958496 seconds
</code></pre>
<p>リランカーを使用した場合と使用しなかった場合、そして素朴で力任せのアプローチを使用した場合の、文書関連性スコアの平均値の違いに注目してください。</p>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># This is the average quality score.</span>
avg_quality_score_rr = statistics.mean(qual_scores_rr)
<span class="hljs-built_in">print</span>(
    <span class="hljs-string">&quot;Average Quality score for AIMon Re-ranking approach: {}&quot;</span>.<span class="hljs-built_in">format</span>(
        avg_quality_score_rr
    )
)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Average Quality score for AIMon Re-ranking approach: 74.62174819211145
</code></pre>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># This is the average retrieval relevance score.</span>
avg_retrieval_rel_score_rr = statistics.mean(avg_retrieval_rel_scores_rr)
<span class="hljs-built_in">print</span>(
    <span class="hljs-string">&quot;Average retrieval relevance score for AIMon Re-ranking approach: {}&quot;</span>.<span class="hljs-built_in">format</span>(
        avg_retrieval_rel_score_rr
    )
)
<button class="copy-code-btn"></button></code></pre>
<pre><code translate="no">Average retrieval relevance score for AIMon Re-ranking approach: 39.794702307038214
</code></pre>
<h2 id="🎉-Again-Quality-Score-improved" class="common-anchor-header">再び、品質スコアが向上しました！<button data-href="#🎉-Again-Quality-Score-improved" class="anchor-icon" translate="no">
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
    </button></h2><p>AIMonのリランカーを使用した後、全てのクエリにおける全体的な品質スコアが向上したことに注目してください。</p>
<p>まとめると、下図に示すように、以下のことが実証されました：</p>
<ul>
<li>幻覚スコア、指示遵守スコア、検索関連性スコアの3つの異なる品質メトリクスの重み付けされた組み合わせを使用した品質スコアの計算。</li>
<li>ブルートフォース（総当たり）文字列マッチング・アプローチを使って、クエリに文書をマッチングさせ、それをLLMに渡すことで、品質ベースラインを確立。</li>
<li>ベクターDB（ここではmilvusを使用）を使ってベースラインの品質を改善。</li>
<li>AIMonの低レイテンシーでドメインに適応可能な再ランカーを使い、品質スコアをさらに向上させた。</li>
<li>また、AIMonのリランカーを追加することで、検索関連性が大幅に向上することも示した。</li>
</ul>
<p><strong>品質スコアを</strong>さらに向上させるために、このノートブックに示したさまざまなコンポーネントを試してみることをお勧めする。1つのアイデアは、上記のinstruction_adherence検出器の<code translate="no">instructions</code> フィールドを使用して、独自の品質定義を追加することです。もう1つのアイデアは、<a href="https://docs.aimon.ai/category/checker-models">AIMonのチェッカーモデルの</a>1つを品質メトリック計算の一部として追加することです。</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">import</span> pandas <span class="hljs-keyword">as</span> pd

df_scores = pd.DataFrame(
    {
        <span class="hljs-string">&quot;Approach&quot;</span>: [<span class="hljs-string">&quot;Brute-Force&quot;</span>, <span class="hljs-string">&quot;VectorDB&quot;</span>, <span class="hljs-string">&quot;AIMon-Rerank&quot;</span>],
        <span class="hljs-string">&quot;Quality Score&quot;</span>: [
            avg_quality_score_bf,
            avg_quality_score_vdb,
            avg_quality_score_rr,
        ],
        <span class="hljs-string">&quot;Retrieval Relevance Score&quot;</span>: [
            avg_retrieval_rel_score_bf,
            avg_retrieval_rel_score_vdb,
            avg_retrieval_rel_score_rr,
        ],
    }
)

<span class="hljs-comment"># % increase of quality scores relative to Brute-Force</span>
df_scores[<span class="hljs-string">&quot;Increase in Quality Score (%)&quot;</span>] = (
    (df_scores[<span class="hljs-string">&quot;Quality Score&quot;</span>] - avg_quality_score_bf) / avg_quality_score_bf
) * <span class="hljs-number">100</span>
df_scores.loc[<span class="hljs-number">0</span>, <span class="hljs-string">&quot;Increase in Quality Score (%)&quot;</span>] = <span class="hljs-number">0</span>

<span class="hljs-comment"># % increase of retrieval relative scores relative to Brute-Force</span>
df_scores[<span class="hljs-string">&quot;Increase in Retrieval Relevance Score (%)&quot;</span>] = (
    (df_scores[<span class="hljs-string">&quot;Retrieval Relevance Score&quot;</span>] - avg_retrieval_rel_score_bf)
    / avg_retrieval_rel_score_bf
) * <span class="hljs-number">100</span>
df_scores.loc[<span class="hljs-number">0</span>, <span class="hljs-string">&quot;Increase in Retrieval Relevance Score (%)&quot;</span>] = <span class="hljs-number">0</span>

df_scores
<button class="copy-code-btn"></button></code></pre>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>アプローチ</th>
      <th>品質スコア</th>
      <th>検索関連性スコア</th>
      <th>品質スコアの増加</th>
      <th>検索関連度スコアの増加(%)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ブルートフォース</td>
      <td>51.750446</td>
      <td>14.317723</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ベクターDB</td>
      <td>67.180039</td>
      <td>19.296728</td>
      <td>29.815382</td>
      <td>34.775115</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AIMon-Rerank</td>
      <td>74.621748</td>
      <td>39.794702</td>
      <td>44.195372</td>
      <td>177.940153</td>
    </tr>
  </tbody>
</table>
<p>上記の表は、私たちの結果をまとめたものです。実際の数値は、LLM回答の品質のばらつき、VectorDBの最近傍探索のパフォーマンスなど、様々な要因によって異なります。</p>
<p>結論として、下図に示すように、LLMアプリケーションの品質スコア、RAG関連性、命令フォロー能力を評価しました。AIMonのリランカーを使用して、アプリケーションの全体的な品質と、お客様のRAGから検索されたドキュメントの平均的な関連性を改善しました。</p>
