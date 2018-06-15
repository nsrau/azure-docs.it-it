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
ms.openlocfilehash: 8d2381f710e87751bd6547c7f435080f185020d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608546"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Eseguire un processo di training di CNTK usando l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure 2.0 consente di creare e gestire risorse Batch per intelligenza artificiale, ovvero creare/eliminare cluster e file server Batch per intelligenza artificiale e di inviare/terminare/eliminare/eseguire il monitoraggio di processi di training.

Questa guida introduttiva mostra come creare un cluster GPU ed eseguire un processo di training con Microsoft Cognitive Toolkit.

Lo script di training [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) è disponibile nella pagina GitHub di Batch per intelligenza artificiale. Questo script consente di eseguire il training della rete neurale convoluzionale in un database MNIST di cifre scritte a mano.

L'esempio ufficiale di CNTK è stato modificato in modo da accettare il percorso del set di dati di training e il percorso della directory di output tramite gli argomenti della riga di comando.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

* Creare un cluster GPU a nodo singolo (con dimensioni della macchina virtuale `Standard_NC6`) denominato `nc6`.
* Creare un nuovo account di archiviazione per archiviare input e output del processo.
* Creare una condivisione di file di Azure con due cartelle `logs` e `scripts` per archiviare l'output dei processi e gli script di training.
* Creare un contenitore BLOB di Azure `data` per archiviare i dati di training.
* Distribuire lo script di training e i dati di training nella condivisione file e nel contenitore creati.
* Configurare il processo per montare la condivisione file di Azure e il contenitore BLOB di Azure nel nodo del cluster e renderli disponibili come file system normale in `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` e `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` è una variabile di ambiente impostata per il processo da Batch per intelligenza artificiale.
* Eseguire il monitoraggio dell'esecuzione del processo trasmettendo il flusso dell'output standard.
* Al completamento del processo, esaminarne l'output e i modelli generati.
* Al termine eliminare tutte le risorse allocate.

# <a name="prerequisites"></a>prerequisiti

* Sottoscrizione di Azure - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Accesso all'interfaccia della riga di comando di Azure 2.0 versione 2.0.31 o successiva. È possibile usare l'interfaccia della riga di comando di Azure 2.0 disponibile in [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) o installarla in locale seguendo [queste istruzioni](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

# <a name="cloud-shell-only"></a>Solo Cloud Shell

Se si usa Cloud Shell, modificare la directory di lavoro in `/usr/$USER/clouddrive` perché la home directory non contiene spazio vuoto:

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico per la distribuzione e la gestione delle risorse di Azure. Il comando seguente consente di creare un nuovo gruppo di risorse ```batchai.quickstart``` nella località Stati Uniti orientali:

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>Creare il cluster GPU

Il comando seguente consente di creare un cluster GPU a nodo singolo (le dimensioni della macchina virtuale sono Standard_NC6) usando Ubuntu DSVM come immagine del sistema operativo:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM consente di eseguire processi di training in contenitori Docker e di eseguire i più comuni framework di apprendimento profondo direttamente nella macchina virtuale.

L'opzione `--generate-ssh-keys` indica all'interfaccia della riga di comando di Azure di generare chiavi SSH pubbliche e private se non sono già disponibili. Per accedere ai nodi del cluster, è possibile usare il nome utente corrente e la chiave SSH generata.

Tenere presente che se si usa Cloud Shell, è opportuno eseguire il backup della cartella ~/.ssh in un archivio permanente.

Output di esempio:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
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
    "adminUserName": "alex",
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

# <a name="create-a-storage-account"></a>Creare un account di archiviazione

Il comando seguente consente di creare un nuovo account di archiviazione nella stessa area del gruppo di risorse batchai.repices. Aggiornare il comando con un nome di account di archiviazione univoco.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Se il nome dell'account di archiviazione selezionato non è disponibile, il comando precedente restituirà l'errore corrispondente. In questo caso scegliere un altro nome e riprovare.

# <a name="data-deployment"></a>Distribuzione dei dati

## <a name="download-the-training-script-and-training-data"></a>Scaricare lo script di training e i dati di training

* Scaricare ed estrarre nella cartella corrente il database MNIST pre-elaborato da [questo percorso](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D).

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

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Creare la condivisione file di Azure e distribuire lo script di training

I comandi seguenti consentono di creare le condivisioni file di Azure `scripts` and `logs` e di copiare lo script di training nella cartella `cntk` all'interno della condivisione `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Creare un contenitore BLOB e distribuire i dati di training

I comandi seguenti consentono di creare il contenitore BLOB di Azure `data` e di copiare i dati di training nella cartella `mnist_cntk`:
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Inviare il processo di training

## <a name="prepare-job-configuration-file"></a>Preparare il file di configurazione del processo

Creare un file di configurazione del processo di training `job.json` con il contenuto seguente:
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
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Questo file di configurazione specifica:

* `nodeCount`: numero di nodi richiesti dal processo (1 per questa guida introduttiva).
* `cntkSettings`: percorso dello script di training e argomenti della riga di comando. Gli argomenti della riga di comando includono il percorso dei dati di training e il percorso di destinazione per l'archiviazione dei modelli generati. `AZ_BATCHAI_OUTPUT_MODEL` è una variabile di ambiente impostata da Batch per intelligenza artificiale sulla base della configurazione della directory di output (vedere di seguito).
* `stdOutErrPathPrefix`: percorso in cui Batch per intelligenza artificiale creerà le directory contenenti output e log del processo.
* `outputDirectories`: raccolta di directory di output che verranno create da Batch per intelligenza artificiale. Per ogni directory Batch per intelligenza artificiale crea una variabile di ambiente denominata `AZ_BATCHAI_OUTPUT_<id>`, in cui `<id>` è l'identificatore di directory.
* `mountVolumes`: elenco di file system da montare durante l'esecuzione del processo. I file system vengono montati in `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` è una variabile di ambiente impostata da Batch per intelligenza artificiale.
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` indica che il nome dell'account di archiviazione verrà specificato durante l'invio del processo tramite il parametro --storage-account-name o la variabile di ambiente `AZURE_BATCHAI_STORAGE_ACCOUNT` nel computer in uso.

## <a name="submit-the-job"></a>Inviare il processo

Per inviare il processo nel cluster, usare il comando seguente:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Output di esempio:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
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
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>Eseguire il monitoraggio dell'esecuzione del processo

Lo script di training segnala lo stato del training nel file `stderr.txt` all'interno della directory di output standard. Per eseguire il monitoraggio dello stato, usare il comando seguente:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Output di esempio:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
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
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

Il flusso viene arrestato al completamento del processo (riuscito o non riuscito).

# <a name="inspect-generated-model-files"></a>Esaminare i file di modello generati

Il processo archivia i file di modello generati nella directory di output con attributo `id` uguale a `MODEL`. Per visualizzare l'elenco dei file di modello e ottenere gli URL di download usando il comando seguente:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
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

In alternativa, è possibile usare il portale o Azure Storage Explorer per esaminare i file generati. Per distinguere l'output dei diversi processi, Batch per intelligenza artificiale crea una struttura di cartelle univoca per ognuno di essi. Per trovare il percorso della cartella contenente l'output, è possibile usare l'attributo `jobOutputDirectoryPathSegment` del processo inviato:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Output di esempio:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Eliminare risorse

Per eliminare il gruppo di risorse e tutte le risorse allocate, usare il comando seguente:

```azurecli
az group delete -n batchai.quickstart -y
```
