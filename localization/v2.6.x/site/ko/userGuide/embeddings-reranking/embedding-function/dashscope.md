---
id: dashscope.md
title: 대시스코프Compatible with Milvus 2.6.x
summary: 이 항목에서는 Milvus에서 DashScope 임베딩 기능을 구성하고 사용하는 방법을 설명합니다.
beta: Milvus 2.6.x
---
<h1 id="DashScope" class="common-anchor-header">대시스코프<span class="beta-tag" style="background-color:rgb(0, 179, 255);color:white" translate="no">Compatible with Milvus 2.6.x</span><button data-href="#DashScope" class="anchor-icon" translate="no">
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
    </button></h1><p>이 항목에서는 Milvus에서 DashScope 임베딩 기능을 구성하고 사용하는 방법에 대해 설명합니다.</p>
<h2 id="Choose-an-embedding-model" class="common-anchor-header">임베딩 모델 선택<button data-href="#Choose-an-embedding-model" class="anchor-icon" translate="no">
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
    </button></h2><p>다음은 현재 사용 가능한 DashScope 임베딩 모델을 빠르게 참조할 수 있습니다:</p>
<table>
   <tr>
     <th><p>모델 이름</p></th>
     <th><p>크기</p></th>
     <th><p>행당 최대 토큰 수</p></th>
     <th><p>지원 언어</p></th>
   </tr>
   <tr>
     <td><p>텍스트 임베딩-V3</p></td>
     <td><p>1,024(기본값), 768 또는 512</p></td>
     <td><p>8,192</p></td>
     <td><p>중국어, 영어, 스페인어, 프랑스어, 포르투갈어, 인도네시아어, 일본어, 한국어, 독일어, 러시아어 및 기타 50개 이상의 언어</p></td>
   </tr>
   <tr>
     <td><p>텍스트 임베딩-v2</p></td>
     <td><p>1,536</p></td>
     <td><p>2,048</p></td>
     <td><p>한국어, 영어, 스페인어, 프랑스어, 포르투갈어, 인도네시아어, 일본어, 한국어, 독일어, 러시아어, 중국어, 영어, 스페인어, 포르투갈어, 인도네시아어, 일본어</p></td>
   </tr>
   <tr>
     <td><p>텍스트 임베딩-v1</p></td>
     <td><p>1,536</p></td>
     <td><p>2,048</p></td>
     <td><p>중국어, 영어, 스페인어, 프랑스어, 포르투갈어, 인도네시아어, 일본어, 한국어, 독일어, 러시아어, 터키어</p></td>
   </tr>
</table>
<p>임베딩 모델 <strong>텍스트 임베딩-v3는</strong> <code translate="no">dim</code> 파라미터를 통해 임베딩 크기 축소를 지원합니다. 일반적으로 임베딩 크기가 클수록 컴퓨팅, 메모리, 스토리지 측면에서 비용이 더 많이 듭니다. 치수 수를 조정할 수 있으면 전체 비용과 성능을 더 잘 제어할 수 있습니다. 각 모델에 대한 자세한 내용은 <a href="https://help.aliyun.com/zh/model-studio/user-guide/embedding?disableWebsiteRedirect=true">임베딩을</a> 참조하세요.</p>
<h2 id="Configure-credentials" class="common-anchor-header">자격 증명 구성<button data-href="#Configure-credentials" class="anchor-icon" translate="no">
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
    </button></h2><p>Milvus는 임베딩을 요청하기 전에 DashScope API 키를 알고 있어야 합니다. Milvus는 자격 증명을 구성하는 두 가지 방법을 제공합니다:</p>
<ul>
<li><p><strong>구성 파일(권장):</strong> API 키를 <code translate="no">milvus.yaml</code> 에 저장하여 재시작할 때마다 노드가 자동으로 가져옵니다.</p></li>
<li><p><strong>환경 변수:</strong> 배포 시점에 키를 주입하는 방법(Docker Compose에 이상적).</p></li>
</ul>
<p>아래 두 가지 방법 중 하나를 선택하세요. 구성 파일은 베어메탈 및 가상 머신에서 유지 관리가 더 쉬운 반면, 환경 변수 경로는 컨테이너 워크플로우에 적합합니다.</p>
<div class="alert note">
<p>동일한 공급자에 대한 API 키가 구성 파일과 환경 변수 모두에 있는 경우, Milvus는 항상 <code translate="no">milvus.yaml</code> 의 값을 사용하고 환경 변수는 무시합니다.</p>
</div>
<h3 id="Option-1-Configuration-file-recommended--higher-priority" class="common-anchor-header">옵션 1: 구성 파일(권장 및 우선순위 높음)</h3><p>API 키를 <code translate="no">milvus.yaml</code>; Milvus는 시작 시 키를 읽고 동일한 공급자에 대한 모든 환경 변수를 재정의합니다.</p>
<ol>
<li><p>**아래에 키를 선언하세요. <code translate="no">credential:</code></p>
<p>하나 또는 여러 개의 API 키를 나열할 수 있으며, 각 키에 나중에 참조할 레이블을 지정할 수 있습니다.</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-comment"># milvus.yaml</span>
<span class="hljs-attr">credential:</span>
  <span class="hljs-attr">apikey_dev:</span>            <span class="hljs-comment"># dev environment</span>
    <span class="hljs-attr">apikey:</span> <span class="hljs-string">&lt;YOUR_DEV_KEY&gt;</span>
  <span class="hljs-attr">apikey_prod:</span>           <span class="hljs-comment"># production environment</span>
    <span class="hljs-attr">apikey:</span> <span class="hljs-string">&lt;YOUR_PROD_KEY&gt;</span>    
<button class="copy-code-btn"></button></code></pre>
<p>여기에 API 키를 넣으면 재시작 시에도 지속되며 레이블을 변경하는 것만으로 키를 전환할 수 있습니다.</p></li>
<li><p><strong>대시스코프 호출에 사용할 키를 Milvus에 알려주기</strong></p>
<p>동일한 파일에서 DashScope 공급자가 사용할 레이블을 가리킵니다.</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-attr">function:</span>
  <span class="hljs-attr">textEmbedding:</span>
    <span class="hljs-attr">providers:</span>
      <span class="hljs-attr">dashscope:</span>
        <span class="hljs-attr">credential:</span> <span class="hljs-string">apikey_dev</span>      <span class="hljs-comment"># ← choose any label you defined above</span>
        <span class="hljs-comment"># url: https://dashscope-intl.aliyuncs.com/compatible-mode/v1   # (optional) custom endpoint</span>
<button class="copy-code-btn"></button></code></pre>
<p>이렇게 하면 Milvus가 DashScope 임베딩 엔드포인트로 보내는 모든 요청에 특정 키가 바인딩됩니다.</p></li>
</ol>
<h3 id="Option-2-Environment-variable" class="common-anchor-header">옵션 2: 환경 변수</h3><p>Docker Compose와 함께 Milvus를 실행하고 파일과 이미지에서 비밀을 유지하려는 경우 이 방법을 사용합니다.</p>
<p>Milvus는 <code translate="no">milvus.yaml</code> 에서 공급자에 대한 키를 찾을 수 없는 경우에만 환경 변수로 되돌아갑니다.</p>
<table>
   <tr>
     <th><p>변수</p></th>
     <th><p>필수</p></th>
     <th><p>설명</p></th>
   </tr>
   <tr>
     <td><p><code translate="no">MILVUSAI_DASHSCOPE_API_KEY</code></p></td>
     <td><p>Yes</p></td>
     <td><p>각 Milvus 컨테이너 내에서 DashScope 키를 사용할 수 있게 <em> 합니다( <code translate="no">milvus.yaml</code> 에 DashScope용 키가 있는 경우 무시됨</em>).</p></td>
   </tr>
</table>
<p><strong>docker-compose.yaml</strong> 파일에서 <code translate="no">MILVUSAI_DASHSCOPE_API_KEY</code> 환경 변수를 설정합니다.</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-comment"># docker-compose.yaml (standalone service section)</span>
<span class="hljs-attr">standalone:</span>
  <span class="hljs-comment"># ... other configurations ...</span>
  <span class="hljs-attr">environment:</span>
    <span class="hljs-comment"># ... other environment variables ...</span>
    <span class="hljs-comment"># Set the environment variable pointing to the DashScope API key inside the container</span>
    <span class="hljs-attr">MILVUSAI_DASHSCOPE_API_KEY:</span> <span class="hljs-string">&lt;MILVUSAI_DASHSCOPE_API_KEY&gt;</span>
<button class="copy-code-btn"></button></code></pre>
<p><code translate="no">environment:</code> 블록은 호스트 OS는 그대로 두고 Milvus 컨테이너에만 키를 삽입합니다. 자세한 내용은 <a href="/docs/ko/configure-docker.md#Configure-Milvus-with-Docker-Compose">Docker Compose로 Milvus 구성을</a> 참조하세요.</p>
<h2 id="Use-embedding-function" class="common-anchor-header">임베딩 기능 사용<button data-href="#Use-embedding-function" class="anchor-icon" translate="no">
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
    </button></h2><p>자격 증명이 구성되면 다음 단계에 따라 임베딩 함수를 정의하고 사용하세요.</p>
<h3 id="Step-1-Define-schema-fields" class="common-anchor-header">1단계: 스키마 필드 정의</h3><p>임베딩 함수를 사용하려면 특정 스키마로 컬렉션을 만듭니다. 이 스키마에는 최소 3개의 필수 필드가 포함되어야 합니다:</p>
<ul>
<li><p>컬렉션의 각 엔티티를 고유하게 식별하는 기본 필드.</p></li>
<li><p>임베드할 원시 데이터를 저장하는 스칼라 필드.</p></li>
<li><p>함수가 스칼라 필드에 대해 생성할 벡터 임베딩을 저장하기 위해 예약된 벡터 필드.</p></li>
</ul>
<p>다음 예에서는 텍스트 데이터를 저장하는 스칼라 필드( <code translate="no">&quot;document&quot;</code> ) 1개와 함수 모듈에서 생성할 임베딩을 저장하는 벡터 필드( <code translate="no">&quot;dense&quot;</code> ) 1개가 있는 스키마를 정의합니다. 선택한 임베딩 모델의 출력과 일치하도록 벡터 차원(<code translate="no">dim</code>)을 설정하는 것을 잊지 마세요.</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> pymilvus <span class="hljs-keyword">import</span> MilvusClient, DataType, Function, FunctionType

<span class="hljs-comment"># Initialize Milvus client</span>
client = MilvusClient(
    uri=<span class="hljs-string">&quot;http://localhost:19530&quot;</span>,
)

<span class="hljs-comment"># Create a new schema for the collection</span>
schema = client.create_schema()

<span class="hljs-comment"># Add primary field &quot;id&quot;</span>
schema.add_field(<span class="hljs-string">&quot;id&quot;</span>, DataType.INT64, is_primary=<span class="hljs-literal">True</span>, auto_id=<span class="hljs-literal">False</span>)

<span class="hljs-comment"># Add scalar field &quot;document&quot; for storing textual data</span>
schema.add_field(<span class="hljs-string">&quot;document&quot;</span>, DataType.VARCHAR, max_length=<span class="hljs-number">9000</span>)

<span class="hljs-comment"># Add vector field &quot;dense&quot; for storing embeddings.</span>
<span class="hljs-comment"># IMPORTANT: Set dim to match the exact output dimension of the embedding model.</span>
schema.add_field(<span class="hljs-string">&quot;dense&quot;</span>, DataType.FLOAT_VECTOR, dim=<span class="hljs-number">1024</span>)
<button class="copy-code-btn"></button></code></pre>
<h3 id="Step-2-Add-embedding-function-to-schema" class="common-anchor-header">2단계: 스키마에 임베딩 함수 추가하기</h3><p>Milvus의 함수 모듈은 스칼라 필드에 저장된 원시 데이터를 임베딩으로 자동 변환하여 명시적으로 정의된 벡터 필드에 저장합니다.</p>
<p>아래 예는 스칼라 필드 <code translate="no">&quot;document&quot;</code> 를 임베딩으로 변환하여 결과 벡터를 앞서 정의한 <code translate="no">&quot;dense&quot;</code> 벡터 필드에 저장하는 함수 모듈(<code translate="no">ali</code>)을 추가한 것입니다.</p>
<p>임베딩 함수를 정의한 후에는 컬렉션 스키마에 추가합니다. 이렇게 하면 Milvus가 지정된 임베딩 함수를 사용하여 텍스트 데이터의 임베딩을 처리하고 저장하도록 지시합니다.</p>
<pre><code translate="no" class="language-python">
<span class="hljs-comment"># Define embedding function specifically for model provider</span>
text_embedding_function = Function(
    name=<span class="hljs-string">&quot;ali&quot;</span>,                                     <span class="hljs-comment"># Unique identifier for this embedding function</span>
    function_type=FunctionType.TEXTEMBEDDING,       <span class="hljs-comment"># Indicates a text embedding function</span>
    input_field_names=[<span class="hljs-string">&quot;document&quot;</span>],                 <span class="hljs-comment"># Scalar field(s) containing text data to embed</span>
    output_field_names=[<span class="hljs-string">&quot;dense&quot;</span>],                   <span class="hljs-comment"># Vector field(s) for storing embeddings</span>
    params={                                        <span class="hljs-comment"># Provider-specific embedding parameters</span>
        <span class="hljs-string">&quot;provider&quot;</span>: <span class="hljs-string">&quot;dashscope&quot;</span>,                    <span class="hljs-comment"># Embedding provider name (must be &quot;dashscope&quot;)</span>
        <span class="hljs-string">&quot;model_name&quot;</span>: <span class="hljs-string">&quot;text-embedding-v3&quot;</span>,          <span class="hljs-comment"># Specific embedding model used</span>
        <span class="hljs-comment"># Optional parameters:</span>
        <span class="hljs-comment"># &quot;credential&quot;: &quot;apikey_dev&quot;                # Optional: Credential label specified in milvus.yaml</span>
        <span class="hljs-comment"># &quot;dim&quot;: &quot;1024&quot;,                            # Optional: Shorten the output vector dimension</span>
    }
)

<span class="hljs-comment"># Add the configured embedding function to your existing collection schema</span>
schema.add_function(text_embedding_function)
<button class="copy-code-btn"></button></code></pre>
<h2 id="Next-steps" class="common-anchor-header">다음 단계<button data-href="#Next-steps" class="anchor-icon" translate="no">
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
    </button></h2><p>임베딩 함수를 구성한 후에는 <a href="/docs/ko/embedding-function-overview.md">함수 개요에서</a> 인덱스 구성, 데이터 삽입 예제 및 시맨틱 검색 작업에 대한 추가 지침을 참조하세요.</p>
