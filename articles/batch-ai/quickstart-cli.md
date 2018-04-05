---
title: Guida introduttiva di Azure - Training di CNTK con Batch per intelligenza artificiale - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Imparare velocemente come eseguire un processo di training di CNTK con Batch per intelligenza artificiale usando l'interfaccia della riga di comando di Azure
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Eseguire un processo di training di CNTK usando l'interfaccia della riga di comando di Azure

Questa guida introduttiva illustra nel dettaglio l'uso dell'interfaccia della riga di comando di Azure per eseguire un processo di training di Microsoft Cognitive Toolkit (CNTK) tramite il servizio Batch per intelligenza artificiale. L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script.

In questo esempio si usa il database MNIST di immagini manoscritte per il training di una rete neurale convoluzionale (CNN) in un cluster GPU a nodo singolo gestito da Batch per intelligenza artificiale. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure più recente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

Anche i provider di risorse di Batch per intelligenza artificiale devono essere registrati una volta per la sottoscrizione dell'utente usando Azure Cloud Shell o l'interfaccia della riga di comando di Azure. La registrazione di un provider può richiedere fino a 15 minuti.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

I cluster e i processi di Batch per intelligenza artificiale sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*. Usa quindi il comando [az configure](/cli/azure/reference-index#az_configure) per impostare questo gruppo di risorse e la sua posizione come valori predefiniti.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>L'impostazione dei valori predefiniti per il comando `az` è un passaggio facoltativo. È possibile scegliere di non impostare i valori predefiniti. Se si sceglie di impostare i valori predefiniti, sarà necessario rimuoverli al termine dell'esercitazione. Per rimuovere le impostazioni predefinite usare i comandi seguenti:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Questa guida introduttiva usa un account di archiviazione di Azure per ospitare dati e script per il processo di training. Creare un account di archiviazione con il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Ogni account di archiviazione deve avere un nome univoco. Nel precedente comando `az` e in altri comandi simili in questa esercitazione sostituire il valore dell'impostazione `mystorageaccount` con il nome del proprio account di archiviazione.

## <a name="prepare-azure-file-share"></a>Preparare la condivisione file di Azure

A scopo illustrativo, questa guida introduttiva usa una condivisione file di Azure per ospitare i dati e gli script di training per il processo Learning.

1. Creare una condivisione file denominata *batchaiquickstart* usando il comando [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. Creare una directory nella condivisione denominata *mnistcntksample* usando il comando [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Scaricare il [pacchetto di esempio](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) e decomprimerlo. Caricarne il contenuto nella directory usando il comando [az storage file upload](/cli/azure/storage/file#az_storage_file_upload):

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Creare il cluster GPU
Usare il comando [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) per creare un cluster Batch per intelligenza artificiale costituito da un singolo nodo VM GPU. In questo esempio la macchina virtuale esegue l'immagine Ubuntu LTS predefinita. Specificare invece `image UbuntuDSVM` per eseguire la macchina virtuale per Microsoft Deep Learning, che supporta framework di training aggiuntivi. La dimensione NC6 ha una GPU NVIDIA K80. Montare la condivisione di file in una cartella denominata *azurefileshare*. Il percorso completo della cartella nel nodo di calcolo GPU è $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


Dopo la creazione del cluster, l'output è simile al seguente:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Ottenere lo stato del cluster

Per ottenere una panoramica dello stato del cluster, eseguire il comando [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list):

```azurecli
az batchai cluster list -o table
```

L'output è simile al seguente:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Per informazioni dettagliate, eseguire il comando [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show), che restituisce tutte le proprietà del cluster mostrate dopo la sua creazione.

Il cluster è pronto quando i nodi sono allocati e la preparazione è completata (vedere l'attributo `nodeStateCounts`). Se si è verificato un errore, l'attributo `errors` contiene la descrizione dell'errore.

## <a name="create-training-job"></a>Creare il processo di training

Quando il cluster è pronto, configurare e inviare il processo Learning.

1. Creare un file modello JSON per la creazione del processo denominato job.json:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Creare un processo denominato *myjob* da eseguire nel cluster con il comando [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create):

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

L'output è simile al seguente:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Monitorare il processo

Usare il comando [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) per ottenere una panoramica dello stato del processo:

```azurecli
az batchai job list -o table
```

L'output è simile al seguente:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Per informazioni dettagliate, eseguire il comando [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

`executionState` contiene lo stato di esecuzione corrente del processo:

* `queued`: il processo è in attesa che i nodi del cluster diventino disponibili
* `running`: il processo è in esecuzione
*   `succeeded` (o `failed`): il processo è completato e `executionInfo` contiene i dettagli del risultato


## <a name="list-stdout-and-stderr-output"></a>Elencare l'output di stdout e stderr
Usare il comando [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) per elencare i collegamenti ai file di log stdout e stderr:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

L'output è simile al seguente:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Esaminare l'output

È possibile eseguire lo streaming o il tail dei file di output di un processo mentre questo è in esecuzione. L'esempio che segue usa il comando [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) per eseguire lo streaming del log stderr.txt:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

L'output è simile al seguente. Per interrompere l'output premere CTRL+C.

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Eliminare le risorse

Usare il comando [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) per eliminare il processo:

```azurecli
az batchai job delete --name myjob
```
Usare il comando [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) per eliminare il cluster:

```azurecli
az batchai cluster delete --name mycluster
```

Usare il comando `az group delete` per eliminare il gruppo di risorse creato per questa guida introduttiva:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Ripristinare le impostazioni predefinite dell'interfaccia della riga di comando di Azure 2.0

Rimuovere le impostazioni predefinite configurate in precedenza per la posizione e il gruppo di risorse:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come eseguire un processo di training di CNTK in un cluster di Batch per intelligenza artificiale usando l'interfaccia della riga di comando di Azure. Per altre informazioni sull'uso di Batch per intelligenza artificiale con diversi toolkit, vedere le [recipe di training](https://github.com/Azure/BatchAI).

Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure 2.0 per la gestione di Batch per intelligenza artificiale, vedere la [documentazione di GitHub](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
