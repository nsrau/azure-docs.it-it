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
ms.date: 06/14/2018
ms.author: Alexander.Yukhanov
ms.openlocfilehash: eb00c1d4ec74b5268a1497b11087030ab6a86e5a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294074"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Eseguire un processo di training di CNTK usando l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure 2.0 consente di creare e gestire risorse Batch per intelligenza artificiale, ovvero creare/eliminare cluster e file server Batch per intelligenza artificiale e di inviare/terminare/eliminare/eseguire il monitoraggio di processi di training.

Questa guida introduttiva illustra come creare un cluster GPU ed eseguire un processo di training con Microsoft Cognitive Toolkit (CNTK).

Lo script di training [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) è disponibile nella pagina GitHub di Batch per intelligenza artificiale. Questo script consente di eseguire il training di una rete neurale convoluzionale nel database MNIST di cifre scritte a mano.

L'esempio ufficiale di CNTK è stato modificato in modo da accettare il percorso del set di dati di training e il percorso della directory di output tramite gli argomenti della riga di comando.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

* Creare un cluster GPU a nodo singolo (con dimensioni della macchina virtuale `Standard_NC6`) denominato `nc6`.
* Creare un account di archiviazione per archiviare input e output del processo.
* Creare una condivisione file di Azure con due cartelle `logs` e `scripts` per archiviare l'output dei processi e gli script di training.
* Creare un contenitore BLOB di Azure `data` per archiviare i dati di training.
* Distribuire lo script di training e i dati di training nella condivisione file e nel contenitore creati.
* Configurare il processo per montare la condivisione file di Azure e il contenitore BLOB di Azure nel nodo del cluster e renderli disponibili come file system normale in `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` e `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` è una variabile di ambiente impostata per il processo da Batch per intelligenza artificiale.
* Eseguire il monitoraggio dell'esecuzione del processo trasmettendo il flusso dell'output standard.
* Al completamento del processo, esaminarne l'output e i modelli generati.
* Al termine eliminare tutte le risorse allocate.

## <a name="prerequisites"></a>prerequisiti

* Sottoscrizione di Azure - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Accesso all'interfaccia della riga di comando di Azure 2.0 versione 0.3 o successiva con il modulo batchai. È possibile usare l'interfaccia della riga di comando di Azure 2.0 disponibile in [Azure Cloud Shell](../cloud-shell/overview.md) o installarla in locale seguendo [queste istruzioni](/cli/azure/install-azure-cli?view=azure-cli-latest).

  Se si usa Cloud Shell, modificare la directory di lavoro in `/usr/$USER/clouddrive` perché la home directory non contiene spazio vuoto:

  ```azurecli
  cd /usr/$USER/clouddrive
  ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico per la distribuzione e la gestione delle risorse di Azure. Il comando seguente consente di creare un nuovo gruppo di risorse `batchai.quickstart` nella località Stati Uniti orientali:

```azurecli
az group create -n batchai.quickstart -l eastus
```
## <a name="create-batch-ai-workspace"></a>Creare un'area di lavoro di Azure Batch per intelligenza artificiale

Il comando seguente crea un'area di lavoro di Azure Batch per intelligenza artificiale nel gruppo di risorse. Un'area di lavoro di Azure Batch per intelligenza artificiale è una raccolta di primo livello di tutti i tipi di risorse di Batch per intelligenza artificiale:

```azurecli
az batchai workspace create -g batchai.quickstart -n quickstart
```

## <a name="create-gpu-cluster"></a>Creare il cluster GPU

Il comando seguente crea un cluster GPU a nodo singolo (la dimensione della VM è Standard_NC6) nell'area di lavoro, usando la macchina virtuale di data science di Ubuntu (DSVM) come immagine del sistema operativo:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -w quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

La DSVM di Ubuntu consente di eseguire processi di training in contenitori Docker e di eseguire i più comuni framework di Deep Learning direttamente nella macchina virtuale.

L'opzione `--generate-ssh-keys` indica all'interfaccia della riga di comando di Azure di generare chiavi SSH pubbliche e private se non sono già disponibili. Per accedere ai nodi del cluster, è possibile usare il nome utente corrente e la chiave SSH generata.

Se si usa Cloud Shell, è consigliabile eseguire il backup della cartella ~/.ssh in un archivio permanente.

Output di esempio:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "myuser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Il comando seguente crea un account di archiviazione nello stesso gruppo di risorse usato per creare il cluster di Azure Batch per intelligenza artificiale. L'account di archiviazione viene usato per archiviare input e output del processo. Aggiornare il comando con un nome di account di archiviazione univoco.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```


## <a name="deploy-data"></a>Distribuire i dati

### <a name="download-the-training-script-and-training-data"></a>Scaricare lo script di training e i dati di training

* Scaricare ed estrarre nella cartella corrente un database MNIST pre-elaborato da [questo percorso](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D).

Per GNU/Linux o Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Tenere presente che se `unzip` non è presente nella distribuzione GNU/Linux, potrebbe essere necessario installarlo.

* Scaricare lo script di esempio [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) nella cartella corrente:

Per GNU/Linux o Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

### <a name="create-azure-file-share-and-deploy-the-training-script"></a>Creare la condivisione file di Azure e distribuire lo script di training

I comandi seguenti consentono di creare le condivisioni file di Azure `scripts` e `logs` e di copiare lo script di training nella cartella `cntk` all'interno della condivisione `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

### <a name="create-a-blob-container-and-deploy-training-data"></a>Creare un contenitore BLOB e distribuire i dati di training

I comandi seguenti consentono di creare il contenitore BLOB di Azure `data` e di copiare i dati di training nella cartella `mnist_cntk`:

```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

## <a name="submit-training-job"></a>Inviare il processo di training

### <a name="create-a-batch-ai-experiment"></a>Creare un esperimento Batch per intelligenza artificiale

Un esperimento è un contenitore logico per i processi Batch per intelligenza artificiale correlati. Usare il comando seguente per creare un esperimento nell'area di lavoro:

```azurecli
az batchai experiment create -g batchai.quickstart -w quickstart -n quickstart
```

### <a name="prepare-job-configuration-file"></a>Preparare il file di configurazione del processo

Creare un file di configurazione del processo di training `job.json` con il contenuto seguente. Aggiornare con il nome del proprio account di archiviazione.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<YOUR_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Questo file di configurazione specifica:

* `nodeCount`: numero di nodi richiesti dal processo (1 per questa guida introduttiva)
* `cntkSettings`: percorso dello script di training e argomenti della riga di comando. Gli argomenti della riga di comando includono il percorso dei dati di training e il percorso di destinazione per l'archiviazione dei modelli generati. `AZ_BATCHAI_OUTPUT_MODEL` è una variabile di ambiente impostata da Batch per intelligenza artificiale sulla base della configurazione della directory di output (vedere di seguito).
* `stdOutErrPathPrefix`: percorso in cui Batch per intelligenza artificiale crea le directory contenenti output e log del processo.
* `outputDirectories`: raccolta di directory di output che verranno create da Batch per intelligenza artificiale. Per ogni directory, Batch per intelligenza artificiale crea una variabile di ambiente denominata `AZ_BATCHAI_OUTPUT_<id>`, in cui `<id>` è l'identificatore di directory
* `mountVolumes`: elenco di file system da montare durante l'esecuzione del processo. I file system vengono montati in `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` è una variabile di ambiente impostata da Batch per intelligenza artificiale.
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>`: nome dell'account di archiviazione da specificare durante l'invio del processo tramite la variabile di ambiente `--storage-account-name parameter` o `AZURE_BATCHAI_STORAGE_ACCOUNT` nel computer.

### <a name="submit-the-job"></a>Inviare il processo

```azurecli
az batchai job create -n cntk_python_1 -c nc6 -g batchai.quickstart -w quickstart -e quickstart  -f job.json --storage-account-name <storage account name>
```

Output di esempio:
```
{
  "caffe2Settings": null,
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-06-14T22:22:57.543000+00:00",
  "customMpiSettings": null,
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "startTime": "2018-06-14T22:22:59.838000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2018-06-14T22:22:59.838000+00:00",
  "horovodSettings": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/f2d6ff09-7549-4e1a-8cd8-ec839f042a61",
  "jobPreparation": null,
  "mountVolumes": {
    "azureBlobFileSystems": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null
    }
  ],
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-06-14T22:22:58.625000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "danlep0614b",
  "schedulingPriority": "normal",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/workspaces/experiments/jobs"
}

```

## <a name="monitor-job-execution"></a>Monitorare l'esecuzione del processo

Lo script di training segnala lo stato del training nel file `stderr.txt` nella directory di output standard. Monitorare lo stato con il comando seguente:

```azurecli
az batchai job file stream -j cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart -f stderr.txt
```

Output di esempio:
```
File found with URL "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.62% * 10000
```

Il flusso si arresta al completamento del processo (riuscito o non riuscito).

## <a name="inspect-generated-model-files"></a>Esaminare i file di modello generati

Il processo memorizza i file modello generati nella directory di output con l'attributo `id` uguale a `MODEL`. Elencare i file modello e ottenere gli URL di download con il comando seguente:

```azurecli
az batchai job file list -j cntk_python_1 -w quickstart -e quickstart -g batchai.quickstart -d MODEL
```

Output di esempio:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

In alternativa, usare il portale di Azure o Azure Storage Explorer per esaminare i file generati. Per distinguere l'output dei diversi processi, Batch per intelligenza artificiale crea una struttura di cartelle univoca per ognuno di essi. Trovare il percorso della cartella contenente l'output usando l'attributo `jobOutputDirectoryPathSegment` del processo inviato:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart --query jobOutputDirectoryPathSegment
```

Output di esempio:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse allocate con il comando seguente:

```azurecli
az group delete -n batchai.quickstart -y
```
