---
id: configure-querynode-localdisk.md
title: تكوين Milvus QueryNode مع القرص المحلي
related_key: 'querynode, query node, local disk'
summary: تعرف على كيفية تهيئة Milvus QueryNode لاستخدام القرص المحلي.
---
<h1 id="Configure-Milvus-QueryNode-with-Local-Disk" class="common-anchor-header">تكوين Milvus QueryNode مع القرص المحلي<button data-href="#Configure-Milvus-QueryNode-with-Local-Disk" class="anchor-icon" translate="no">
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
    </button></h1><p>توضح هذه المقالة كيفية تكوين Milvus QueryNode Milvus QueryNode لاستخدام تخزين القرص المحلي.</p>
<h2 id="Overview" class="common-anchor-header">نظرة عامة<button data-href="#Overview" class="anchor-icon" translate="no">
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
    </button></h2><p>Milvus هي قاعدة بيانات متجهة تركز على الذكاء الاصطناعي مصممة خصيصًا لتخزين واسترجاع كميات هائلة من البيانات المتجهة بكفاءة. وهي مثالية لمهام مثل تحليل الصور والفيديو ومعالجة اللغة الطبيعية وأنظمة التوصيات. لضمان الأداء الأمثل، من الضروري تقليل زمن انتقال القراءة على القرص إلى الحد الأدنى. يوصى بشدة باستخدام محركات أقراص NVMe SSD المحلية لمنع التأخير والحفاظ على استقرار النظام.</p>
<p>تتضمن الميزات الرئيسية التي يتم فيها تشغيل تخزين القرص المحلي ما يلي:</p>
<ul>
<li><a href="/docs/ar/chunk_cache.md"><strong>ذاكرة التخزين المؤقت للقطع</strong></a>: التحميل المسبق للبيانات في ذاكرة التخزين المؤقت للقرص المحلي للبحث بشكل أسرع.</li>
<li><a href="/docs/ar/mmap.md"><strong>MMap</strong></a>: تعيين محتويات الملف مباشرة في الذاكرة لتحسين كفاءة الذاكرة.</li>
<li><a href="/docs/ar/disk_index.md"><strong>فهرس DiskANN</strong></a>: يتطلب تخزين القرص لإدارة الفهرس بكفاءة.</li>
</ul>
<p>في هذه المقالة، سنركز على نشر <a href="/docs/ar/install-overview.md#Milvus-Distributed">Milvus Distributed</a> على المنصات السحابية، وكيفية تكوين QueryNode لاستخدام تخزين القرص NVMe. يسرد الجدول التالي أنواع الأجهزة الموصى بها لمختلف موفري السحابة.</p>
<table>
<thead>
<tr><th style="text-align:center">مزود السحابة</th><th style="text-align:center">نوع الجهاز</th></tr>
</thead>
<tbody>
<tr><td style="text-align:center">AWS</td><td style="text-align:center">سلسلة R6id</td></tr>
<tr><td style="text-align:center">GCP</td><td style="text-align:center">سلسلة N2</td></tr>
<tr><td style="text-align:center">أزور</td><td style="text-align:center">سلسلة Lsv3</td></tr>
<tr><td style="text-align:center">علي بابا كلاود</td><td style="text-align:center">سلسلة i3</td></tr>
<tr><td style="text-align:center">سحابة تينسنت</td><td style="text-align:center">سلسلة IT5</td></tr>
</tbody>
</table>
<p>توفر أنواع الأجهزة هذه تخزين قرص NVMe. يمكنك استخدام الأمر <code translate="no">lsblk</code> في مثيلات هذه الأنواع من الأجهزة للتحقق مما إذا كانت تحتوي على وحدة تخزين قرص NVMe. إذا كان لديهم ذلك، يمكنك المتابعة إلى الخطوة التالية.</p>
<pre><code translate="no" class="language-bash">$ lsblk | grep nvme
nvme0n1     259:0    0 250.0G  0 disk 
nvme1n1     259:1    0 250.0G  0 disk 
<button class="copy-code-btn"></button></code></pre>
<h2 id="Configure-Kubernetes-to-use-local-disk" class="common-anchor-header">تكوين Kubernetes لاستخدام القرص المحلي<button data-href="#Configure-Kubernetes-to-use-local-disk" class="anchor-icon" translate="no">
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
    </button></h2><p>لتهيئة QueryNode من Milvus Distributed لاستخدام تخزين قرص NVMe، تحتاج إلى تكوين العقد العاملة لمجموعات Kubernetes المستهدفة لتخزين الحاويات والصور على قرص NVMe. يختلف الإجراء الخاص بذلك اعتمادًا على موفري السحابة.</p>
<h3 id="AWS" class="common-anchor-header">AWS</h3><p>عند استخدام Amazon EKS، يمكنك تخصيص العُقد المُدارة باستخدام قوالب التشغيل، حيث يمكنك تحديد إعدادات التكوين لمجموعات العقد الخاصة بك. فيما يلي مثال على كيفية تركيب قرص NVMe على العقد العاملة في مجموعة Amazon EKS الخاصة بك:</p>
<pre><code translate="no" class="language-bash">MIME-Version: 1.0
Content-Type: multipart/mixed; boundary=<span class="hljs-string">&quot;==MYBOUNDARY==&quot;</span>

--==MYBOUNDARY==
Content-Type: text/x-shellscript; charset=<span class="hljs-string">&quot;us-ascii&quot;</span>

<span class="hljs-comment">#!/bin/bash</span>
<span class="hljs-built_in">echo</span> <span class="hljs-string">&quot;Running custom user data script&quot;</span>
<span class="hljs-keyword">if</span> ( lsblk | fgrep -q nvme1n1 ); <span class="hljs-keyword">then</span>
    <span class="hljs-built_in">mkdir</span> -p /mnt/data /var/lib/kubelet /var/lib/docker
    mkfs.xfs /dev/nvme1n1
    mount /dev/nvme1n1 /mnt/data
    <span class="hljs-built_in">chmod</span> 0755 /mnt/data
    <span class="hljs-built_in">mv</span> /var/lib/kubelet /mnt/data/
    <span class="hljs-built_in">mv</span> /var/lib/docker /mnt/data/
    <span class="hljs-built_in">ln</span> -sf /mnt/data/kubelet /var/lib/kubelet
    <span class="hljs-built_in">ln</span> -sf /mnt/data/docker /var/lib/docker
    UUID=$(lsblk -f | grep nvme1n1 | awk <span class="hljs-string">&#x27;{print $3}&#x27;</span>)
    <span class="hljs-built_in">echo</span> <span class="hljs-string">&quot;UUID=<span class="hljs-variable">$UUID</span>     /mnt/data   xfs    defaults,noatime  1   1&quot;</span> &gt;&gt; /etc/fstab
<span class="hljs-keyword">fi</span>
<span class="hljs-built_in">echo</span> 10485760 &gt; /proc/sys/fs/aio-max-nr

--==MYBOUNDARY==--
<button class="copy-code-btn"></button></code></pre>
<div class="alert note">
<p>في المثال أعلاه، نفترض أن قرص NVMe هو <code translate="no">/dev/nvme1n1</code>. تحتاج إلى تعديل البرنامج النصي ليتوافق مع التكوين الخاص بك.</p>
</div>
<p>للحصول على التفاصيل، راجع <a href="https://docs.aws.amazon.com/eks/latest/userguide/launch-templates.html#launch-template-user-data">تخصيص العقد المدارة باستخدام قوالب التشغيل</a>.</p>
<h3 id="GCP" class="common-anchor-header">GCP</h3><p>لتوفير تخزين محلي SSD على مجموعات محرك Google Kubernetes Engine (GKE)، وتكوين أحمال العمل لاستهلاك البيانات من التخزين المؤقت المدعوم بـ SSD المحلي المدعوم بـ SSD والمتصل بالعقد في مجموعتك، قم بتشغيل الأمر التالي:</p>
<pre><code translate="no" class="language-bash">gcloud container node-pools create <span class="hljs-variable">${POOL_NAME}</span> \
    --cluster=<span class="hljs-variable">${CLUSTER_NAME}</span> \
    --ephemeral-storage-local-ssd count=<span class="hljs-variable">${NUMBER_OF_DISKS}</span> \
    --machine-type=<span class="hljs-variable">${MACHINE_TYPE}</span>
<button class="copy-code-btn"></button></code></pre>
<p>لمزيد من التفاصيل، راجع <a href="https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd">توفير تخزين SSD محلي على GKE</a>.</p>
<h3 id="Azure" class="common-anchor-header">أزور</h3><p>لإنشاء مجموعة مقياس آلة افتراضية (VMSS) مع وحدة تخزين أقراص NVMe محلية، تحتاج إلى تمرير بيانات مخصصة إلى مثيلات الآلة الافتراضية. فيما يلي مثال على كيفية إرفاق قرص NVMe بمثيلات الآلة الافتراضية في VMSS:</p>
<pre><code translate="no" class="language-bash">mdadm -Cv /dev/md0 -l0 -n2 /dev/nvme0n1 /dev/nvme1n1
mdadm -Ds &gt; /etc/mdadm/mdadm.conf 
update-initramfs -u

mkfs.xfs /dev/md0
<span class="hljs-built_in">mkdir</span> -p /var/lib/kubelet
<span class="hljs-built_in">echo</span> <span class="hljs-string">&#x27;/dev/md0 /var/lib/kubelet xfs defaults 0 0&#x27;</span> &gt;&gt; /etc/fstab
mount -a
<button class="copy-code-btn"></button></code></pre>
<div class="alert note">
<p>في المثال أعلاه، نفترض أن أقراص NVMe هي <code translate="no">/dev/nvme0n1</code> و <code translate="no">/dev/nvme1n1</code>. تحتاج إلى تعديل البرنامج النصي لمطابقة التكوين الخاص بك.</p>
</div>
<h3 id="Alibaba-Cloud--TecentCloud" class="common-anchor-header">علي بابا كلاود وتيسنت كلاود</h3><p>لإنشاء تجمع عقدة يستخدم وحدات تخزين SSD محلية، نحتاج إلى تمرير بيانات مخصصة. فيما يلي مثال على البيانات المخصصة.</p>
<pre><code translate="no" class="language-bash"><span class="hljs-meta">#!/bin/bash</span>
<span class="hljs-built_in">echo</span> <span class="hljs-string">&quot;nvme init start...&quot;</span>
mkfs.xfs /dev/nvme0n1
<span class="hljs-built_in">mkdir</span> -p /mnt/data
<span class="hljs-built_in">echo</span> <span class="hljs-string">&#x27;/dev/nvme0n1 /mnt/data/ xfs defaults 0 0&#x27;</span> &gt;&gt; /etc/fstab
mount -a

<span class="hljs-built_in">mkdir</span> -p /mnt/data/kubelet /mnt/data/containerd /mnt/data/log/pods
<span class="hljs-built_in">mkdir</span> -p  /var/lib/kubelet /var/lib/containerd /var/log/pods

<span class="hljs-built_in">echo</span> <span class="hljs-string">&#x27;/mnt/data/kubelet /var/lib/kubelet none defaults,bind 0 0&#x27;</span> &gt;&gt; /etc/fstab
<span class="hljs-built_in">echo</span> <span class="hljs-string">&#x27;/mnt/data/containerd /var/lib/containerd none defaults,bind 0 0&#x27;</span> &gt;&gt; /etc/fstab
<span class="hljs-built_in">echo</span> <span class="hljs-string">&#x27;/mnt/data/log/pods /var/log/pods none defaults,bind 0 0&#x27;</span> &gt;&gt; /etc/fstab
mount -a

<span class="hljs-built_in">echo</span> <span class="hljs-string">&quot;nvme init end...&quot;</span>
<button class="copy-code-btn"></button></code></pre>
<div class="alert note">
<p>في المثال أعلاه، نفترض أن قرص NVMe هو <code translate="no">/dev/nvme0n1</code>. تحتاج إلى تعديل البرنامج النصي لمطابقة التكوين الخاص بك.</p>
</div>
<h3 id="Your-own-IDC" class="common-anchor-header">IDC الخاص بك</h3><p>إذا كنت تقوم بتشغيل IDC الخاص بك وتريد تكوين الحاويات الخاصة بك لاستخدام نظام الملفات على قرص NVMe المثبت حديثًا بشكل افتراضي في الحاوية (Contirond)، فاتبع الخطوات التالية:</p>
<ul>
<li><p><strong>قم بتركيب أقراص NVMe.</strong></p>
<p>تأكد من تركيب قرص NVMe بشكل صحيح على جهازك المضيف. يمكنك تحميله إلى دليل من اختيارك. على سبيل المثال، إذا قمت بتحميله على <code translate="no">/mnt/nvme</code> ، فتأكد من إعداده بشكل صحيح ويمكنك رؤية القرص متاحًا على <code translate="no">/mnt/nvme</code> عن طريق تشغيل <code translate="no">lsblk</code> أو <code translate="no">df -h</code>.</p></li>
<li><p><strong>تحديث تكوين الحاويةd.</strong></p>
<p>قم بتعديل تكوين containerd لاستخدام التثبيت الجديد كدليل جذر لتخزين الحاوية.</p>
<pre><code translate="no" class="language-bash"><span class="hljs-built_in">sudo</span> <span class="hljs-built_in">mkdir</span> -p /mnt/nvme/containerd /mnt/nvme/containerd/state
<span class="hljs-built_in">sudo</span> vim /etc/containerd/config.toml
<button class="copy-code-btn"></button></code></pre>
<p>حدد موقع قسم <code translate="no">[plugins.&quot;io.containerd.grpc.v1.cri&quot;.containerd]</code> ، وقم بتعديل إعدادات <code translate="no">snapshotter</code> و <code translate="no">root</code> على النحو التالي ：</p>
<pre><code translate="no" class="language-toml"><span class="hljs-section">[plugins.&quot;io.containerd.grpc.v1.cri&quot;.containerd]</span>
<span class="hljs-attr">snapshotter</span> = <span class="hljs-string">&quot;overlayfs&quot;</span>
<span class="hljs-attr">root</span> = <span class="hljs-string">&quot;/mnt/nvme/containerd&quot;</span>
<span class="hljs-attr">state</span> = <span class="hljs-string">&quot;/mnt/nvme/containerd/state&quot;</span>
<button class="copy-code-btn"></button></code></pre></li>
<li><p><strong>أعد تشغيل الحاوية.</strong></p>
<p>أعد تشغيل خدمة containerd لتطبيق التغييرات.</p>
<pre><code translate="no" class="language-bash"><span class="hljs-built_in">sudo</span> systemctl restart containerd
<button class="copy-code-btn"></button></code></pre></li>
</ul>
<h2 id="Verify-disk-performance" class="common-anchor-header">التحقق من أداء القرص<button data-href="#Verify-disk-performance" class="anchor-icon" translate="no">
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
    </button></h2><p>يُنصح بالتحقق من أداء القرص باستخدام <a href="https://github.com/axboe/fio">Fio،</a> وهي أداة شائعة لقياس أداء القرص. فيما يلي مثال على كيفية تشغيل Fio لاختبار أداء القرص.</p>
<ul>
<li><p><strong>انشر جراب الاختبار على العقدة مع قرص NVMe.</strong></p>
<pre><code translate="no" class="language-bash">kubectl create -f ubuntu.yaml
<button class="copy-code-btn"></button></code></pre>
<p>الملف <code translate="no">ubuntu.yaml</code> على النحو التالي:</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-attr">apiVersion:</span> <span class="hljs-string">v1</span>
<span class="hljs-attr">kind:</span> <span class="hljs-string">Pod</span>
<span class="hljs-attr">metadata:</span>
<span class="hljs-attr">name:</span> <span class="hljs-string">ubuntu</span>
<span class="hljs-attr">spec:</span>
<span class="hljs-attr">containers:</span>
<span class="hljs-bullet">-</span> <span class="hljs-attr">name:</span> <span class="hljs-string">ubuntu</span>
    <span class="hljs-attr">image:</span> <span class="hljs-string">ubuntu:latest</span>
    <span class="hljs-attr">command:</span> [<span class="hljs-string">&quot;sleep&quot;</span>, <span class="hljs-string">&quot;86400&quot;</span>]
    <span class="hljs-attr">volumeMounts:</span>
    <span class="hljs-bullet">-</span> <span class="hljs-attr">name:</span> <span class="hljs-string">data-volume</span>
        <span class="hljs-attr">mountPath:</span> <span class="hljs-string">/data</span>
<span class="hljs-attr">volumes:</span>
    <span class="hljs-bullet">-</span> <span class="hljs-attr">name:</span> <span class="hljs-string">data-volume</span>
    <span class="hljs-attr">emptyDir:</span> {}
<button class="copy-code-btn"></button></code></pre></li>
<li><p><strong>قم بتشغيل Fio لاختبار أداء القرص.</strong></p>
<pre><code translate="no" class="language-bash"><span class="hljs-comment"># enter the container</span>
kubectl <span class="hljs-built_in">exec</span> pod/ubuntu -it bash

<span class="hljs-comment"># in container</span>
apt-get update
apt-get install fio -y

<span class="hljs-comment"># change to the mounted dir</span>
<span class="hljs-built_in">cd</span> /data

<span class="hljs-comment"># write 10GB</span>
fio -direct=1 -iodepth=128 -rw=randwrite -ioengine=libaio -bs=4K -size=10G -numjobs=10 -runtime=600 -group_reporting -filename=<span class="hljs-built_in">test</span> -name=Rand_Write_IOPS_Test

<span class="hljs-comment"># verify the read speed</span>
<span class="hljs-comment"># compare with the disk performance indicators provided by various cloud providers.</span>
fio --filename=<span class="hljs-built_in">test</span> --direct=1 --rw=randread --bs=4k --ioengine=libaio --iodepth=64 --runtime=120 --numjobs=128 --time_based --group_reporting --name=iops-test-job --eta-newline=1  --<span class="hljs-built_in">readonly</span>
<button class="copy-code-btn"></button></code></pre>
<p>ويجب أن يبدو الإخراج هكذا:</p>
<pre><code translate="no" class="language-bash">Jobs: 128 (f=128): [r(128)][100.0%][r=1458MiB/s][r=373k IOPS][eta 00m:00s]
iops-test-job: (groupid=0, <span class="hljs-built_in">jobs</span>=128): err= 0: pid=768: Mon Jun 24 09:35:06 2024
<span class="hljs-built_in">read</span>: IOPS=349k, BW=1364MiB/s (1430MB/s)(160GiB/120067msec)
    slat (nsec): min=765, max=530621k, avg=365836.09, stdev=4765464.96
    clat (usec): min=35, max=1476.0k, avg=23096.78, stdev=45409.13
    lat (usec): min=36, max=1571.6k, avg=23462.62, stdev=46296.74
    clat percentiles (usec):
    |  1.00th=[    69],  5.00th=[    79], 10.00th=[    85], 20.00th=[    95],
    | 30.00th=[   106], 40.00th=[   123], 50.00th=[   149], 60.00th=[ 11469],
    | 70.00th=[ 23462], 80.00th=[ 39584], 90.00th=[ 70779], 95.00th=[103285],
    | 99.00th=[189793], 99.50th=[244319], 99.90th=[497026], 99.95th=[591397],
    | 99.99th=[767558]
bw (  MiB/s): min=  236, max= 4439, per=100.00%, avg=1365.82, stdev= 5.02, samples=30591
iops        : min=60447, max=1136488, avg=349640.62, stdev=1284.65, samples=30591
lat (usec)   : 50=0.01%, 100=24.90%, 250=30.47%, 500=0.09%, 750=0.31%
lat (usec)   : 1000=0.08%
lat (msec)   : 2=0.32%, 4=0.59%, 10=1.86%, 20=8.20%, 50=17.29%
lat (msec)   : 100=10.62%, 250=4.80%, 500=0.38%, 750=0.09%, 1000=0.01%
lat (msec)   : 2000=0.01%
cpu          : usr=0.20%, sys=0.48%, ctx=838085, majf=0, minf=9665
IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, &gt;=64=100.0%
    submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, &gt;=64=0.0%
    complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, &gt;=64=0.0%
    issued rwts: total=41910256,0,0,0 short=0,0,0,0 dropped=0,0,0,0
    latency   : target=0, window=0, percentile=100.00%, depth=64
<button class="copy-code-btn"></button></code></pre></li>
</ul>
<h2 id="Deploy-Milvus-Distributed" class="common-anchor-header">نشر ميلفوس الموزع<button data-href="#Deploy-Milvus-Distributed" class="anchor-icon" translate="no">
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
    </button></h2><p>بمجرد أن تكون نتائج التحقق مرضية، يمكنك نشر Milvus Distributed بالخطوات التالية:</p>
<h3 id="Tips-for-deploying-Milvus-Distributed-using-Helm" class="common-anchor-header">نصائح لنشر Milvus Distributed باستخدام Helm</h3><p>تستخدم كبسولة QueryNode أقراص NVMe كوحدات تخزين EmptyDir بشكل افتراضي. يُنصح بتحميل أقراص NVMe على <code translate="no">/var/lib/milvus/data</code> داخل كبسولات QueryNode لضمان الأداء الأمثل.</p>
<p>للحصول على تفاصيل حول كيفية نشر Milvus Distributed باستخدام Helm، راجع <a href="/docs/ar/install_cluster-helm.md">تشغيل Milvus في Kubernetes باستخدام Helm</a>.</p>
<h3 id="Tips-for-deploying-Milvus-Distributed-using-Milvus-Operator" class="common-anchor-header">نصائح لنشر ميلفوس الموزع باستخدام مشغل ميلفوس</h3><p>يقوم مشغل Milvus تلقائيًا بتكوين جراب QueryNode تلقائيًا لاستخدام أقراص NVMe كوحدات تخزين EmptyDir. يُنصح بإضافة التكوينات التالية إلى المورد المخصص <code translate="no">MilvusCluster</code>:</p>
<pre><code translate="no" class="language-yaml"><span class="hljs-string">...</span>
<span class="hljs-attr">spec:</span>
  <span class="hljs-attr">components:</span>
    <span class="hljs-attr">queryNode:</span>
      <span class="hljs-attr">volumeMounts:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-attr">mountPath:</span> <span class="hljs-string">/var/lib/milvus/data</span>
        <span class="hljs-attr">name:</span> <span class="hljs-string">data</span>
      <span class="hljs-attr">volumes:</span>
      <span class="hljs-bullet">-</span> <span class="hljs-attr">emptyDir:</span>
        <span class="hljs-attr">name:</span> <span class="hljs-string">data</span>
<button class="copy-code-btn"></button></code></pre>
<p>سيضمن ذلك أن تستخدم جراب QueryNode قرص NVMe كوحدة تخزين بيانات. للحصول على تفاصيل حول كيفية نشر ميلفوس الموزعة باستخدام مشغل ميلفوس، راجع <a href="/docs/ar/install_cluster-milvusoperator.md">تشغيل ميلفوس في Kubernetes باستخدام مشغل ميلفوس</a>.</p>
