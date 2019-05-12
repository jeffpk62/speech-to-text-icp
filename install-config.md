---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-07"

subcollection: speech-to-text-icp

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:important: .important}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}
{:download: .download}
{:gif: data-image-type='gif'}

# Installing and configuring the service
{: #install}

{{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care is supported on [{{site.data.keyword.icpfull}} version 3.1.0 ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html){: new_window} with Kubernetes version 1.10.0 or later. Follow these instructions to install and configure the {{site.data.keyword.speechtotextshort}}: Customer Care service.
{: shortdesc}

## Software requirements
{: #prereqs}

- {{site.data.keyword.icpfull_notm}} 3.1.0
- Kubernetes 1.10.0
- Helm 2.7.3

## System requirements
{: #system-requirements}

In addition to the general [Hardware requirements and recommendations](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/supported_system_config/hardware_reqs.html) for {{site.data.keyword.icpfull_notm}}, {{site.data.keyword.speechtotextshort}}: Customer Care has the following requirements:

-   **x86** is the only architecture supported at this time.
-   A minimum of three worker nodes are needed for high availability.
-   The resources required for the installation, in terms of CPUs and memory, depend on the configuration that you select. There are two typical installation configurations:
    -   The **development configuration**, which is the configuration that is used in the default installation, has a minimal footprint and is meant for development purposes and as a proof of concept. It can only handle several concurrent recognition sessions.
    -   The **production configuration** is a highly available solution that is intended to run production workloads. The total capacity of the production-grade installation is 50 concurrent {{site.data.keyword.speechtotextshort}} sessions; for example, 50 concurrent WebSocket connections that are streaming audio (50 Virtual Processor Cores, or VPCs). This figure corresponds roughly to a total of about 1 million minutes of audio per month assuming the cluster is used at 100% capacity. If you install the [Speech Analytics](analytics.html) feature, you can process batches of audio files at a rate of 30 concurrent sessions, meaning that a maximum of 30 audio files can be processed concurrently (30 VPCs).

The total resource requirements of the {{site.data.keyword.speechtotextshort}}: Customer Care solution with Speech Analytics are:

-   **31.95** CPUs and **88** GBs of memory for the development configuration
-   **79.55** CPUs and **183.5** GBs of memory for the production configuration

The standard installation requirements without Speech Analytics are:

-   **14.95** CPUs and **36** GBs of memory for the development configuration
-   **47.55** CPUs and **103** GBs of memory for the production configuration

For more details about required resources and how to scale from the development configuration to the production configuration, see [Required resources details](#required-resources-details).

## Step 1: Purchase and download installation artifacts
{: #download-artifacts}

1.  Purchase {{site.data.keyword.speechtotextshort}}: Customer Care from [Passport Advantage ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/software/passportadvantage/index.html){:new_window}.

2.  Download the appropriate package for your environment.

    {{site.data.keyword.speechtotextshort}}: Customer Care includes {{site.data.keyword.icpfull_notm}} Foundation version 3.1.0.

    If you have {{site.data.keyword.icpfull_notm}} version 3.1.0 set up in your environment already, you can download the archive for {{site.data.keyword.speechtotextshort}}: Customer Care alone.

    If you do not have {{site.data.keyword.icpfull_notm}} or have a later major version of {{site.data.keyword.icpfull_notm}} set up in your organization (4.x, for example), you must install version 3.1.0. To get the {{site.data.keyword.icpfull_notm}} 3.1.0 archive included, choose the e-assembly that includes {{site.data.keyword.icpfull_notm}}.

The Passport Advantage Archive (PPA) file for {{site.data.keyword.speechtotextshort}}: Customer Care contains a Helm chart and images. Helm is the Kubernetes native package management system that is used for application management inside an {{site.data.keyword.icpfull_notm}} cluster.

## Step 2: Prepare the cloud environment
{: #install-icp}

You must have cluster administrator or team administrator access to the systems in your cluster.

1.  If you do not have {{site.data.keyword.icpfull_notm}} version 3.1.0 set up, install it. See [Installing a standard IBM Cloud Private environment ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/install_containers.html).
2.  If you haven't done so, install the IBM Cloud Private command-line interface and log in to your cluster. See [Installing the IBM Cloud Private CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_cluster/install_cli.html).
3.  Configure authentication from your computer to the Docker private image registry host and log in to the private registry. See [Configuring authentication for the Docker CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html).
4.  If you are not a root user, ensure that your account is part of the `docker` group. See [Post-installation steps ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.docker.com/engine/installation/linux/linux-postinstall/#manage-docker-as-a-non-root-user) in the Docker documentation.
5.  Ensure that you have a stable network connection between your computer and the cluster.
6.  Install the Kubernetes command-line tool, kubectl, and configure access to your cluster. See [Accessing your cluster from the kubectl CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_cluster/cfc_cli.html).
7.  Obtain access to the boot node and the cluster administrator account, or request that someone with that access level create your certificate. If you cannot access the cluster administrator account, you need an IBM Cloud Private account that is assigned to the operator or administrator role for a team and can access the kube-system namespace.
8.  Set up the Helm command-line interface.

    See [Setting up the Helm CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/create_helm_cli.html) for details.

    1.  Initialize the Helm command-line interface.

        ```bash
        helm init --client-only
        ```
        {: codeblock}

        **Important**: Do not use the `--upgrade` flag with the `init` command. Adding the `--upgrade` flag replaces the server version of Helm Tiller that is installed with {{site.data.keyword.icpfull_notm}}.

    2.  You can confirm the version numbers by running the following command:

        ```bash
        helm version --tls
        ```
        {: codeblock}

        The response indicates version numbers similar to these:

        ```bash
        Client: &version.Version{SemVer:"v2.9.1" ... }
        Server: &version.Version{SemVer:"v2.9.1+icp" ... }
        ```
        {: codeblock}

## Step 3: Add the Helm chart to the cloud repository
{: #add-chart-to-icp}

1.  Log in to your cluster from the {{site.data.keyword.icpfull_notm}} CLI, and log in to the Docker private image registry.

    ```bash
    cloudctl login -a https://{icp_url}:8443 --skip-ssl-validation
    docker login {icp_url}:8500
    ```
    {: codeblock}

    Where {icp_url} is the certificate authority (CA) domain. See [Specifying your own certificate authority (CA) for {{site.data.keyword.icpfull_notm}} services ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/create_ca_cert.html).

2.  Before you load the archive into your cluster, you need to run the following commands. Replace `{path_to_PPA_archive}` with the path to the PPA archive that you downloaded in [Step 1](#download-artifacts).

    ```bash
    mkdir extracted_archive
    tar -xzvf {path_to_PPA_archive} -C extracted_archive
    docker load -i extracted_archive/images/71fa7369f43748b8c5e3578aa46141502da770a967455d1071498b714bb0f089.tar.gz
    docker tag watson_nlu/curl:4.0.0 radial/busyboxplus:curl
    rm -rf extracted_archive
    ```
    {: codeblock}

3.  Run the following command to load the archive into your cluster.

    ```bash
    cloudctl catalog load-archive --archive {path_to_PPA_archive} --registry {icp_url}:8500
    ```
    {: pre}


## Step 4: Install the service
{: #install-the-service}

- [4.1 Create a namespace](#create-namespace)
- [4.2 Configure Minio Object Storage](#configure-minio)
- [4.3 Create secrets for Postgres and RabbitMQ datastores](#create-secrets-for-postgres-rabbitmq)
- [4.4 Install the service from the catalog](#install-from-catalog)

### 4.1 Create a namespace
{: #create-namespace}

First, you need to create the Kubernetes namespace where {{site.data.keyword.speechtotextshort}}: Customer Care will be installed. You can create a namespace from the {{site.data.keyword.icpfull_notm}} Console by clicking on the `Manage` menu and then choosing `Namespaces` followed by `Create Namespace`. You can also use `kubectl` on the command-line: `kubectl create -f <namespace-file>`.

The following example namespace file can be used to create the namespace `speech-services`.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: speech-services
```
{: codeblock}

### 4.2 Configure Minio Object Storage
{: #configure-minio}

By default, Minio Object Storage (which is used for binary storage) is included in the installation. However, you can choose to use your own Minio installation instead. The following sections describe both options.

#### Using the default Minio installation
{: #use-default-minio-installation}

The default Minio service needs a persistent volume for storing data. Create a storage class named `local-storage` by copying the following YAML code to a file and running `kubectl apply -f <file>`:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```
{: codeblock}

Create a persistent volume by using the previously created storage class. Copy the following YAML code to a file and run `kubectl apply -f <file>`:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: speech-minio-pv
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 100Gi
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  hostPath:
    path: /mnt/local-storage/speech/minio/pv
    type: ""
```
{: codeblock}

Note that the storage size, `storage:`, must be at least the same size that you specify during installation. See the [Storage size calculation guidelines](#storage-guidelines) section for information about the storage space that is required to install this product.

Before you install {{site.data.keyword.speechtotextshort}}: Customer Care, you need to provide a secret object that is used by Minio itself and by other service components that interact with Minio. This secret contains the security keys to access Minio.

The secret must contain the items `accesskey` (5 - 20 characters) and `secretkey` (8 - 40 characters) in base64 encoding. Therefore, before creating the secret, you need to perform the base64 enconding.

The following commands encode the accesskey and secretkey in base64. For security reasons, you are highly encouraged to create an accesskey and a secretkey that are different from the sample keys (`admin` and `admin1234`) that are shown in these examples.
{: important}

```bash
echo -n "admin" | base64
YWRtaW4=

echo -n "admin1234" | base64
YWRtaW4xMjM0
```
{: codeblock}

Create the following secret object with the base64 encoded keys:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: minio
type: Opaque
data:
  accesskey: YWRtaW4=
  secretkey: YWRtaW4xMjM0
```
{: codeblock}

You can create this secret object through the ICP Console by clicking on the configuration and the `Secrets` menu. You can also use the `kubectl` command-line tool: `kubectl create -f <secrets-file>`.

In the configuration page of the Minio Chart, before installation, you also need to configure the following:

-   Check the `Use the Default Minio Installation` option [`global.datastore.minio.enabled=true`].
-   Pass the name of the secret object that contains the keys (for example, `minio` from the previous example) in the following fields:
    -   In the `Global Settings | Minio Access Secret Name` field [`global.datastores.minio.secretName=minio`]
    -   In the `Minio Object Storage | Minio Access Secret Name` field [`external.minio.minioAccessSercret=minio`]
-   Under `Minio Object Storage | Persistent Volume Size`, enter `100Gi` [`external.minio.persistence.size=100Gi`]. See the [Storage size calculation guidelines](#storage-guidelines) section for information about the storage space that is required to install this product.

Optionally, you can choose to set Minio server mode to `distributed` if you need high availability:

-   Select the `distributed` option in `Minio Object Storage | Minio Server Mode` [`external.minio.mode=distributed`].
-   Set the number of replicas for distributed mode `Minio Object Storage | Number of Replicas`. This value is the number of cluster nodes; it must be greater than 4 and less than 32. [`externam.minio.replicas=4`].

#### Using your own Minio installation
{: #use-existing-minio-installation}

If you want to use your own installation of Minio instead of the default installation, you need to set the following parameters at configuration time:

-   Disable the default Minio installation on the configuration page by unchecking `Global Settings | Use the Default Minio Installation`. This change corresponds to the value [`global.datastores.minio.enabled=false`].
-   Provide the endpoint for your Minio instance in `Global Settings | User Installed Minio Endpoint`. The endpoint must be in the following format: `http://<services>.<namespace>:<port>`, where
    -   `service` is the Minio Kubernetes service that was created during the Minio installation.
    -   `namespace` is the namespace into which Minio was installed, which can be different from the namespace in which {{site.data.keyword.speechtotextshort}}: Customer Care is installed.
    -   `port` is the port at which Minio is listening (the default is `9000`). For example: `http://my-minio-ibm-minio-objectstorage.default:9000`.

Provide Minio with sufficient storage space for the {{site.data.keyword.speechtotextshort}}: Customer Care service installation itself in addition to other objects that you want to store in Minio. See the [Storage size calculation guidelines](#storage-guidelines) section for detailed information about the space that is needed for the {{site.data.keyword.speechtotextshort}}: Customer Care service.

#### Storage size calculation guidelines
{: #storage-guidelines}

Object storage is used for storing binary data from the following sources:

-   Base models (for example `en-US_NarrowbandModel`)

    On average, base models are each `1.5 GB`. Because models are updated regularly, you need to multiply that amount by three to make room for at least three different versions of each model.
-   Customization data (audio files and training snapshots)

    The storage required for customization data depends on how many hours of audio you use for training your custom models. On average, one hour of audio data needs `0.5 GB` of storage space. You can have multiple customizations, so you must factor in additional space.
-   Audio files from recognition jobs that are processed asynchronously, in case they need to be queued

    The storage required for asynchronous jobs depends on the use case. If you plan to submit large batches of audio files, expect the service to queue some jobs temporarily. This means that some audio files will live temporarily in binary storage. The amount of storage required for this purpose will not exceed the size of the largest batch of jobs that you plan to submit in parallel.

A few examples of how to calculate storage size [GB] follow:

-   6 models, 3 versions, 50 hours audio = `6 * 1.5 * 3 + 50 * 0.5 = 52`
-   2 models, 3 versions, 20 hours audio = `2 * 1.5 * 3 + 20 * 0.5 = 19`

The default storage size, `100 GB`, is a minimal starting point and should be enough for operations with a few (2 - 6) models and about 50 hours of audio for the purpose of training custom models. That said, it is always a good idea to be generous in anticipation of future storage needs.


### 4.3 Create secrets for Postgres and RabbitMQ datastores
{: #create-secrets-for-postgres-rabbitmq}

You must create the following secret object before installation. The object contains the username and password that will be used to access the Postgres datastore that is included in the chart. Before creating the secret, you need to produce a base64 encoding of the following file `postgres-secrets.properties`.

While the username needs to be set to `stolon`, for security reasons you are strongly encouraged to create a password that is different from the one that is used in the example (`stolon`).
{: important}

```
compose.postgres.username = stolon
compose.postgres.password = stolon
```
{: codeblock}

```bash
base64 postgres-secrets.properties
ICAgIGNvbXBvc2UucG9zdGdyZXMudXNlcm5hbWUgPSBzdG9sb24KICAgIGNvbXBvc2UucG9zdGdyZXMucGFzc3dvcmQgPSBzdG9sb24KCg==
```
{: codeblock}

Set the resulting base64 string as the value of `data.postgres-secrets.properties` in a new secret object.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: datastores-secrets
type: Opaque
data:
  postgres-secrets.properties: ICAgIGNvbXBvc2UucG9zdGdyZXMudXNlcm5hbWUgPSBzdG9sb24KICAgIGNvbXBvc2UucG9zdGdyZXMucGFzc3dvcmQgPSBzdG9sb24KCg==
```
{: codeblock}

To create this secret object, use the {{site.data.keyword.icpfull_notm}} Console or the command `kubectl create -f <secrets-file>`.

#### Share the user credentials with the Postgres chart
{: #share-credentials-with-postgres}

The Postgres chart reads the credentials from the following secret file. You need to set the attribute `data.pg_su_password` to the Postgres password that you set (and encoded as base64) in `data.compose.postgres.password` in the previous step. You can set the attribute `pg_repl_password`, which is the replication password and is also base64 encoded, to any value.

```yaml
apiVersion: v1
data:
  pg_repl_password: cmVwbHVzZXI=
  pg_su_password: c3RvbG9u
kind: Secret
metadata:
  name: user-provided-postgressql # this name can be anything you choose
type: Opaque
```
{: codeblock}

You will need to set the name of this secret object, for example `user-provided-postgressql`, in the configuration field named `PostgreSQL name of secrets object containing credentials` when you configure the installation. If this secret object is not created, a secret object that contains randomly generated passwords is created automatically, and you will need to be change these generated passwords once the deployment is complete.


### 4.4 Install the service from the catalog
{: #install-from-catalog}

Installing the Helm chart deploys a single Speech to Text: Customer Care solution with the **development configuration**. You can then [scale up this configuration](#standard-install-requirements) to the **production configuration** to support up to 50 concurrent recognition sessions.

1.  From the {{site.data.keyword.icpfull_notm}} management console navigation menu, click **Manage** > **Helm Repositories**.

2.  Click **Sync Repositories**.

    You must have the *cluster administrator* user type or access level to sync repositories.

3.  From the navigation menu, select **Catalog**.
4.  Click the **ibm-watson-speech-prod** package, and then click **Configure**.
5.  Specify values for the installation details fields.

    - Helm release name
    - Target namespace

6.  Click **License agreement**.

    Click **Next** multiple times to read the full agreement, and then click **OK** to close the license terms. If you have read and agree to the license agreement, select the checkbox.

7.  When you install the service, many configuration settings are applied to it for you unless you override them with your own values. Make sure that the following match what is required from previous steps:

    - Minio secret and persistent volume storage size (from [Step 4.2](#configure-minio))
    - PostgreSQL name of secrets object containing credentials (from [Step 4.3](#share-credentials-with-postgres))
    - Configure the speech models to include with your installation. The default installation includes only the English speech models.

8.  You might want to change additional configuration settings to specify things like user names and passwords for databases or stores that are created for you, for example. **You cannot change these settings after you complete the installation.**

    See [Configuration details](#configuration-details) for help understanding the configuration choices.

9.  Click **Install**.

### Installing from the CLI
{: #install-from-cli}

Optionally, you can install the chart from the CLI instead of from the catalog with the following command.

```bash
$ helm install --tls --name {my-release} -f {my-values.yaml} ibm-watson-speech-prod
```
{: codeblock}

-   Replace `{my_release}` with a name for your release.
-   Replace `{my-values.yaml}` with the path to a YAML file that specifies the configuration details that are to be used with the `install` command. Specifying a YAML file is optional. You can also specify individual configurations with the `--set` command.

```bash
helm install --tls --set image.repository={my_image} --name {my-release} ibm-watson-speech-prod
```
{: codeblock}

When the command completes, its output shows the current status of the release.

After completing the installation, manually change any auto-generated password or certificate. Otherwise, the Helm command-line interface allows users with the operator role to see the password or certificate. This is a security risk.
{: important}

### Configuration details
{: #configuration-details}

The following sections explain the configuration settings for {{site.data.keyword.speechtotextshort}}: Customer Care.

#### Language model selection
{: #language-model-selection}

You can perform an installation that includes only a subset of the language models in the catalog. Installing all of the models in the catalog substantially increases the memory requirements. Therefore, it is strongly recommended that you install only those languages that you will use.

You can select the languages to be installed by checking/unchecking each of the models in `Global settings | <model-name> Model` options. By default, the dynamic resource calculation feature is enabled; it automatically computes the exact amount of memory that is required for the selected models.

#### Storage of customer data (STT Runtime and AMC Patcher)
{: #storage-of-customer-data}

By default, payload data, including audio files, recognition hypotheses, and annotations, are temporarily stored in the running container. You can disable this behavior by checking `STT Runtime | Disable storage of customer data` option. Checking this option also removes sensitive information from container logs.

#### Dynamic resource calculation
{: #dynamic-resource-calculation}

For the `speech-to-text-stt-runtime` and `speech-to-text-stt-am-patcher` deployments, you can enable automatic resource calculation, which is based on the selected number of CPUs and the selected language models. Automatic resource calculation is enabled by default. You can modify this behavior by checking/unchecking `STT Runtime | Dynamic Memory Calculation (STT Runtime)` and `STT Runtime | Dynamic Memory Calculation (AMC Patcher)`, respectively.

#### Options reference
{: #options-reference}

The following options apply to a Speech to Text: Customer Care runtime configuration.

| Value                                                   | Description                                              | Default             |
|---------------------------------------------------------|----------------------------------------------------------|---------------------|
| `chuck.anonymizeLogs`                                     | Opt out of runtime logs and audio data.                                                                                                                                                                                                               | `False`                                                   |
| `chuck.groups.sttAMPatcher.resources.dynamicMemory`       | Calculate memory requirements for STT AMC patcher according to selected models. For more information, see the chart Overview.                                                                                                                                     | `True`                                                    |
| `chuck.groups.sttAMPatcher.resources.requestsCpu`         | Every customization session needs 4 CPUs.                                                                                                                                                                                                             | `8`                                                       |
| `chuck.groups.sttAMPatcher.resources.requestsMemory`      | Amount of memory depends on number of CPUs. Size can be calculated as #CPUs * 3 GB.                                                                                                                                                                    | `22000Mi`                                                 |
| `chuck.groups.sttAMPatcher.resources.threads`             | Number of parallel-processing threads for AMC. Note that fewer threads means longer training time.                                                                                                                                                    | `4`                                                       |
| `chuck.groups.sttRuntimeDefault.resources.dynamicMemory`  | Calculate memory requirements for STT runtime according to selected models. For more information, see the chart Overview.                                                                                                                                         | `True`                                                    |
| `chuck.groups.sttRuntimeDefault.resources.requestsCpu`    | Requested CPUs for STT runtime. Minimal value is 4.                                                                                                                                                                                                   | `8`                                                       |
| `chuck.groups.sttRuntimeDefault.resources.requestsMemory` | Calculation of the memory requirements can be found in the chart Overview. When dynamic memory is enabled, this option has no effect.                                                                                                                 | `22000Mi`                                                 |
| `external.minio.minioAccessSercret`                       | Create a secret that contains base64-encoded accesskey (5 - 20 characters) and secretkey (8 - 40 characters). The keys are used to access the Minio Object Server. You need to create the secret in the same namespace in which you deploy the chart. | `minio`                                                   |
| `external.minio.mode`                                     | Sets Minio server mode.                                                                                                                                                                                                                               | `standalone`                                              |
| `external.minio.persistence.size`                         | Size of persistent volume claim (PVC).                                                                                                                                                                                                                | `100Gi`                                                   |
| `external.minio.replicas`                                 | Number of nodes (applicable only for Minio distributed mode). Must be 4 <= x <= 32.                                                                                                                                                                   | `4`                                                       |
| `global.datastores.minio.enabled`                         | Minio is part of the installation. If you install Minio yourself, you must provide the endpoint for your Minio installation.                                                                                                                  | `True`                                                            |
| `global.datastores.minio.endpoint`                        | Endpoint in form 'http://{minio-service}.{namespace}:{port}'.                                                                                                                                                                                          | `http://minio-ibm-minio-objectstore.speech-services:9000` |
| `global.datastores.minio.secretName`                      | Minio object storage access secret name created as an installation prerequisite.                                                                                                                                                                      | `minio`                                                   |
| `global.sttModels.enUsBroadbandModel.enabled`             | Enables/disables en-US Broadband Model for the service.                                                                                                                                                                                               | `True`                                                    |
| `global.sttModels.enUsNarrowbandModel.enabled`            | Enables/disables en-US Narrowband Model for the service.                                                                                                                                                                                              | `True`                                                    |
| `global.sttModels.enUSShortFormNarrowbandModel.enabled`            | Enables/disables en-US Short Form Narrowband Model for the service.                                                                                                                                                                                              | `True`                                                    |
| `global.sttModels.jaJpBroadbandModel.enabled`             | Enables/disables jp-JP Broadband Model for the service.                                                                                                                                                                                               | `False`                                                    |
| `global.sttModels.jaJpNarrowbandModel.enabled`            | Enables/disables jp-JP Narrowband Model for the service.                                                                                                                                                                                              | `False`                                                    |
| `global.sttModels.koKrBroadbandModel.enabled`             | Enables/disables ko-KR Broadband Model for the service.                                                                                                                                                                                               | `False`                                                    |
| `global.sttModels.koKrNarrowbandModel.enabled`            | Enables/disables ko-KR Narrowband Model for the service.                                                                                                                                                                                              | `False`                                                    |
| `global.sttModels.esEsBroadbandModel.enabled`            | Enables/disables es-ES Narrowband Model for the service.                                                                                                                                                                                              | `False`                                                    |
| `global.sttModels.esEsNarrowbandModel.enabled`            | Enables/disables es-ES Narrowband Model for the service.                                                                                                                                                                                              | `False`                                                    |
| `global.sttModels.frFrBroadbandModel.enabled`            | Enables/disables fr-FR Narrowband Model for the service.                                                                                                                                                                                              | `False`                                                    |
| `global.sttModels.frFrNarrowbandModel.enabled`            | Enables/disables fr-FR Narrowband Model for the service.                                                                                                                                                                                              | `False`                                                    |
| `postgressql.auth.authSecretName`                         | PostgresSQL name of the secrets object that contains the credentials to access the datastore.                                                                                                                                                         | `user-provided-postgressql`                               |

### Uninstalling the Chart
{: #uninstall}

To uninstall and delete the `my-release` deployment, run the following command:

```bash
$ helm delete --tls my-release
```

To irrevocably uninstall and delete the `my-release` deployment, run the following command:

```bash
$ helm delete --purge --tls my-release
```

If you omit the `--purge` option, Helm deletes all resources for the deployment but retains the record with the release name. This allows you to roll back the deletion. If you include the `--purge` option, Helm removes all records for the deployment so that the name can be used for another installation.

## Required resources details
{: #required-resources-details}

### Resources required by the standard installation
{: #standard-install-requirements}

While the default installation of the {{site.data.keyword.speechtotextshort}}: Customer Care solution comes with the **development configuration**, you can obtain the **production configuration** by scaling up the number of pods/replicas of the deployment objects after installing the solution. You can learn the list of deployments (Kubernetes `Deployment` objects) by running the command `kubectl get deployment`. You must then scale up the number of pods on each of these deployment objects to match the number of pods in the production configuration, as shown in the following table. You can do this by using the following command: `kubectl scale --replicas=<n> deployment <deployment-object>`, where `<n>` is the desired number of replicas for the given deployment (`<deployment-name>`).

| Deployment              | # pods dev config | # pods prod config |
|-------------------------|-------------------|--------------------|
| {release}-speech-to-text-stt-runtime          |  1 | 4 |
| {release}-speech-to-text-stt-customization    |  1 | 2 |
| {release}-speech-to-text-stt-am-patcher       |  1 | 1 |
| {release}-speech-to-text-stt-async            |  1 | 2 |
| {release}-speech-to-text-gdpr-data-deletion   |  1 | 2 |
| {release}-minio-objectstore                   |  1 | 1 |
| {release}-rabbitmq                            |  1 | 1 |
| {release}-postgressql-proxy                   |  2 | 2 |
| {release}-postgressql-sentinel                |  3 | 3 |
| {release}-postgressql-keeper                  |  3 | 3 |

The standard installation requires the following resources:

-   A total of **14.95** CPUs and **36** GBs of memory for the development configuration
-   A total of **47.55** CPUs and **103** GBs of memory for the production configuration

You can obtain these numbers by multiplying the number of pods in the previous table by the CPUs and memory that are required by each pod; the tables in the following sections provide this information. These numbers are based on a standard installation that includes the US English models only, which results in memory requirements of 18.5 GB for each of the `speech-to-text-stt-runtime` and `speech-to-text-stt-am-patcher` instances. In general, the memory requirements vary depending on which models you include in the installation.

### Additional resources required by the Speech Analytics feature (optional)
{: #speech-analytics-requirements}

When the Speech Analytics feature is installed, you need to add additional CPU and memory requirements to those of the standard installation. When the Speech Analytics feature is selected, the default installation comes with the **development configuration**. You can easily obtain the **production configuration** by scaling up the number of pods/replicas of the deployment objects after installing the solution. The following table shows the number of pods/replicas that are required for each deployment object for both the development and production configurations.

| Deployment                 | # pods dev config | # pods prod config | Notes |
|----------------------------|-------------------|--------------------|-------|
| {release}-ibm-sentiment-sentiment-analysis-en-cc  | 1 | 1 |  |
| {release}-ibm-watson-nlp-en                       | 1 | 1 |  |
| {release}-ibm-watson-nlu-prod-keywords            | 1 | 1 |  |
| {release}-ibm-watson-nlu-prod-nluserver           | 1 | 1 |  |
| {release}-ibm-watson-nlu-prod-orchestrator        | 1 | 1 |  |
| {release}-ibm-watson-tone-tone-analysis-en        | 1 | 1 |  |
| {release}-speech-to-text-speech-analytics-ui      | 1 | 1 |  |
| {release}-speech-to-text-stt-speech-hub           | 1 | 2 |  |
| {release}-topic-modelling-runtime                 | 1 | 1 |  |
| {release}-speech-to-text-stt-runtime              | n/a | 1 | extra instance |

When you select the Speech Analytics feature, the additional resources required are as follows.

-   **17** CPUs and **52** GBs of memory for the development configuration
-   **32** CPUs and **80.5** GBs of memory for the production configuration

The total required resources of the standard installation

-   **31.95** CPUs and **88** GBs of memory for the development configuration
-   **79.55** CPUs and **183.5** GBs of memory for the production configuration

### CPU and memory requirements per pod/replica
{: #requirements-per-pod}

For the standard installation.

| Deployment                                       | CPU   | Memory [GB] |
|--------------------------------------------------|-------|-------------|
| {release}-speech-to-text-stt-runtime             | 4     | 11.5        |
| {release}-speech-to-text-stt-customization       | 2     | 2           |
| {release}-speech-to-text-stt-am-patcher          | 4     | 11.5        |
| {release}-speech-to-text-stt-async               | 2     | 2           |
| {release}-speech-to-text-gdpr-data-deletion      | 0.2   | 0.5         |
| {release}-minio-objectstore                      | 0.25  | 0.25        |
| {release}-rabbitmq                               | 0.1   | 0.25        |
| {release}-postgressql-proxy                      | 0.3   | 1           |
| {release}-postgressql-sentinel                   | 0.3   | 1           |
| {release}-postgressql-keeper                     | 0.3   | 1           |

For the Speech Analytics feature.

| Deployment                                        | CPU               | Memory [GB]        |
|---------------------------------------------------|-------------------|--------------------|
| {release}-ibm-sentiment-sentiment-analysis-en-cc  | 5.5 | 12 |
| {release}-ibm-watson-nlp-en                       | 0.5 | 1  |
| {release}-ibm-watson-nlu-prod-keywords            | 0.5 | 2  |
| {release}-ibm-watson-nlu-prod-nluserver           | 0.2 | 1  |
| {release}-ibm-watson-nlu-prod-orchestrator        | 0.3 | 1  |
| {release}-ibm-watson-tone-tone-analysis-en        | 2   | 8  |
| {release}-speech-to-text-speech-analytics-ui      | 1   | 1  |
| {release}-speech-to-text-stt-speech-hub           | 2   | 10 |
| {release}-topic-modelling-runtime                 | 10  | 16 |
