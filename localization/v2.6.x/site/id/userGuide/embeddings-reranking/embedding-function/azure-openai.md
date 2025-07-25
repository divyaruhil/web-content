---
id: azure-openai.md
title: Azure OpenAICompatible with Milvus 2.6.x
summary: >-
  Topik ini menjelaskan cara mengonfigurasi dan menggunakan fungsi-fungsi
  penyematan Azure OpenAI di Milvus.
beta: Milvus 2.6.x
---
<h1 id="Azure-OpenAI" class="common-anchor-header">Azure OpenAI<span class="beta-tag" style="background-color:rgb(0, 179, 255);color:white" translate="no">Compatible with Milvus 2.6.x</span><button data-href="#Azure-OpenAI" class="anchor-icon" translate="no">
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
    </button></h1><p>Topik ini menjelaskan cara mengonfigurasi dan menggunakan fungsi-fungsi penyematan Azure OpenAI di Milvus.</p>
<h2 id="Choose-an-embedding-model" class="common-anchor-header">Memilih model penyematan<button data-href="#Choose-an-embedding-model" class="anchor-icon" translate="no">
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
    </button></h2><p>Milvus mendukung semua model penyematan yang disediakan oleh Azure OpenAI. Di bawah ini adalah model-model embedding Azure OpenAI yang tersedia saat ini untuk referensi cepat:</p>
<table>
   <tr>
     <th><p>Model</p></th>
     <th><p>Dimensi</p></th>
     <th><p>Token Maks</p></th>
     <th><p>Deskripsi</p></th>
   </tr>
   <tr>
     <td><p>penyematan-teks-3-kecil</p></td>
     <td><p>Standar: 1.536 (dapat dipotong ke ukuran dimensi di bawah 1536)</p></td>
     <td><p>8,191</p></td>
     <td><p>Ideal untuk pencarian semantik yang sensitif terhadap biaya dan dapat diskalakan-menawarkan kinerja yang kuat dengan harga yang lebih rendah.</p></td>
   </tr>
   <tr>
     <td><p>penyematan teks-3-besar</p></td>
     <td><p>Standar: 3.072 (dapat dipotong ke ukuran dimensi di bawah 3072)</p></td>
     <td><p>8,191</p></td>
     <td><p>Terbaik untuk aplikasi yang menuntut akurasi pengambilan yang lebih baik dan representasi semantik yang lebih kaya.</p></td>
   </tr>
   <tr>
     <td><p>penyematan teks-ada-002</p></td>
     <td><p>Diperbaiki: 1.536 (tidak mendukung pemotongan)</p></td>
     <td><p>8,191</p></td>
     <td><p>Model generasi sebelumnya yang cocok untuk pipeline lama atau skenario yang membutuhkan kompatibilitas ke belakang.</p></td>
   </tr>
</table>
<p>Model penyematan generasi ketiga<strong>(text-embedding-3</strong>) mendukung pengurangan ukuran penyematan melalui parameter <code translate="no">dim</code>. Biasanya embedding yang lebih besar lebih mahal dari perspektif komputasi, memori, dan penyimpanan. Kemampuan untuk menyesuaikan jumlah dimensi memungkinkan kontrol yang lebih besar atas biaya dan kinerja secara keseluruhan. Untuk detail lebih lanjut tentang setiap model, lihat <a href="https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?tabs=global-standard,standard-chat-completions#embeddings">Penyematan</a>.</p>
<h2 id="Configure-credentials" class="common-anchor-header">Mengonfigurasi kredensial<button data-href="#Configure-credentials" class="anchor-icon" translate="no">
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
    </button></h2><p>Milvus harus mengetahui kunci API Azure OpenAI Anda sebelum dapat meminta penyematan. Milvus menyediakan dua metode untuk mengonfigurasi kredensial:</p>
<ul>
<li><p><strong>File konfigurasi (disarankan):</strong> Simpan kunci API di <code translate="no">milvus.yaml</code> sehingga setiap restart dan node mengambilnya secara otomatis.</p></li>
<li><p><strong>Variabel lingkungan:</strong> Menyuntikkan kunci pada waktu penerapan-ideal untuk Docker Compose.</p></li>
</ul>
<p>Pilih salah satu dari dua metode di bawah ini-file konfigurasi lebih mudah dikelola pada bare-metal dan VM, sedangkan rute env-var sesuai dengan alur kerja kontainer.</p>
<div class="alert note">
<p>Jika kunci API untuk penyedia yang sama ada di berkas konfigurasi dan variabel lingkungan, Milvus selalu menggunakan nilai di <code translate="no">milvus.yaml</code> dan mengabaikan variabel lingkungan.</p>
</div>
<h3 id="Option-1-Configuration-file-recommended--higher-priority" class="common-anchor-header">Opsi 1: Berkas konfigurasi (disarankan &amp; prioritas lebih tinggi)</h3><p>Simpan kunci API Anda di <code translate="no">milvus.yaml</code>; Milvus membacanya pada saat startup dan mengesampingkan variabel lingkungan apa pun untuk penyedia yang sama.</p>
<ol>
<li><p>**Deklarasikan kunci Anda di bawah <code translate="no">credential:</code></p>
<p>Anda dapat mendaftarkan satu atau banyak kunci API-beri label yang Anda ciptakan dan akan direferensikan nanti.</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-comment"># milvus.yaml</span>
<span class="hljs-attr">credential:</span>
  <span class="hljs-attr">apikey_dev:</span>            <span class="hljs-comment"># dev environment</span>
    <span class="hljs-attr">apikey:</span> <span class="hljs-string">&lt;YOUR_DEV_KEY&gt;</span>
  <span class="hljs-attr">apikey_prod:</span>           <span class="hljs-comment"># production environment</span>
    <span class="hljs-attr">apikey:</span> <span class="hljs-string">&lt;YOUR_PROD_KEY&gt;</span>    
<button class="copy-code-btn"></button></code></pre>
<p>Menempatkan kunci API di sini akan membuatnya tetap ada di seluruh proses restart dan memungkinkan Anda mengganti kunci hanya dengan mengubah label.</p></li>
<li><p><strong>Beri tahu Milvus kunci mana yang akan digunakan untuk panggilan Azure OpenAI</strong></p>
<p>Di file yang sama, arahkan penyedia Azure OpenAI ke label yang Anda inginkan untuk digunakan.</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-attr">function:</span>
  <span class="hljs-attr">textEmbedding:</span>
    <span class="hljs-attr">providers:</span>
      <span class="hljs-attr">azure_openai:</span>
        <span class="hljs-attr">credential:</span> <span class="hljs-string">apikey_dev</span>      <span class="hljs-comment"># ← choose any label you defined above</span>
        <span class="hljs-attr">resource_name:</span>  <span class="hljs-comment"># Your azure openai resource name</span>
        <span class="hljs-comment"># url: # Your azure openai embedding url</span>
<button class="copy-code-btn"></button></code></pre>
<p>Ini akan mengikat kunci tertentu untuk setiap permintaan yang dikirimkan Milvus ke titik akhir penyematan Azure OpenAI.</p></li>
</ol>
<h3 id="Option-2-Environment-variables" class="common-anchor-header">Opsi 2: Variabel lingkungan</h3><p>Gunakan metode ini ketika Anda menjalankan Milvus dengan Docker Compose dan lebih memilih untuk menyimpan rahasia dari berkas dan gambar.</p>
<p>Milvus akan kembali ke variabel lingkungan hanya jika tidak ada kunci untuk penyedia yang ditemukan di <code translate="no">milvus.yaml</code>.</p>
<table>
   <tr>
     <th><p>Variabel</p></th>
     <th><p>Diperlukan</p></th>
     <th><p>Deskripsi</p></th>
   </tr>
   <tr>
     <td><p><code translate="no">MILVUSAI_AZURE_OPENAI_API_KEY</code></p></td>
     <td><p>Ya</p></td>
     <td><p>Membuat kunci Azure OpenAI tersedia di dalam setiap kontainer Milvus <em>(diabaikan jika kunci untuk Azure OpenAI ada di <code translate="no">milvus.yaml</code></em> )</p></td>
   </tr>
   <tr>
     <td><p><code translate="no">MILVUSAI_AZURE_OPENAI_RESOURCE_NAME</code></p></td>
     <td><p>Ya</p></td>
     <td><p>Nama sumber daya Azure OpenAI Anda seperti yang ditetapkan saat Anda membuat sumber daya layanan Azure OpenAI.</p></td>
   </tr>
</table>
<p>Dalam berkas <strong>docker-compose.yaml</strong> Anda, atur variabel lingkungan.</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-comment"># docker-compose.yaml (standalone service section)</span>
<span class="hljs-attr">standalone:</span>
  <span class="hljs-comment"># ... other configurations ...</span>
  <span class="hljs-attr">environment:</span>
    <span class="hljs-comment"># ... other environment variables ...</span>
    <span class="hljs-comment"># Set the environment variable pointing to the Azure OpenAI API key inside the container</span>
    <span class="hljs-attr">MILVUSAI_AZURE_OPENAI_API_KEY:</span> <span class="hljs-string">&lt;MILVUSAI_AZURE_OPENAI_API_KEY&gt;</span>
    <span class="hljs-attr">MILVUSAI_AZURE_OPENAI_RESOURCE_NAME:</span> <span class="hljs-string">&lt;MILVUSAI_AZURE_OPENAI_RESOURCE_NAME&gt;</span>
<button class="copy-code-btn"></button></code></pre>
<p>Blok <code translate="no">environment:</code> hanya menyuntikkan kunci ke dalam kontainer Milvus, membiarkan OS host Anda tidak tersentuh. Untuk detailnya, lihat <a href="/docs/id/configure-docker.md#Configure-Milvus-with-Docker-Compose">Mengkonfigurasi Milvus dengan Docker Compose</a>.</p>
<h2 id="Use-embedding-function" class="common-anchor-header">Menggunakan fungsi penyematan<button data-href="#Use-embedding-function" class="anchor-icon" translate="no">
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
    </button></h2><p>Setelah kredensial dikonfigurasi, ikuti langkah-langkah berikut untuk mendefinisikan dan menggunakan fungsi penyematan.</p>
<h3 id="Step-1-Define-schema-fields" class="common-anchor-header">Langkah 1: Mendefinisikan bidang skema</h3><p>Untuk menggunakan fungsi penyematan, buat koleksi dengan skema tertentu. Skema ini harus menyertakan setidaknya tiga bidang yang diperlukan:</p>
<ul>
<li><p>Bidang utama yang secara unik mengidentifikasi setiap entitas dalam koleksi.</p></li>
<li><p>Bidang skalar yang menyimpan data mentah yang akan disematkan.</p></li>
<li><p>Bidang vektor yang dicadangkan untuk menyimpan penyematan vektor yang akan dihasilkan oleh fungsi untuk bidang skalar.</p></li>
</ul>
<p>Contoh berikut ini mendefinisikan skema dengan satu bidang skalar <code translate="no">&quot;document&quot;</code> untuk menyimpan data tekstual dan satu bidang vektor <code translate="no">&quot;dense&quot;</code> untuk menyimpan embedding yang akan dihasilkan oleh modul Function. Ingatlah untuk mengatur dimensi vektor (<code translate="no">dim</code>) agar sesuai dengan output dari model penyematan yang Anda pilih.</p>
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
schema.add_field(<span class="hljs-string">&quot;dense&quot;</span>, DataType.FLOAT_VECTOR, dim=<span class="hljs-number">1536</span>)
<button class="copy-code-btn"></button></code></pre>
<h3 id="Step-2-Add-embedding-function-to-schema" class="common-anchor-header">Langkah 2: Menambahkan fungsi penyematan ke skema</h3><p>Setelah Anda mendefinisikan fungsi penyematan Anda, tambahkan ke skema koleksi Anda. Ini menginstruksikan Milvus untuk menggunakan fungsi penyematan yang ditentukan untuk memproses dan menyimpan penyematan dari data teks Anda.</p>
<pre><code translate="no" class="language-python"><span class="hljs-comment"># Define embedding function specifically for Azure OpenAI provider</span>
text_embedding_function = Function(
    name=<span class="hljs-string">&quot;azopenai&quot;</span>,                                <span class="hljs-comment"># Unique identifier for this embedding function</span>
    function_type=FunctionType.TEXTEMBEDDING,       <span class="hljs-comment"># Indicates a text embedding function</span>
    input_field_names=[<span class="hljs-string">&quot;document&quot;</span>],                 <span class="hljs-comment"># Scalar field(s) containing text data to embed</span>
    output_field_names=[<span class="hljs-string">&quot;dense&quot;</span>],                   <span class="hljs-comment"># Vector field(s) for storing embeddings</span>
    params={                                        <span class="hljs-comment"># Provider-specific embedding parameters</span>
        <span class="hljs-string">&quot;provider&quot;</span>: <span class="hljs-string">&quot;azure_openai&quot;</span>,                 <span class="hljs-comment"># Embedding provider name (must be &quot;azure_openai&quot;)</span>
        <span class="hljs-string">&quot;model_name&quot;</span>: <span class="hljs-string">&quot;zilliz-text-embedding-3-small&quot;</span>,      <span class="hljs-comment"># Model should be set to the deployment name you chose when you deployed the embedding model</span>
        <span class="hljs-comment"># Optional parameters (only specify if necessary):</span>
        <span class="hljs-comment"># &quot;url&quot;: &quot;https://{resource_name}.openai.azure.com/&quot; # Optional: Your Azure OpenAI service endpoint</span>
        <span class="hljs-comment"># &quot;credential&quot;: &quot;apikey_dev&quot;,               # Optional: Credential label specified in milvus.yaml</span>
        <span class="hljs-comment"># &quot;dim&quot;: &quot;1536&quot;,                            # Optional: Shorten the output vector dimension</span>
        <span class="hljs-comment"># &quot;user&quot;: &quot;user123&quot;,                        # Optional: identifier for API tracking</span>
    }
)

<span class="hljs-comment"># Add the configured embedding function to your existing collection schema</span>
schema.add_function(text_embedding_function)
<button class="copy-code-btn"></button></code></pre>
<h2 id="Next-steps" class="common-anchor-header">Langkah selanjutnya<button data-href="#Next-steps" class="anchor-icon" translate="no">
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
    </button></h2><p>Setelah mengonfigurasi fungsi penyematan, lihat <a href="/docs/id/embedding-function-overview.md">Ikhtisar Fungsi</a> untuk panduan tambahan mengenai konfigurasi indeks, contoh penyisipan data, dan operasi pencarian semantik.</p>
