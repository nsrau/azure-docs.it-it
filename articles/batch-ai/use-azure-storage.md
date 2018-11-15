---
title: Archiviazione di input e output dei processi di Batch per intelligenza artificiale con Archiviazione di Azure | Microsoft Docs
description: Come usare Archiviazione di Azure con Batch per intelligenza artificiale per archiviare nel cloud i file di input e output in modo semplice e rapido
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 42697f7f4bb8c6b9ef785eef0fe2f5f33b2b38a7
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615604"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Archiviare i file di input e output dei processi di Batch per intelligenza artificiale con Archiviazione di Azure

Questa guida descrive come usare Archiviazione di Azure per l'archiviazione dei file di input e output quando si esegue un processo. Archiviazione di Azure è una delle opzioni di archiviazione supportate da Batch per intelligenza artificiale. Batch per intelligenza artificiale si integra con Archiviazione di Azure montando i sistemi di Archiviazione di Azure nel file system di un processo o un cluster di Batch per intelligenza artificiale, semplificando l'accesso ai file archiviati nel cloud. 

## <a name="introduction-to-azure-storage"></a>Introduzione ad Archiviazione di Azure

Archiviazione di Azure è la soluzione di archiviazione cloud Microsoft. Batch per intelligenza artificiale supporta il montaggio dei contenitori BLOB di Azure e delle condivisioni di File di Azure in processi o cluster di Batch per intelligenza artificiale, consentendo di accedere ai file da un processo come se si trovassero nel file system nativo. Batch per intelligenza artificiale consente di montare i contenitori BLOB di Azure con [blobfuse](https://github.com/Azure/azure-storage-fuse) e le condivisioni di File di Azure con il protocollo SMB. Per altre informazioni su Archiviazione di Azure, vedere l'[introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Archiviare set di dati e script di input in Archiviazione di Azure

Se si sceglie Archiviazione di Azure per l'ambiente di Batch per intelligenza artificiale, è consigliabile archiviare i file di input, ad esempio i set di dati, in un contenitore BLOB, che ha una velocità effettiva superiore, e archiviare l'output di training in una condivisione di file, che supporta il flusso (consente la lettura dei log di output mentre il processo viene eseguito simultaneamente). 

Per poter usare Archiviazione di Azure, è necessario [creare un account di Archiviazione di Azure](../storage/common/storage-quickstart-create-account.md). Batch per intelligenza artificiale supporta il montaggio di volumi degli account di Archiviazione di Azure di tipo Utilizzo generico v1 (GPv1) e Utilizzo generico v2 (GPv2). L'account di Archiviazione di Azure può includere più contenitori BLOB o istanze di condivisione di file. Prendere in considerazione i requisiti relativi a costi e prestazioni quando si sceglie il tipo di account di archiviazione da creare. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md). 

Per creare un contenitore BLOB e caricare il set di dati in un contenitore BLOB di Azure, scegliere uno dei metodi seguenti:
- [Portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md) per il caricamento con un'interfaccia utente grafica basata sul Web. Per caricare un numero ridotto di file, il portale di Azure offre l'operazione più semplice.
- [Interfaccia della riga di comando di Archiviazione di Azure](../storage/blobs/storage-quickstart-blobs-cli.md) per il caricamento dalla riga di comando (supporta il caricamento delle directory). Per caricare directory di file, usare `az storage blob upload-batch`.
- [Altre tecniche](../storage/common/storage-moving-data.md), incluso l'uso di SDK dell'applicazione.

Analogamente, per creare una condivisione di File di Azure, scegliere uno dei metodi seguenti:
- [Portale di Azure](../storage/files/storage-how-to-use-files-portal.md)
- [interfaccia della riga di comando di Archiviazione di Azure](../storage/files/storage-how-to-use-files-cli.md)
- [altre tecniche](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Archiviazione automatica con Batch per intelligenza artificiale

In alternativa, è possibile creare un account di Archiviazione di Azure con una condivisione di File di Azure e un contenitore BLOB (e montare automaticamente questi volumi in un cluster di Batch per intelligenza artificiale) usando il parametro `--use-auto-storage` con `az batchai cluster create`. Per altre informazioni, vedere [qui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Montare Archiviazione di Azure 

### <a name="mount-volumes-to-a-job"></a>Montare i volumi in un processo

Il montaggio di un volume di Archiviazione di Azure consente di accedere al volume usando il file system creato per ogni processo. Di conseguenza, un processo può leggere e scrivere file direttamente nell'archiviazione cloud come se fossero file locali.

Per montare un volume di Archiviazione di Azure in un processo creato con l'interfaccia della riga di comando di Azure, usare la proprietà `mountVolumes` nel file `job.json` durante l'esecuzione di `az batchai job create`. Per un esempio, vedere il [recipe GPU Tensorflow Distributed](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). Lo schema per `mountVolumes` è:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` e `azureBlobFileSystems` sono entrambe matrici di oggetti che rappresentano i volumi da montare. Descrizioni dei segnaposto:

- <RELATIVE_MOUNT_PATH>: il volume verrà montato in questo percorso. Ad esempio, se `relativeMountPath` è `jobs`, il volume sarà in `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`.
- <STORAGE_ACCOUNT_NAME>: nome dell'account di Archiviazione di Azure che contiene la condivisione di file o il contenitore BLOB
- <FILE_SHARE_NAME>: nome della condivisione di file
- <BLOB_CONTAINER_NAME>: nome del contenitore BLOB

Per montare i volumi di Archiviazione di Azure con gli SDK di Azure Batch per intelligenza artificiale, impostare la proprietà `mount_volumes` (Python) o `MountVolumes` (C#, Java) su `JobCreateParameters`. È necessario specificare le credenziali dell'account di archiviazione durante il montaggio dei volumi con gli SDK di Azure Batch per intelligenza artificiale. Visualizzare gli schemi per il montaggio dei volumi in [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet) e [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Montare i volumi in un cluster

Batch per intelligenza artificiale supporta anche il montaggio dei volumi di Archiviazione di Azure in un cluster di Batch per intelligenza artificiale. Quando si monta un volume in un cluster, tutti i processi in esecuzione nel cluster possono usare i volumi montati nel cluster. Anche se il montaggio a livello di processo offre la massima flessibilità, poiché consente a ogni processo di usare volumi montati differenti, il montaggio a livello di cluster può essere sufficiente negli scenari semplici.

Per montare un volume di Archiviazione di Azure in un cluster creato con l'interfaccia della riga di comando di Azure, usare la proprietà `mountVolumes` nel file `cluster.json` durante l'esecuzione di `az batchai cluster create`. Lo schema per `mountVolumes` quando si esegue il montaggio in un cluster è lo stesso usato per il montaggio in un processo. 

Analogamente, è possibile usare la proprietà `mount_volumes` (Python) o `MountVolumes` (C#, Java) in `ClusterCreateParameters` durante il montaggio con gli SDK di Azure Batch per intelligenza artificiale. 

## <a name="access-mounted-filesystem-in-a-job"></a>Accedere al file system montato in un processo

### <a name="azbatchaijobmountroot-environment-variable"></a>Variabile di ambiente $AZ_BATCHAI_JOB_MOUNT_ROOT

All'interno dell'ambiente di esecuzione del processo, per accedere alla directory che contiene i sistemi di archiviazione montati è possibile usare la variabile di ambiente `$AZ_BATCHAI_JOB_MOUNT_ROOT` (se si usa il montaggio a livello di processo). Se si usa il montaggio a livello di cluster, la variabile di ambiente è `$AZ_BATCHAI_MOUNT_ROOT`. Gli esempi seguenti presuppongono che venga usato il montaggio a livello di processo.

Per specificare il percorso dei dati in un volume montato, usare la variabile di ambiente `$AZ_BATCHAI_JOB_MOUNT_ROOT` con il percorso montato. Ad esempio, se lo script di training `train.py` è stato caricato in una condivisione di File di Azure montata nel percorso di montaggio relativo `scripts`, il file sarà disponibile in `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Se lo script di training richiede la conoscenza di un percorso, è necessario passarlo come argomento della riga di comando. Ad esempio, se si archiviano i dati in una cartella denominata `train_data` in un contenitore BLOB di Azure montato nel percorso `data`, è possibile passare `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` allo script come argomento della riga di comando. Di conseguenza, è necessario modificare lo script in modo che accetti gli argomenti della riga di comando.

### <a name="abbreviate-input-paths"></a>Abbreviare i percorsi di input

Per abbreviare i percorsi di input come variabile di ambiente, usare la proprietà `inputDirectories` del file `job.json` (o `models.JobCreateParameters.input_directories` se si usa l'SDK di Batch per intelligenza artificiale). Lo schema di `inputDirectories` è:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Ogni percorso specificato verrà inserito in una variabile di ambiente denominata `$AZ_BATCHAI_INPUT_<ID>`. L'uso di questo metodo consente di semplificare i percorsi per i file o le directory di input. Ad esempio, per abbreviare il percorso di uno script di training: se `"id"` è `"SCRIPT"` e `"path"` è `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, il percorso è disponibile in `$AZ_BATCHAI_INPUT_SCRIPT` nell'ambiente di esecuzione del processo.

Per altre informazioni, vedere [qui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Abbreviare i percorsi di output

Per abbreviare i percorsi di output come variabile di ambiente, usare la proprietà `outputDirectories` del file `job.json` (o `models.JobCreateParameters.output_directories` se si usa l'SDK di Batch per intelligenza artificiale). L'uso di questo metodo consente di semplificare i percorsi per i file di output. Lo schema di `outputDirectories` è:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Ogni percorso specificato verrà inserito in una variabile di ambiente denominata `$AZ_BATCHAI_OUTPUT_<ID>`. `pathPrefix` determina il volume montato per archiviare l'output, ad esempio `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`. `pathSuffix` determina il nome della cartella dell'output, ad esempio `"logs"`, `"checkpoints"`. Il percorso effettivo dell'output è una concatenazione di `pathPrefix`, `jobOutputDirectoryPathSegment` (generato automaticamente per ogni processo) e `pathSuffix`.

Per altre informazioni, vedere [qui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Recuperare l'output del processo da Archiviazione di Azure

### <a name="use-azure-portal"></a>Usare il portale di Azure

Il portale di Azure è un modo pratico per visualizzare l'output di processi usando uno strumento di esplorazione file dell'interfaccia utente grafica. Tuttavia, se si vuole visualizzare l'output di StdOut e StdErr, o di un percorso in `outputDirectories`, i file vengono inseriti in un percorso generato automaticamente nel volume di Archiviazione di Azure. Per altre informazioni, vedere di seguito.

### <a name="access-stdout-and-stderr-output"></a>Accedere all'output di StdOut e StdErr

Usare la proprietà `stdOutErrPathPrefix` di `job.json` per indicare al processo dove inserire i log di esecuzione del processo e l'output di StdOut e StdErr. Ad esempio, se è stata montata una condivisione file nel percorso di montaggio relativo `outputs` e si specifica che `stdOutErrPathPrefix` deve essere `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, l'output del processo StdOut e StdErr sarà disponibile in `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` in quel volume montato. Questo percorso generato automaticamente viene usato per evitare conflitti di output tra i processi con lo stesso nome.

Per altre informazioni, vedere [qui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Elencare i file di output

È possibile usare l'interfaccia della riga di comando di Azure per elencare i file di output disponibili di un processo con il comando `az batchai job file list`. Ad esempio, per elencare i file nella directory di output standard di un processo, usare `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Per altre informazioni ed esempi, vedere [qui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Streaming dei file di output

È possibile usare l'interfaccia della riga di comando di Azure per lo streaming dei file con il comando `az batchai job file stream`. Ad esempio, per visualizzare:
- Stdout: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Per altre informazioni ed esempi, vedere [qui](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sull'uso dei comandi dell'interfaccia della riga di comando per interagire con Archiviazione di Azure, vedere la [documentazione della riga di comando di Azure Batch per intelligenza artificiale](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Per altri esempi dell'uso di Batch per intelligenza artificiale, tra cui il montaggio dell'archiviazione e la lettura dei file di output, vedere i [recipe di Jupyter Notebook per Batch per intelligenza artificiale](https://github.com/Azure/BatchAI).
- Esaminare altre opzioni per il montaggio dell'archiviazione, tra cui il [montaggio di un server NFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) e il [montaggio di un proprio cluster NFS, cifs o GlusterFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)