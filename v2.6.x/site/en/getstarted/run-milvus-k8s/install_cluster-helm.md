---
id: install_cluster-helm.md
label: Helm
related_key: Kubernetes
summary: Learn how to install Milvus cluster on Kubernetes.
title: Install Milvus Cluster with Helm
---

# Run Milvus in Kubernetes with Helm

This page illustrates how to start a Milvus instance in Kubernetes using [Milvus Helm charts](https://github.com/zilliztech/milvus-helm).

## Overview

Helm uses a packaging format called charts. A chart is a collection of files that describe a related set of Kubernetes resources. Milvus provides a set of charts to help you deploy Milvus dependencies and components.

## Prerequisites

- [Install Helm CLI](https://helm.sh/docs/intro/install/).
- [Create a K8s cluster](prerequisite-helm.md#How-can-I-start-a-K8s-cluster-locally-for-test-purposes).
- Install a [StorageClass](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/). You can check the installed StorageClass as follows.

    ```bash
    $ kubectl get sc

    NAME                  PROVISIONER                  RECLAIMPOLICY    VOLUMEBIINDINGMODE    ALLOWVOLUMEEXPANSION     AGE
    standard (default)    k8s.io/minikube-hostpath     Delete           Immediate             false 
    ```

- Check [the hardware and software requirements](prerequisite-helm.md) before installation.
- Before installing Milvus, it is recommended to use the [Milvus Sizing Tool](https://milvus.io/tools/sizing) to estimate the hardware requirements based on your data size. This helps ensure optimal performance and resource allocation for your Milvus installation.

<div class="alert note">

If you encounter any issues pulling the image, contact us at <a href="mailto:community@zilliz.com">community@zilliz.com</a> with details about the problem, and we'll provide you with the necessary support.

</div>

## Install Milvus Helm Chart

Before installing Milvus Helm Charts, you need to add Milvus Helm repository.

```
$ helm repo add milvus https://zilliztech.github.io/milvus-helm/
```

<div class="alert note">

The Milvus Helm Charts repo at `https://github.com/milvus-io/milvus-helm` has been archived and you can get further updates from `https://github.com/zilliztech/milvus-helm` as follows:

```shell
helm repo add zilliztech https://zilliztech.github.io/milvus-helm/
helm repo update
# upgrade existing helm release
helm upgrade my-release zilliztech/milvus --reset-then-reuse-values
```

The archived repo is still available for the charts up to 4.0.31. For later releases, use the new repo instead.

</div>

Then fetch Milvus charts from the repository as follows:

```
$ helm repo update
```

You can always run this command to fetch the latest Milvus Helm charts.

## Online install

### 1. Deploy a Milvus cluster

Once you have installed the Helm chart, you can start Milvus on Kubernetes. This section will guide you through the steps to starting Milvus.

- To deploy a Milvus instance in standalone mode, run the following command:

  ```bash
  helm install my-release milvus/milvus \
    --set image.all.tag=v2.6.0-rc1 \
    --set cluster.enabled=false \
    --set pulsarv3.enabled=false \
    --set standalone.messageQueue=woodpecker \
    --set woodpecker.enabled=true \
    --set streaming.enabled=true
  ```

  <div class="alert note">

  Starting from Milvus 2.6.x, the following architecture changes have been made in standalone mode:

  - The default message queue (MQ) is **Woodpecker**.
  - The **Streaming Node** component is introduced and enabled by default.

  For details, refer to the [Architecture Overview](architecture_overview.md).

  </div>

- To deploy a Milvus instance in cluster mode, run the following command:

  ```bash
  helm install my-release milvus/milvus \
    --set image.all.tag=v2.6.0-rc1 \
    --set streaming.enabled=true \
    --set indexNode.enabled=false
  ```

  <div class="alert note">

  Starting from Milvus 2.6.x, the following architecture changes have been made in cluster mode:

  - The default MQ is still **Pulsar**.
  - The **Streaming Node** component is introduced and enabled by default.
  - The **Index Node** and **Data Node** are merged into a single **Data Node** component.

  For details, refer to the [Architecture Overview](architecture_overview.md).

  </div>

In the above command, `my-release` is the release name, and `milvus/milvus` is the locally installed chart repository. To use a different name, replace `my-release` with the one you see fit.

The commands above deploy a Milvus instance with its components and dependencies using default configurations. To customize these settings, we recommend you use the [Milvus Sizing Tool](https://milvus.io/tools/sizing) to adjust the configurations based on your actual data size and then download the corresponding YAML file. To learn more about configuration parameters, refer to [Milvus System Configurations Checklist](https://milvus.io/docs/system_configuration.md).

<div class="alert note">
  <ul>
    <li>The release name should only contain letters, numbers and dashes. Dots are not allowed in the release name.</li>
    <li>The default command line installs cluster version of Milvus while installing Milvus with Helm. Further setting is needed while installing Milvus standalone.</li>
    <li>According to the <a href="https://kubernetes.io/docs/reference/using-api/deprecation-guide/#v1-25">deprecated API migration guide of Kubernetes</a>, the <b>policy/v1beta1</b> API version of PodDisruptionBudget is no longer served as of v1.25. You are suggested to migrate manifests and API clients to use the <b>policy/v1</b> API version instead. <br/>As a workaround for users who still use the <b>policy/v1beta1</b> API version of PodDisruptionBudget on Kubernetes v1.25 and later, you can instead run the following command to install Milvus:<br/>
    <code>helm install my-release milvus/milvus --set pulsar.bookkeeper.pdb.usePolicy=false,pulsar.broker.pdb.usePolicy=false,pulsar.proxy.pdb.usePolicy=false,pulsar.zookeeper.pdb.usePolicy=false</code></li> 
    <li>See <a href="https://artifacthub.io/packages/helm/milvus/milvus">Milvus Helm Chart</a> and <a href="https://helm.sh/docs/">Helm</a> for more information.</li>
  </ul>
</div>

### 2. Check Milvus cluster status

Run the following command to check the status of all pods in your Milvus cluster.

```
$ kubectl get pods
```

Once all pods are running, the output of the above command should be similar to the following:

```
NAME                                             READY  STATUS   RESTARTS  AGE
my-release-etcd-0                                1/1    Running   0        3m23s
my-release-etcd-1                                1/1    Running   0        3m23s
my-release-etcd-2                                1/1    Running   0        3m23s
my-release-milvus-datanode-68cb87dcbd-4khpm      1/1    Running   0        3m23s
my-release-milvus-indexnode-5c5f7b5bd9-l8hjg     1/1    Running   0        3m24s
my-release-milvus-mixcoord-7fb9488465-dmbbj      1/1    Running   0        3m23s
my-release-milvus-proxy-6bd7f5587-ds2xv          1/1    Running   0        3m24s
my-release-milvus-querynode-5cd8fff495-k6gtg     1/1    Running   0        3m24s
my-release-minio-0                               1/1    Running   0        3m23s
my-release-minio-1                               1/1    Running   0        3m23s
my-release-minio-2                               1/1    Running   0        3m23s
my-release-minio-3                               1/1    Running   0        3m23s
my-release-pulsar-autorecovery-86f5dbdf77-lchpc  1/1    Running   0        3m24s
my-release-pulsar-bookkeeper-0                   1/1    Running   0        3m23s
my-release-pulsar-bookkeeper-1                   1/1    Running   0        98s
my-release-pulsar-broker-556ff89d4c-2m29m        1/1    Running   0        3m23s
my-release-pulsar-proxy-6fbd75db75-nhg4v         1/1    Running   0        3m23s
my-release-pulsar-zookeeper-0                    1/1    Running   0        3m23s
my-release-pulsar-zookeeper-metadata-98zbr       0/1   Completed  0        3m24s
```

You can also access Milvus WebUI at `http://127.0.0.1:9091/webui/` to learn more about the your Milvus instance. For details, refer to [Milvus WebUI](milvus-webui.md).

### 3. Forward a local port to Milvus

Run the following command to get the port at which your Milvus cluster serves.

```bash
$ kubectl get pod my-release-milvus-proxy-6bd7f5587-ds2xv --template
='{{(index (index .spec.containers 0).ports 0).containerPort}}{{"\n"}}'
19530
```

The output shows that the Milvus instance serves at the default port **19530**.

<div class="alert note">

If you have deployed Milvus in standalone mode, change the pod name from `my-release-milvus-proxy-xxxxxxxxxx-xxxxx` to `my-release-milvus-xxxxxxxxxx-xxxxx`.

</div>

Then, run the following command to forward a local port to the port at which Milvus serves.

```bash
$ kubectl port-forward service/my-release-milvus 27017:19530
Forwarding from 127.0.0.1:27017 -> 19530
```

Optionally, you can use `:19530` instead of `27017:19530` in the above command to let `kubectl` allocate a local port for you so that you don't have to manage port conflicts.

By default, kubectl's port-forwarding only listens on `localhost`. Use the `address` flag if you want Milvus to listen on the selected or all IP addresses. The following command makes port-forward listen on all IP addresses on the host machine.

## Access Milvus WebUI

Milvus ships with a built-in GUI tool called Milvus WebUI that you can access through your browser. Milvus Web UI enhances system observability with a simple and intuitive interface. You can use Milvus Web UI to observe the statistics and metrics of the components and dependencies of Milvus, check database and collection details, and list detailed Milvus configurations. For details about Milvus Web UI, see [Milvus WebUI](milvus-webui.md)

To enable the access to the Milvus Web UI, you need to port-forward the proxy pod to a local port.

```shell
$ kubectl port-forward --address 0.0.0.0 service/my-release-milvus 27018:9091
Forwarding from 0.0.0.0:27018 -> 9091
```

Now, you can access Milvus Web UI at `http://localhost:27018`.

## Offline install

If you are in a network-restricted environment, follow the procedure in this section to start a Milvus cluster.

### 1. Get Milvus manifest

Run the following command to get the Milvus manifest.

```shell
$ helm template my-release milvus/milvus > milvus_manifest.yaml
```

The above command renders chart templates for a Milvus cluster and saves the output to a manifest file named `milvus_manifest.yaml`. Using this manifest, you can install a Milvus cluster with its components and dependencies in separate pods.

<div class="alert note">

- To install a Milvus instance in the standalone mode where all Milvus components are contained within a single pod, you should run `helm template my-release --set cluster.enabled=false --set etcd.replicaCount=1 --set minio.mode=standalone --set pulsarv3.enabled=false milvus/milvus > milvus_manifest.yaml` instead to render chart templates for a Milvus instance in a standalone mode.
- To change Milvus configurations, download the [`value.yaml`](https://raw.githubusercontent.com/milvus-io/milvus-helm/master/charts/milvus/values.yaml) template, place your desired settings in it, and use `helm template -f values.yaml my-release milvus/milvus > milvus_manifest.yaml` to render the manifest accordingly.

</div>

### 2. Download image-pulling script

The image-pulling script is developed in Python. You should download the script along with its dependencies in the `requirement.txt` file.

```shell
$ wget https://raw.githubusercontent.com/milvus-io/milvus/master/deployments/offline/requirements.txt
$ wget https://raw.githubusercontent.com/milvus-io/milvus/master/deployments/offline/save_image.py
```

### 3. Pull and save images

Run the following command to pull and save the required images.

```shell
$ pip3 install -r requirements.txt
$ python3 save_image.py --manifest milvus_manifest.yaml
```

The images are pulled into a sub-folder named `images` in the current directory.

### 4. Load images

You can now load the images to the hosts in the network-restricted environment as follows:

```shell
$ for image in $(find . -type f -name "*.tar.gz") ; do gunzip -c $image | docker load; done
```

### 5. Deploy Milvus

```shell
$ kubectl apply -f milvus_manifest.yaml
```

Till now, you can follow steps [2](#2-Check-Milvus-cluster-status) and [3](#3-Forward-a-local-port-to-Milvus) of the online install to check the cluster status and forward a local port to Milvus.

## Upgrade running Milvus cluster

Run the following command to upgrade your running Milvus cluster to the latest version:

```shell
$ helm repo update
$ helm upgrade my-release zilliztech/milvus --reset-then-reuse-values
```

## Uninstall Milvus

Run the following command to uninstall Milvus.

```bash
$ helm uninstall my-release
```

## What's next

Having installed Milvus in Docker, you can:

- Check [Hello Milvus](quickstart.md) to see what Milvus can do.

- Learn the basic operations of Milvus:
  - [Manage Databases](manage_databases.md)
  - [Manage Collections](manage-collections.md)
  - [Manage Partitions](manage-partitions.md)
  - [Insert, Upsert & Delete](insert-update-delete.md)
  - [Single-Vector Search](single-vector-search.md)
  - [Hybrid Search](multi-vector-search.md)

- [Upgrade Milvus Using Helm Chart](upgrade_milvus_cluster-helm.md).
- [Scale your Milvus cluster](scaleout.md).
- Deploy your Milvus cluster on clouds:
  - [Amazon EKS](eks.md)
  - [Google Cloud](gcp.md)
  - [Microsoft Azure](azure.md)
- Explore [Milvus WebUI](milvus-webui.md), an intuitive web interface for Milvus observability and management.
- Explore [Milvus Backup](milvus_backup_overview.md), an open-source tool for Milvus data backups.
- Explore [Birdwatcher](birdwatcher_overview.md), an open-source tool for debugging Milvus and dynamic configuration updates.
- Explore [Attu](https://github.com/zilliztech/attu), an open-source GUI tool for intuitive Milvus management.
- [Monitor Milvus with Prometheus](monitor.md).
