---
id: birdwatcher_usage_guides.md
summary: Apprenez à utiliser Birdwatch pour déboguer Milvus.
title: Utiliser Birdwatcher
---
<h1 id="Use-Birdwatcher" class="common-anchor-header">Utiliser Birdwatcher<button data-href="#Use-Birdwatcher" class="anchor-icon" translate="no">
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
    </button></h1><p>Ce guide vous explique comment utiliser Birdwatcher pour vérifier l'état de votre Milvus et le configurer à la volée.</p>
<h2 id="Start-Birdwatcher" class="common-anchor-header">Démarrer Birdwatcher<button data-href="#Start-Birdwatcher" class="anchor-icon" translate="no">
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
    </button></h2><p>Birdwatcher est un outil en ligne de commande, vous pouvez le démarrer comme suit :</p>
<pre><code translate="no" class="language-shell">./birdwatcher
<button class="copy-code-btn"></button></code></pre>
<p>Vous serez alors accueilli par l'invite suivante :</p>
<pre><code translate="no" class="language-shell">Offline &gt;
<button class="copy-code-btn"></button></code></pre>
<h2 id="Connect-to-etcd" class="common-anchor-header">Se connecter à etcd<button data-href="#Connect-to-etcd" class="anchor-icon" translate="no">
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
    </button></h2><p>Vous devez utiliser Birdwatcher pour vous connecter à etcd avant toute autre opération.</p>
<ul>
<li><p>Se connecter avec les paramètres par défaut</p>
<pre><code translate="no" class="language-shell">Offline &gt; connect
Milvus(by-dev) &gt;
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Se connecter depuis Birdwatcher dans un pod</p>
<p>Si vous choisissez d'exécuter Birdwatcher dans un pod Kubernetes, vous devez d'abord obtenir l'adresse IP d'etcd comme suit :</p>
<pre><code translate="no" class="language-shell">kubectl get pod my-release-etcd-0 -o &#x27;jsonpath={.status.podIP}&#x27;
<button class="copy-code-btn"></button></code></pre>
<p>Accédez ensuite au shell du pod.</p>
<pre><code translate="no" class="language-shell">kubectl exec --stdin --tty birdwatcher-7f48547ddc-zcbxj -- /bin/sh
<button class="copy-code-btn"></button></code></pre>
<p>Enfin, utilisez l'adresse IP renvoyée pour vous connecter à etcd comme suit :</p>
<pre><code translate="no" class="language-shell">Offline &gt; connect --etcd ${ETCD_IP_ADDR}:2379
Milvus(by-dev)
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Se connecter avec un chemin racine différent</p>
<p>Si le chemin d'accès à la racine de votre Milvus est différent de <code translate="no">by-dev</code> et qu'un message d'erreur vous signale un chemin d'accès à la racine incorrect, vous pouvez vous connecter à etcd comme suit :</p>
<pre><code translate="no" class="language-shell">Offline &gt; connect --rootPath my-release
Milvus(my-release) &gt;
<button class="copy-code-btn"></button></code></pre>
<p>Si vous ne connaissez pas le chemin racine de votre Milvus, connectez-vous à etcd comme suit :</p>
<pre><code translate="no" class="language-shell">Offline &gt; connect --dry
using dry mode, ignore rootPath and metaPath
Etcd(127.0.0.1:2379) &gt; find-milvus
1 candidates found:
my-release
Etcd(127.0.0.1:2379) &gt; use my-release
Milvus(my-release) &gt;
<button class="copy-code-btn"></button></code></pre></li>
</ul>
<h2 id="Check-Milvus-status" class="common-anchor-header">Vérifier l'état de Milvus<button data-href="#Check-Milvus-status" class="anchor-icon" translate="no">
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
    </button></h2><p>Vous pouvez utiliser les commandes <code translate="no">show</code> pour vérifier l'état du Milvus.</p>
<pre><code translate="no" class="language-shell">Milvus(my-release) &gt; show -h
Usage:
   show [command]

Available Commands:
  alias               list alias meta info
  channel-watch       display channel watching info from data coord meta store
  checkpoint          list checkpoint collection vchannels
  collection-history  display collection change history
  collection-loaded   display information of loaded collection from querycoord
  collections         list current available collection from RootCoord
  config-etcd         list configuations set by etcd source
  configurations      iterate all online components and inspect configuration
  current-version     
  database            display Database info from rootcoord meta
  index               
  partition           list partitions of provided collection
  querycoord-channel  display querynode information from querycoord cluster
  querycoord-cluster  display querynode information from querycoord cluster
  querycoord-task     display task information from querycoord
  replica             list current replica information from QueryCoord
  segment             display segment information from data coord meta store
  segment-index       display segment index information
  segment-loaded      display segment information from querycoordv1 meta
  segment-loaded-grpc list segments loaded information
  session             list online milvus components

Flags:
  -h, --help   help for show

Use &quot; show [command] --help&quot; for more information about a command.
<button class="copy-code-btn"></button></code></pre>
<h3 id="List-sessions" class="common-anchor-header">Lister les sessions</h3><p>Pour répertorier les sessions associées à différents composants dans Milvus :</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show session
Session:datacoord, ServerID: 3, Version: 2.2.11, Address: 10.244.0.8:13333
Session:datanode, ServerID: 6, Version: 2.2.11, Address: 10.244.0.8:21124
Session:indexcoord, ServerID: 4, Version: 2.2.11, Address: 10.244.0.8:31000
Session:indexnode, ServerID: 5, Version: 2.2.11, Address: 10.244.0.8:21121
Session:proxy, ServerID: 8, Version: 2.2.11, Address: 10.244.0.8:19529
Session:querycoord, ServerID: 7, Version: 2.2.11, Address: 10.244.0.8:19531
Session:querynode, ServerID: 2, Version: 2.2.11, Address: 10.244.0.8:21123
Session:rootcoord, ServerID: 1, Version: 2.2.11, Address: 10.244.0.8:53100
<button class="copy-code-btn"></button></code></pre>
<p>Dans la sortie de la commande, chaque entrée de session répertoriée par <code translate="no">show session</code> correspond à un nœud ou à un service actuellement actif et enregistré dans <strong>etcd</strong>.</p>
<h3 id="Check-databases-and-collections" class="common-anchor-header">Vérifier les bases de données et les collections</h3><p>Vous pouvez répertorier toutes les bases de données et collections.</p>
<ul>
<li><p>Lister les bases de données</p>
<p>Dans la sortie de la commande, vous trouverez des informations sur chaque base de données.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show database
=============================
ID: 1   Name: default
TenantID:        State: DatabaseCreated
--- Total Database(s): 1
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Lister les collections</p>
<p>Dans la sortie de la commande, vous trouverez des informations détaillées sur chaque collection.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show collections
================================================================================
DBID: 1
Collection ID: 443407225551410746       Collection Name: medium_articles_2020
Collection State: CollectionCreated     Create Time: 2023-08-08 09:27:08
Fields:
- Field ID: 0   Field Name: RowID       Field Type: Int64
- Field ID: 1   Field Name: Timestamp   Field Type: Int64
- Field ID: 100         Field Name: id          Field Type: Int64
        - Primary Key: true, AutoID: false
- Field ID: 101         Field Name: title       Field Type: VarChar
        - Type Param max_length: 512
- Field ID: 102         Field Name: title_vector        Field Type: FloatVector
        - Type Param dim: 768
- Field ID: 103         Field Name: link        Field Type: VarChar
        - Type Param max_length: 512
- Field ID: 104         Field Name: reading_time        Field Type: Int64
- Field ID: 105         Field Name: publication         Field Type: VarChar
        - Type Param max_length: 512
- Field ID: 106         Field Name: claps       Field Type: Int64
- Field ID: 107         Field Name: responses   Field Type: Int64
Enable Dynamic Schema: false
Consistency Level: Bounded
Start position for channel by-dev-rootcoord-dml_0(by-dev-rootcoord-dml_0_443407225551410746v0): [1 0 28 175 133 76 39 6]
--- Total collections:  1        Matched collections:  1
--- Total channel: 1     Healthy collections: 1
================================================================================
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Afficher une collection spécifique</p>
<p>Vous pouvez afficher une collection spécifique en spécifiant son identifiant.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show collection-history --id 443407225551410746
================================================================================
DBID: 1
Collection ID: 443407225551410746       Collection Name: medium_articles_2020
Collection State: CollectionCreated     Create Time: 2023-08-08 09:27:08
Fields:
- Field ID: 0   Field Name: RowID       Field Type: Int64
- Field ID: 1   Field Name: Timestamp   Field Type: Int64
- Field ID: 100         Field Name: id          Field Type: Int64
        - Primary Key: true, AutoID: false
- Field ID: 101         Field Name: title       Field Type: VarChar
        - Type Param max_length: 512
- Field ID: 102         Field Name: title_vector        Field Type: FloatVector
        - Type Param dim: 768
- Field ID: 103         Field Name: link        Field Type: VarChar
        - Type Param max_length: 512
- Field ID: 104         Field Name: reading_time        Field Type: Int64
- Field ID: 105         Field Name: publication         Field Type: VarChar
        - Type Param max_length: 512
- Field ID: 106         Field Name: claps       Field Type: Int64
- Field ID: 107         Field Name: responses   Field Type: Int64
Enable Dynamic Schema: false
Consistency Level: Bounded
Start position for channel by-dev-rootcoord-dml_0(by-dev-rootcoord-dml_0_443407225551410746v0): [1 0 28 175 133 76 39 6]
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Afficher toutes les collections chargées</p>
<p>Vous pouvez demander à Birdwatcher de filtrer toutes les collections chargées.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show collection-loaded
Version: [&gt;= 2.2.0]     CollectionID: 443407225551410746
ReplicaNumber: 1        LoadStatus: Loaded
--- Collections Loaded: 1
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Lister tous les points de contrôle d'une collection</p>
<p>Vous pouvez demander à Birdwatcher de lister tous les points de contrôle d'une collection spécifique.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show checkpoint --collection 443407225551410746
vchannel by-dev-rootcoord-dml_0_443407225551410746v0 seek to 2023-08-08 09:36:09.54 +0000 UTC, cp channel: by-dev-rootcoord-dml_0_443407225551410746v0, Source: Channel Checkpoint
<button class="copy-code-btn"></button></code></pre></li>
</ul>
<h3 id="Check-index-details" class="common-anchor-header">Vérifier les détails de l'index</h3><p>Exécutez la commande suivante pour lister tous les fichiers d'index en détail.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show index
*************2.1.x***************
*************2.2.x***************
==================================================================
Index ID: 443407225551410801    Index Name: _default_idx_102    CollectionID:443407225551410746
Create Time: 2023-08-08 09:27:19.139 +0000      Deleted: false
Index Type: HNSW        Metric Type: L2
Index Params: 
==================================================================
<button class="copy-code-btn"></button></code></pre>
<h3 id="List-partitions" class="common-anchor-header">Lister les partitions</h3><p>Exécutez la commande suivante pour lister toutes les partitions d'une collection spécifique.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show partition --collection 443407225551410746
Parition ID: 443407225551410747 Name: _default  State: PartitionCreated
--- Total Database(s): 1
<button class="copy-code-btn"></button></code></pre>
<h3 id="Check-channel-status" class="common-anchor-header">Vérifier l'état des canaux</h3><p>Exécutez la commande suivante pour afficher l'état des canaux</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show channel-watch
=============================
key: by-dev/meta/channelwatch/6/by-dev-rootcoord-dml_0_443407225551410746v0
Channel Name:by-dev-rootcoord-dml_0_443407225551410746v0         WatchState: WatchSuccess
Channel Watch start from: 2023-08-08 09:27:09 +0000, timeout at: 1970-01-01 00:00:00 +0000
Start Position ID: [1 0 28 175 133 76 39 6], time: 1970-01-01 00:00:00 +0000
Unflushed segments: []
Flushed segments: []
Dropped segments: []
--- Total Channels: 1
<button class="copy-code-btn"></button></code></pre>
<h3 id="List-all-replicas-and-segments" class="common-anchor-header">Répertorier toutes les répliques et tous les segments</h3><ul>
<li><p>Répertorier toutes les répliques</p>
<p>Exécutez la commande suivante pour répertorier toutes les répliques et les collections correspondantes.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show replica
================================================================================
ReplicaID: 443407225685278721 CollectionID: 443407225551410746 version:&gt;=2.2.0
All Nodes:[2]
<button class="copy-code-btn"></button></code></pre></li>
<li><p>Liste de tous les segments</p>
<p>Exécutez la commande suivante pour afficher la liste de tous les segments et leur état</p>
<pre><code translate="no" class="language-shell">SegmentID: 443407225551610865 State: Flushed, Row Count:5979
--- Growing: 0, Sealed: 0, Flushed: 1
--- Total Segments: 1, row count: 5979
<button class="copy-code-btn"></button></code></pre>
<p>Exécuter la commande suivante pour lister en détail tous les segments chargés. Pour Milvus 2.1.x, utilisez plutôt <code translate="no">show segment-loaded</code>.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show segment-loaded-grpc
===========
ServerID 2
Channel by-dev-rootcoord-dml_0_443407225551410746v0, collection: 443407225551410746, version 1691486840680656937
Leader view for channel: by-dev-rootcoord-dml_0_443407225551410746v0
Growing segments number: 0 , ids: []
SegmentID: 443407225551610865 CollectionID: 443407225551410746 Channel: by-dev-rootcoord-dml_0_443407225551410746v0
Sealed segments number: 1    
<button class="copy-code-btn"></button></code></pre></li>
</ul>
<h3 id="List-configurations" class="common-anchor-header">Lister les configurations</h3><p>Vous pouvez demander à Birdwatcher de lister les configurations actuelles de chaque composant Milvus.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show configurations
client nil Session:proxy, ServerID: 8, Version: 2.2.11, Address: 10.244.0.8:19529
Component rootcoord-1
rootcoord.importtaskexpiration: 900
rootcoord.enableactivestandby: false
rootcoord.importtaskretention: 86400
rootcoord.maxpartitionnum: 4096
rootcoord.dmlchannelnum: 16
rootcoord.minsegmentsizetoenableindex: 1024
rootcoord.port: 53100
rootcoord.address: localhost
rootcoord.maxdatabasenum: 64
Component datacoord-3
...
querynode.gracefulstoptimeout: 30
querynode.cache.enabled: true
querynode.cache.memorylimit: 2147483648
querynode.scheduler.maxreadconcurrentratio: 2
<button class="copy-code-btn"></button></code></pre>
<p>Vous pouvez également visiter chaque composant Milvus pour trouver sa configuration. L'exemple suivant montre comment lister la configuration du QueryCoord avec l'ID 7.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; show session
Session:datacoord, ServerID: 3, Version: 2.2.11, Address: 10.244.0.8:13333
Session:datanode, ServerID: 6, Version: 2.2.11, Address: 10.244.0.8:21124
Session:indexcoord, ServerID: 4, Version: 2.2.11, Address: 10.244.0.8:31000
Session:indexnode, ServerID: 5, Version: 2.2.11, Address: 10.244.0.8:21121
Session:proxy, ServerID: 8, Version: 2.2.11, Address: 10.244.0.8:19529
Session:querycoord, ServerID: 7, Version: 2.2.11, Address: 10.244.0.8:19531
Session:querynode, ServerID: 2, Version: 2.2.11, Address: 10.244.0.8:21123
Session:rootcoord, ServerID: 1, Version: 2.2.11, Address: 10.244.0.8:53100

Milvus(by-dev) &gt; visit querycoord 7
QueryCoord-7(10.244.0.8:19531) &gt; configuration
Key: querycoord.enableactivestandby, Value: false
Key: querycoord.channeltasktimeout, Value: 60000
Key: querycoord.overloadedmemorythresholdpercentage, Value: 90
Key: querycoord.distpullinterval, Value: 500
Key: querycoord.checkinterval, Value: 10000
Key: querycoord.checkhandoffinterval, Value: 5000
Key: querycoord.taskexecutioncap, Value: 256
Key: querycoord.taskmergecap, Value: 8
Key: querycoord.autohandoff, Value: true
Key: querycoord.address, Value: localhost
Key: querycoord.port, Value: 19531
Key: querycoord.memoryusagemaxdifferencepercentage, Value: 30
Key: querycoord.refreshtargetsintervalseconds, Value: 300
Key: querycoord.balanceintervalseconds, Value: 60
Key: querycoord.loadtimeoutseconds, Value: 1800
Key: querycoord.globalrowcountfactor, Value: 0.1
Key: querycoord.scoreunbalancetolerationfactor, Value: 0.05
Key: querycoord.reverseunbalancetolerationfactor, Value: 1.3
Key: querycoord.balancer, Value: ScoreBasedBalancer
Key: querycoord.autobalance, Value: true
Key: querycoord.segmenttasktimeout, Value: 120000
<button class="copy-code-btn"></button></code></pre>
<h2 id="Backup-metrics" class="common-anchor-header">Sauvegarde des métriques<button data-href="#Backup-metrics" class="anchor-icon" translate="no">
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
    </button></h2><p>Vous pouvez demander à Birdwatcher de sauvegarder les métriques de tous les composants</p>
<pre><code translate="no" class="language-shell">Milvus(my-release) &gt; backup
Backing up ... 100%(2452/2451)
backup etcd for prefix  done
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
http://10.244.0.10:9091/metrics
backup for prefix done, stored in file: bw_etcd_ALL.230810-075211.bak.gz
<button class="copy-code-btn"></button></code></pre>
<p>Vous pouvez ensuite vérifier le fichier dans le répertoire où vous démarrez Birdwatcher.</p>
<h2 id="Probe-collections" class="common-anchor-header">Sonder les collections<button data-href="#Probe-collections" class="anchor-icon" translate="no">
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
    </button></h2><p>Vous pouvez demander à Birdwatcher de sonder l'état des collections chargées avec des clés primaires spécifiées ou des requêtes fictives.</p>
<h3 id="Probe-collection-with-known-primary-key" class="common-anchor-header">Sonder une collection avec une clé primaire connue</h3><p>Dans la commande <code translate="no">probe</code>, vous devez spécifier la clé primaire en utilisant le drapeau <code translate="no">pk</code> et l'identifiant de la collection en utilisant le drapeau <code translate="no">collection</code>.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; probe pk --pk 110 --collection 442844725212299747
PK 110 found on segment 442844725212299830
Field id, value: &amp;{long_data:&lt;data:110 &gt; }
Field title, value: &amp;{string_data:&lt;data:&quot;Human Resources Datafication&quot; &gt; }
Field title_vector, value: &amp;{dim:768 float_vector:&lt;data:0.022454707 data:0.007861045 data:0.0063843643 data:0.024065714 data:0.013782166 data:0.018483251 data:-0.026526336 ... data:-0.06579628 data:0.00033906146 data:0.030992996 data:-0.028134001 data:-0.01311325 data:0.012471594 &gt; }
Field article_meta, value: &amp;{json_data:&lt;data:&quot;{\&quot;link\&quot;:\&quot;https:\\/\\/towardsdatascience.com\\/human-resources-datafication-d44c8f7cb365\&quot;,\&quot;reading_time\&quot;:6,\&quot;publication\&quot;:\&quot;Towards Data Science\&quot;,\&quot;claps\&quot;:256,\&quot;responses\&quot;:0}&quot; &gt; }
<button class="copy-code-btn"></button></code></pre>
<h3 id="Probe-all-collections-with-mock-queries" class="common-anchor-header">Sonder toutes les collections avec des requêtes fictives</h3><p>Vous pouvez également demander à Birdwatcher de sonder toutes les collections avec des requêtes fictives.</p>
<pre><code translate="no" class="language-shell">Milvus(by-dev) &gt; probe query
probing collection 442682158191982314
Found vector field vector(103) with dim[384], indexID: 442682158191990455
failed to generated mock request probing index type IVF_FLAT not supported yet
probing collection 442844725212086932
Found vector field title_vector(102) with dim[768], indexID: 442844725212086936
Shard my-release-rootcoord-dml_1_442844725212086932v0 leader[298] probe with search success.
probing collection 442844725212299747
Found vector field title_vector(102) with dim[768], indexID: 442844725212299751
Shard my-release-rootcoord-dml_4_442844725212299747v0 leader[298] probe with search success.
probing collection 443294505839900248
Found vector field vector(101) with dim[256], indexID: 443294505839900251
Shard my-release-rootcoord-dml_5_443294505839900248v0 leader[298] probe with search success.
<button class="copy-code-btn"></button></code></pre>
