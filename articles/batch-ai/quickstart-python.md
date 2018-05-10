---
title: Guida introduttiva di Azure - Training di CNTK con Batch per intelligenza artificiale - Python | Microsoft Docs
description: Imparare velocemente come eseguire un processo di training di CNTK con Batch per intelligenza artificiale usando Python SDK
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Eseguire un processo di training di CNTK usando Azure Python SDK

Questa guida introduttiva illustra nel dettaglio l'uso di Azure Python SDK per eseguire un processo di training di Microsoft Cognitive Toolkit (CNTK) tramite il servizio Batch per intelligenza artificiale.

In questo esempio si usa il database MNIST di immagini manoscritte per il training di una rete neurale convoluzionale (CNN) in un cluster GPU a nodo singolo.

## <a name="prerequisites"></a>prerequisiti

* Sottoscrizione di Azure - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Azure Python SDK - Vedere le [istruzioni per l'installazione](/python/azure/python-sdk-azure-install)

* Account di archiviazione di Azure - Vedere [come creare un account di archiviazione](../storage/common/storage-create-storage-account.md)

* Credenziali entità servizio di Azure Active Directory - vedere [come creare un'entità servizio con l'interfaccia della riga di comando](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Registrare il provider di risorse di Batch per intelligenza artificiale una volta per la sottoscrizione dell'utente usando Azure Cloud Shell o l'interfaccia della riga di comando di Azure. La registrazione di un provider può richiedere fino a 15 minuti.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Configurare le credenziali
Aggiungere il codice seguente nel file di script e sostituire `FILL-IN-HERE` con i valori appropriati:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

Si noti che l'inserimento delle credenziali nel codice sorgente non è una procedura consigliata e che questo approccio viene usato per semplificare la guida introduttiva.
Prendere in considerazione l'uso delle variabili di ambiente o di un file di configurazione separato.

## <a name="create-batch-ai-client"></a>Creare un client di Batch per intelligenza artificiale

Il codice seguente crea un oggetto credenziali dell'entità servizio e il client di Batch per intelligenza artificiale:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

I cluster e i processi di Batch per intelligenza artificiale sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Il frammento di codice seguente crea un gruppo di risorse:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Preparare la condivisione file di Azure
A scopo illustrativo, questa guida introduttiva usa una condivisione file di Azure per ospitare i dati e gli script di training per il processo di training.

1. Creare una condivisione file denominata `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. Creare una directory nella condivisione denominata `mnistcntksample`

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. Scaricare il [pacchetto di esempio](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) e decomprimerlo nella directory corrente. Il codice seguente carica i file necessari nella condivisione file di Azure:

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>Creare il cluster GPU

Creare un cluster di Batch per intelligenza artificiale. In questo esempio il cluster è costituito da un singolo nodo VM STANDARD_NC6. Questa dimensione di VM ha una GPU NVIDIA K80. Montare la condivisione di file in una cartella denominata `azurefileshare`. Per percorso completo di questa cartella sul nodo di calcolo GPU è `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus',
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6',
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
        admin_user_name=admin_user_name,
        admin_user_password=admin_user_password),
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=1)
    ),
    # Configure each node in the cluster
    node_setup=models.NodeSetup(
        # Mount shared volumes to the host
        mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=storage_account_key),
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Ottenere lo stato del cluster

Monitorare lo stato del cluster usando il comando seguente:

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

Il codice precedente visualizza informazioni di base sull'allocazione del cluster, come illustrato nell'esempio seguente:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

Il cluster è pronto quando i nodi sono allocati e la preparazione è completata (vedere l'attributo `nodeStateCounts`). Se si è verificato un errore, l'attributo `errors` contiene la descrizione dell'errore.

## <a name="create-training-job"></a>Creare il processo di training

Quando il cluster è stato creato, configurare e inviare il processo Learning:

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Monitorare il processo
È possibile controllare lo stato del processo usando il codice seguente:

```Python
job = batchai_client.jobs.get(resource_group_name, job_name)

print('Job state: {0} '.format(job.execution_state.name))
```

L'output è simile a: `Job state: running`.

`executionState` contiene lo stato di esecuzione corrente del processo:
* `queued`: il processo è in attesa che i nodi del cluster diventino disponibili
* `running`: il processo è in esecuzione
* `succeeded` (o `failed`): il processo è completato e `executionInfo` contiene i dettagli del risultato

## <a name="list-stdout-and-stderr-output"></a>Elencare l'output di stdout e stderr
Usare il codice seguente per elencare i file stdout, stderr e di log generati:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Elencare i file di modello generati
Usare il codice seguente per elencare i file di modello generati:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Eliminare le risorse

Usare il codice seguente per eliminare il processo:
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

Usare il codice seguente per eliminare il cluster:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

Usare il codice seguente per eliminare tutte le risorse allocate:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come eseguire un processo di training di CNTK in un cluster di Batch per intelligenza artificiale usando Azure Python SDK. Per altre informazioni sull'uso di Batch per intelligenza artificiale con diversi toolkit, vedere le [recipe di training](https://github.com/Azure/BatchAI).
