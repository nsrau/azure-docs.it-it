---
title: Eseguire il rendering di una scena nel cloud - Azure Batch
description: Esercitazione - Come eseguire il rendering di una scena Autodesk 3ds Max con Arnold usando il servizio Rendering di Batch e l'interfaccia della riga di comando di Azure
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: tutorial
ms.date: 02/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 0531406ce50cf8cb549965d1f30b327afe52b003
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Esercitazione: Eseguire il rendering di una scena con Azure Batch 

Azure Batch offre funzionalità di rendering su scala cloud con pagamento in base al consumo. Il servizio Rendering di Batch supporta Autodesk Maya, 3ds Max, Arnold e V-Ray. Questa esercitazione descrive i passaggi necessari per eseguire il rendering di una breve scena con Batch usando l'interfaccia della riga di comando di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Caricare una scena nell'archiviazione di Azure
> * Creare un pool di Batch per il rendering
> * Eseguire il rendering di una scena a singolo frame
> * Ridimensionare il pool ed eseguire il rendering di una scena a più frame
> * Scaricare l'output sottoposto a rendering

In questa esercitazione si eseguirà il rendering di una scena 3ds Max usando il renderer di ray tracing [Arnold](https://www.autodesk.com/products/arnold/overview). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

La scena 3ds Max di esempio per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene), insieme a uno script Bash di esempio e ai file di configurazione JSON. La scena 3ds Max proviene dai [file di esempio di Autodesk 3ds Max](http://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). I file di esempio di Autodesk 3ds Max sono disponibili con una licenza con dicitura "Attribuzione - Non commerciale - Condividi allo stesso modo" di Creative Commons. Copyright © Autodesk, Inc.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-batch-account"></a>Creare un account Batch

Se non è già stato fatto, creare un gruppo di risorse, un account Batch e un account di archiviazione collegato nella sottoscrizione. 

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti orientali 2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

Creare un account di archiviazione per uso generico nel gruppo di risorse con il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). In questa esercitazione l'account di archiviazione viene usato per archiviare una scena 3ds Max di input e l'output sottoposto a rendering.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
Creare un account Batch con il comando [az batch account create](/cli/azure/batch/account#az_batch_account_create). L'esempio seguente crea un account Batch denominato *mybatchaccount* in *myResourceGroup* e collega l'account di archiviazione creato.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Per creare e gestire pool e processi di calcolo, è necessario eseguire l'autenticazione con Batch. Accedere all'account con il comando [az batch account login](/cli/azure/batch/account#az_batch_account_login). Dopo aver effettuato l'accesso, i comandi `az batch` useranno il contesto di questo account. L'esempio seguente usa l'autenticazione con chiave condivisa in base al nome account e alla chiave di Batch. Batch supporta anche l'autenticazione tramite [Azure Active Directory](batch-aad-auth.md), per autenticare singoli utenti o un'applicazione eseguita automaticamente.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>Caricare una scena nell'archiviazione

Per caricare la scena di input nell'archiviazione, prima di tutto è necessario accedere all'account di archiviazione e creare un contenitore di destinazione per i BLOB. Per accedere all'account di archiviazione di Azure, esportare le variabili di ambiente `AZURE_STORAGE_KEY` e `AZURE_STORAGE_ACCOUNT`. Il primo comando della shell Bash usa il comando [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) per ottenere la prima chiave dell'account. Dopo aver impostato queste variabili di ambiente, i comandi di archiviazione useranno l'account di questo contesto.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Creare ora un contenitore BLOB nell'account di archiviazione per i file della scena. L'esempio seguente usa il comando [az storage container create](/cli/azure/storage/container#az_storage_container_create) per creare un contenitore BLOB denominato *scenefiles*, che permette l'accesso in lettura pubblico.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Scaricare la scena `MotionBlur-Dragon-Flying.max` da [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) in una directory di lavoro locale. Ad esempio: 

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Caricare il file della scena dalla directory di lavoro locale al contenitore BLOB. L'esempio seguente usa il comando [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch), che può caricare più file:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Creare un pool per il rendering

Creare un pool di Batch per il rendering usando il comando [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create). In questo esempio le impostazioni del pool vengono specificate in un file JSON. Nella shell corrente creare un file denominato *mypool.json* e quindi copiare e incollare il contenuto seguente. Assicurarsi che tutto il testo venga copiato correttamente. È possibile scaricare il file da [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "latest"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch supporta nodi dedicati e nodi [per priorità bassa](batch-low-pri-vms.md), che possono essere usati entrambi nei pool. I nodi dedicati sono riservati per il pool. I nodi per priorità bassa vengono offerti a un prezzo ridotto usando la capacità in eccesso delle VM in Azure. I nodi per priorità bassa non sono disponibili se Azure non ha capacità sufficiente. 

Il pool specificato contiene un unico nodo per priorità bassa che esegue un'immagine Windows Server con software per il servizio Rendering di Batch. Questo pool viene concesso in licenza per il rendering con 3ds Max e Arnold. In un passaggio successivo il pool verrà ridimensionato in base a un numero maggiore di nodi.

Creare il pool passando il file JSON al comando `az batch pool create`:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
Il provisioning del pool richiede pochi minuti. Per visualizzare lo stato del pool, eseguire il comando [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show). Il comando seguente ottiene lo stato di allocazione del pool:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Continuare con i passaggi seguenti per creare un processo e le attività mentre lo stato del pool sta ancora cambiando. Il provisioning del pool è completo quando lo stato di allocazione è `steady` e i nodi sono in esecuzione.  

## <a name="create-a-blob-container-for-output"></a>Creare un contenitore BLOB per l'output

Negli esempi di questa esercitazione ogni attività nel processo di rendering crea un file di output. Prima di pianificare il processo, creare un contenitore BLOB nell'account di archiviazione come destinazione per i file di output. L'esempio seguente usa il comando [az storage container create](/cli/azure/storage/container#az_storage_container_create) per creare il contenitore *job-myrenderjob* con accesso in lettura pubblico. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Per scrivere i file di output nel contenitore, Batch deve usare un token di firma di accesso condiviso. Creare il token con il comando [az storage account generate-sas](/cli/azure/storage/account#az_storage_account_generate_sas). Questo esempio crea un token per scrivere in qualsiasi contenitore BLOB nell'account e il token scade il 15 novembre 2018:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2018-11-15
```

Annotare il token restituito dal comando, che sarà simile al seguente. Questo token verrà usato in uno dei passaggi seguenti.

```
se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>Eseguire il rendering di una scena a singolo frame

### <a name="create-a-job"></a>Creare un processo

Creare un processo di rendering da eseguire nel pool usando il comando [az batch job create](/cli/azure/batch/job#az_batch_job_create). Inizialmente il processo è privo di attività.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Crea un'attività

Usare il comando [az batch task create](/cli/azure/batch/task#az_batch_task_create) per creare un'attività di rendering nel processo. In questo esempio le impostazioni dell'attività vengono specificate in un file JSON. Nella shell corrente creare un file denominato *myrendertask.json* e quindi copiare e incollare il contenuto seguente. Assicurarsi che tutto il testo venga copiato correttamente. È possibile scaricare il file da [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).

L'attività specifica un comando di 3ds Max per eseguire il rendering di un singolo frame della scena *MotionBlur-DragonFlying.max*.

Modificare gli elementi `blobSource` e `containerURL` nel file JSON in modo che includano il nome dell'account di archiviazione e il token di firma di accesso condiviso. 

> [!TIP]
> L'elemento `containerURL` termina con il token di firma di accesso condiviso ed è simile al seguente:
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "blobSource": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Aggiungere l'attività al processo con il comando seguente:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch pianifica l'attività e l'attività viene eseguita non appena è disponibile un nodo nel pool.


### <a name="view-task-output"></a>Visualizzare l'output dell'attività

L'esecuzione dell'attività richiede pochi minuti. Usare il comando [az batch task show](/cli/azure/batch/task#az_batch_task_show) per visualizzare i dettagli sull'attività.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

L'attività genera il file *dragon0001.jpg* nel nodo di calcolo e lo carica nel contenitore *job-myrenderjob* all'interno dell'account di archiviazione. Per visualizzare l'output, scaricare il file dall'archiviazione al computer locale usando il comando [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Aprire *dragon.jpg* nel computer. L'immagine sottoposta a rendering sarà simile alla seguente:

![Frame 1 dragon sottoposto a rendering](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>Ridimensionare il pool

Modificare ora il pool per preparare un processo di rendering di dimensioni più grandi, con più frame. Batch offre diversi modi per ridimensionare le risorse di calcolo, tra cui il [ridimensionamento automatico](batch-automatic-scaling.md), che aggiunge o rimuove nodi in base alle esigenze legate alle attività. Per questo esempio di base, usare il comando [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) per aumentare a *6* il numero di nodi per priorità bassa nel pool:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Il ridimensionamento del pool richiede pochi minuti. Durante l'esecuzione del processo, configurare le attività successive per l'esecuzione nel processo di rendering esistente.

## <a name="render-a-multiframe-scene"></a>Eseguire il rendering di una scena a più frame

Come nell'esempio con un solo frame, usare il comando [az batch task create](/cli/azure/batch/task#az_batch_task_create) per creare le attività di rendering nel processo denominato *myrenderjob*. A questo punto, specificare le impostazioni delle attività in un file JSON denominato *myrendertask_multi.json*. È possibile scaricare il file da [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json). Ognuna delle sei attività specifica la riga di comando di Arnold per il rendering di un frame della scena 3ds Max *MotionBlur-DragonFlying.max*.

Nella shell corrente creare un file denominato *myrendertask.json* e quindi copiare e incollare il contenuto dal file scaricato. Modificare gli elementi `blobSource` e `containerURL` nel file JSON in modo che includano il nome dell'account di archiviazione e il token di firma di accesso condiviso. Assicurarsi di modificare le impostazioni per ognuna delle sei attività. Salvare il file ed eseguire il comando seguente per accodare le attività:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Visualizzare l'output dell'attività

L'esecuzione dell'attività richiede pochi minuti. Usare il comando [az batch task list](/cli/azure/batch/task#az_batch_task_list) per visualizzare lo stato delle attività. Ad esempio: 

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Usare il comando [az batch task show](/cli/azure/batch/task#az_batch_task_show) per visualizzare i dettagli sulle singole attività. Ad esempio: 

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
Le attività generano i file di output denominati *dragon0002.jpg* - *dragon0007.jpg* nei nodi di calcolo e li caricano nel contenitore *job-myrenderjob* all'interno dell'account di archiviazione. Per visualizzare l'output, scaricare i file in una cartella nel computer locale usando il comando [az storage blob download-batch](/cli/azure/storage/blob#az_storage_blob_download_batch). Ad esempio: 

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Aprire uno dei file nel computer. Il frame 6 sottoposto a rendering sarà simile al seguente:

![Frame 6 dragon sottoposto a rendering](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare il gruppo di risorse, l'account Batch, i pool e tutte le risorse correlate usando il comando [az group delete](/cli/azure/group#az_group_delete). Eliminare le risorse in questo modo:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso a:

> [!div class="checklist"]
> * Caricare scene nell'archiviazione di Azure
> * Creare un pool di Batch per il rendering
> * Eseguire il rendering di una scena a singolo frame con Arnold
> * Ridimensionare il pool ed eseguire il rendering di una scena a più frame
> * Scaricare l'output sottoposto a rendering

Per altre informazioni sul rendering su scala cloud, vedere le opzioni per il servizio Rendering di Batch. 

> [!div class="nextstepaction"]
> [Servizio di rendering di Batch](batch-rendering-service.md)
