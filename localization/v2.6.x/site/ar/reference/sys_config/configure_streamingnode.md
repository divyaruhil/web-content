---
id: configure_streamingnode.md
related_key: configure
group: system_configuration.md
summary: تعرف على كيفية تهيئة StreamingNode لـ Milvus.
---
<h1 id="streamingNode-related-Configurations" class="common-anchor-header">التكوينات المتعلقة بعقدة التدفق<button data-href="#streamingNode-related-Configurations" class="anchor-icon" translate="no">
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
    </button></h1><p>أي تكوين متعلق بخادم عقدة التدفق.</p>
<h2 id="streamingNodeip" class="common-anchor-header"><code translate="no">streamingNode.ip</code><button data-href="#streamingNodeip" class="anchor-icon" translate="no">
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
    </button></h2><table id="streamingNode.ip">
  <thead>
    <tr>
      <th class="width80">الوصف</th>
      <th class="width20">القيمة الافتراضية</th> 
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>        عنوان TCP/IP لعقدة البث. في حالة عدم تحديده، استخدم أول عنوان غير قابل للإرسال      </td>
      <td></td>
    </tr>
  </tbody>
</table>
<h2 id="streamingNodeport" class="common-anchor-header"><code translate="no">streamingNode.port</code><button data-href="#streamingNodeport" class="anchor-icon" translate="no">
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
    </button></h2><table id="streamingNode.port">
  <thead>
    <tr>
      <th class="width80">الوصف</th>
      <th class="width20">القيمة الافتراضية</th> 
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>        منفذ TCP لعقدة البث      </td>
      <td>22222</td>
    </tr>
  </tbody>
</table>
<h2 id="streamingNodegrpcserverMaxSendSize" class="common-anchor-header"><code translate="no">streamingNode.grpc.serverMaxSendSize</code><button data-href="#streamingNodegrpcserverMaxSendSize" class="anchor-icon" translate="no">
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
    </button></h2><table id="streamingNode.grpc.serverMaxSendSize">
  <thead>
    <tr>
      <th class="width80">الوصف</th>
      <th class="width20">القيمة الافتراضية</th> 
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>        الحد الأقصى لحجم كل طلب RPC الذي يمكن أن ترسله عقدة الدفق، الوحدة: بايت      </td>
      <td>268435456</td>
    </tr>
  </tbody>
</table>
<h2 id="streamingNodegrpcserverMaxRecvSize" class="common-anchor-header"><code translate="no">streamingNode.grpc.serverMaxRecvSize</code><button data-href="#streamingNodegrpcserverMaxRecvSize" class="anchor-icon" translate="no">
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
    </button></h2><table id="streamingNode.grpc.serverMaxRecvSize">
  <thead>
    <tr>
      <th class="width80">الوصف</th>
      <th class="width20">القيمة الافتراضية</th> 
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>        الحد الأقصى لحجم كل طلب طلب استدعاء أوامر الشراء عن بُعد (RPC) الذي يمكن لعقدة الدفق استقباله، الوحدة: بايت      </td>
      <td>268435456</td>
    </tr>
  </tbody>
</table>
<h2 id="streamingNodegrpcclientMaxSendSize" class="common-anchor-header"><code translate="no">streamingNode.grpc.clientMaxSendSize</code><button data-href="#streamingNodegrpcclientMaxSendSize" class="anchor-icon" translate="no">
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
    </button></h2><table id="streamingNode.grpc.clientMaxSendSize">
  <thead>
    <tr>
      <th class="width80">الوصف</th>
      <th class="width20">القيمة الافتراضية</th> 
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>        الحد الأقصى لحجم كل طلب طلب استدعاء أوامر الشراء (RPC) الذي يمكن للعملاء على عقدة الدفق، الوحدة: بايت      </td>
      <td>268435456</td>
    </tr>
  </tbody>
</table>
<h2 id="streamingNodegrpcclientMaxRecvSize" class="common-anchor-header"><code translate="no">streamingNode.grpc.clientMaxRecvSize</code><button data-href="#streamingNodegrpcclientMaxRecvSize" class="anchor-icon" translate="no">
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
    </button></h2><table id="streamingNode.grpc.clientMaxRecvSize">
  <thead>
    <tr>
      <th class="width80">الوصف</th>
      <th class="width20">القيمة الافتراضية</th> 
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>        الحد الأقصى لحجم كل طلب طلب استدعاء أوامر الشراء (RPC) الذي يمكن للعملاء على عقدة الدفق، الوحدة: بايت      </td>
      <td>268435456</td>
    </tr>
  </tbody>
</table>
