---
id: embed-with-openai.md
order: 2
summary: يتكامل Milvus مع نماذج OpenAI عبر فئة OpenAIEmbeddingFunction.
title: OpenAI
---
<h1 id="OpenAI" class="common-anchor-header">OpenAI<button data-href="#OpenAI" class="anchor-icon" translate="no">
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
    </button></h1><p>يتكامل ميلفوس مع نماذج OpenAI عبر فئة <strong>OpenAIEmbeddingFunction</strong>. توفّر هذه الفئة طرقًا لترميز المستندات والاستعلامات باستخدام نماذج OpenAI المُدرّبة مسبقًا وإرجاع التضمينات كمتجهات كثيفة متوافقة مع فهرسة Milvus. للاستفادة من هذه الوظيفة، احصل على مفتاح API من <a href="https://openai.com/api/">OpenAI</a> من خلال إنشاء حساب على منصتهم.</p>
<p>لاستخدام هذه الميزة، قم بتثبيت التبعيات اللازمة:</p>
<pre><code translate="no" class="language-bash">pip install --upgrade pymilvus
pip install <span class="hljs-string">&quot;pymilvus[model]&quot;</span>
<button class="copy-code-btn"></button></code></pre>
<p>ثم، قم بإنشاء <strong>OpenAIEmbeddingFunction</strong>:</p>
<pre><code translate="no" class="language-python"><span class="hljs-keyword">from</span> pymilvus <span class="hljs-keyword">import</span> model

openai_ef = model.dense.OpenAIEmbeddingFunction(
    model_name=<span class="hljs-string">&#x27;text-embedding-3-large&#x27;</span>, <span class="hljs-comment"># Specify the model name</span>
    api_key=<span class="hljs-string">&#x27;YOUR_API_KEY&#x27;</span>, <span class="hljs-comment"># Provide your OpenAI API key</span>
    dimensions=<span class="hljs-number">512</span> <span class="hljs-comment"># Set the embedding dimensionality</span>
)
<button class="copy-code-btn"></button></code></pre>
<p><strong>المعلمات</strong>:</p>
<ul>
<li><p><strong>اسم_النموذج</strong><em>(سلسلة</em>)</p>
<p>اسم نموذج OpenAI المراد استخدامه للترميز. الخيارات الصالحة هي <strong>text-تضمين النص-3-small،</strong> و <strong>text-تضمين النص-3-large،</strong> و <strong>text-تضمين النص-AD-002</strong> (افتراضي).</p></li>
<li><p><strong>مفتاح_API</strong><em>(سلسلة</em>)</p>
<p>مفتاح واجهة برمجة التطبيقات للوصول إلى واجهة برمجة تطبيقات OpenAI.</p></li>
<li><p><strong>الأبعاد</strong><em>(int</em>)</p>
<p>عدد الأبعاد التي يجب أن تحتوي عليها التضمينات الناتجة. مدعوم فقط في <strong>تضمين النص-3</strong> والنماذج الأحدث.</p></li>
</ul>
<p>لإنشاء تضمينات للمستندات، استخدم طريقة <strong>تشفير_المستندات()</strong>:</p>
<pre><code translate="no" class="language-python">docs = [
    <span class="hljs-string">&quot;Artificial intelligence was founded as an academic discipline in 1956.&quot;</span>,
    <span class="hljs-string">&quot;Alan Turing was the first person to conduct substantial research in AI.&quot;</span>,
    <span class="hljs-string">&quot;Born in Maida Vale, London, Turing was raised in southern England.&quot;</span>,
]

docs_embeddings = openai_ef.encode_documents(docs)

<span class="hljs-comment"># Print embeddings</span>
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Embeddings:&quot;</span>, docs_embeddings)
<span class="hljs-comment"># Print dimension and shape of embeddings</span>
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Dim:&quot;</span>, openai_ef.dim, docs_embeddings[<span class="hljs-number">0</span>].shape)
<button class="copy-code-btn"></button></code></pre>
<p>يكون الناتج المتوقع مشابهًا لما يلي:</p>
<pre><code translate="no" class="language-python">Embeddings: [array([ <span class="hljs-number">1.76741909e-02</span>, -<span class="hljs-number">2.04964578e-02</span>, -<span class="hljs-number">1.09788161e-02</span>, -<span class="hljs-number">5.27223349e-02</span>,
        <span class="hljs-number">4.23139781e-02</span>, -<span class="hljs-number">6.64533582e-03</span>,  <span class="hljs-number">4.21088142e-03</span>,  <span class="hljs-number">1.04644023e-01</span>,
        <span class="hljs-number">5.10009527e-02</span>,  <span class="hljs-number">5.32827862e-02</span>, -<span class="hljs-number">3.26061808e-02</span>, -<span class="hljs-number">3.66494283e-02</span>,
...
       -<span class="hljs-number">8.93232748e-02</span>,  <span class="hljs-number">6.68255147e-03</span>,  <span class="hljs-number">3.55093405e-02</span>, -<span class="hljs-number">5.09071983e-02</span>,
        <span class="hljs-number">3.74144339e-03</span>,  <span class="hljs-number">4.72541340e-02</span>,  <span class="hljs-number">2.11916920e-02</span>,  <span class="hljs-number">1.00753829e-02</span>,
       -<span class="hljs-number">5.76633997e-02</span>,  <span class="hljs-number">9.68257990e-03</span>,  <span class="hljs-number">4.62721288e-02</span>, -<span class="hljs-number">4.33261096e-02</span>])]
Dim: <span class="hljs-number">512</span> (<span class="hljs-number">512</span>,)
<button class="copy-code-btn"></button></code></pre>
<p>لإنشاء تضمينات للاستعلامات، استخدم الأسلوب <strong>encode_queries()</strong>:</p>
<pre><code translate="no" class="language-python">queries = [<span class="hljs-string">&quot;When was artificial intelligence founded&quot;</span>, 
           <span class="hljs-string">&quot;Where was Alan Turing born?&quot;</span>]

query_embeddings = openai_ef.encode_queries(queries)

<span class="hljs-comment"># Print embeddings</span>
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Embeddings:&quot;</span>, query_embeddings)
<span class="hljs-comment"># Print dimension and shape of embeddings</span>
<span class="hljs-built_in">print</span>(<span class="hljs-string">&quot;Dim&quot;</span>, openai_ef.dim, query_embeddings[<span class="hljs-number">0</span>].shape)
<button class="copy-code-btn"></button></code></pre>
<p>الناتج المتوقع مشابه لما يلي:</p>
<pre><code translate="no" class="language-python">Embeddings: [array([ <span class="hljs-number">0.00530251</span>, -<span class="hljs-number">0.01907905</span>, -<span class="hljs-number">0.01672608</span>, -<span class="hljs-number">0.05030033</span>,  <span class="hljs-number">0.01635982</span>,
       -<span class="hljs-number">0.03169853</span>, -<span class="hljs-number">0.0033602</span> ,  <span class="hljs-number">0.09047844</span>,  <span class="hljs-number">0.00030747</span>,  <span class="hljs-number">0.11853652</span>,
       -<span class="hljs-number">0.02870182</span>, -<span class="hljs-number">0.01526102</span>,  <span class="hljs-number">0.05505067</span>,  <span class="hljs-number">0.00993909</span>, -<span class="hljs-number">0.07165466</span>,
...
       -<span class="hljs-number">9.78106782e-02</span>, -<span class="hljs-number">2.22669560e-02</span>,  <span class="hljs-number">1.21873049e-02</span>, -<span class="hljs-number">4.83198799e-02</span>,
        <span class="hljs-number">5.32377362e-02</span>, -<span class="hljs-number">1.90469325e-02</span>,  <span class="hljs-number">5.62430918e-02</span>,  <span class="hljs-number">1.02650477e-02</span>,
       -<span class="hljs-number">6.21757433e-02</span>,  <span class="hljs-number">7.88027793e-02</span>,  <span class="hljs-number">4.91846527e-04</span>, -<span class="hljs-number">1.51633881e-02</span>])]
Dim <span class="hljs-number">512</span> (<span class="hljs-number">512</span>,)
<button class="copy-code-btn"></button></code></pre>
