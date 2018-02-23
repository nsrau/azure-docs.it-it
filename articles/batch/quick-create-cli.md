---
title: 'Guida introduttiva di Azure: Eseguire un processo Batch - Interfaccia della riga di comando'
description: Imparare rapidamente a eseguire un processo Batch con l'interfaccia della riga di comando di Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/16/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4a9235db7431b531864272d220006dbe8340924
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Guida introduttiva: Eseguire il primo processo Batch con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure per creare un account Batch, un *pool* di nodi di calcolo (macchine virtuali) e un *processo* che esegue *attività* nel pool. Ogni attività di esempio esegue un comando di base in uno dei nodi del pool. Dopo aver completato questa guida introduttiva, saranno chiari i concetti fondamentali del servizio Batch e sarà possibile provare Batch con carichi di lavoro più realistici su scala più ampia.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti orientali 2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

È possibile collegare un account di archiviazione di Azure per uso generico all'account Batch. Anche se non è necessario per questa guida introduttiva, l'account di archiviazione è utile per distribuire applicazioni e archiviare dati di input e output per la maggior parte dei carichi di lavoro concreti. Creare un account di archiviazione nel gruppo di risorse con il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Creare un account Batch

Creare un account Batch con il comando [az batch account create](/cli/azure/batch/account#az_batch_account_create). È necessario un account per creare risorse di calcolo (pool di nodi di calcolo) e processi Batch.

L'esempio seguente crea un account Batch denominato *mybatchaccount* in *myResourceGroup* e collega l'account di archiviazione creato.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Per creare e gestire pool e processi di calcolo, è necessario eseguire l'autenticazione con Batch. Accedere all'account con il comando [az batch account login](/cli/azure/batch/account#az_batch_account_login). Dopo aver effettuato l'accesso, i comandi `az batch` useranno il contesto di questo account.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Creare un pool di nodi di calcolo

Ora che è disponibile un account Batch, creare un pool di esempio di nodi di calcolo Linux usando il comando [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create). L'esempio seguente crea un pool denominato *mypool* di 2 nodi di dimensioni *Standard_A1_v2* che eseguono Ubuntu 16.04 LTS. Le dimensioni consigliate per i nodi offrono un buon compromesso in termini di costi/prestazioni per questo esempio rapido.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04.0-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

Batch crea immediatamente il pool, ma richiede alcuni minuti per allocare e avviare i nodi di calcolo. In questa fase, il pool si trova nello stato `resizing`. Per visualizzare lo stato del pool, eseguire il comando [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show). Questo comando mostra tutte le proprietà del pool ed è possibile eseguire query per le proprietà specifiche. Il comando seguente ottiene lo stato di allocazione del pool:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Continuare con i passaggi seguenti per creare un processo e le attività mentre lo stato del pool sta ancora cambiando. Il pool è pronto per l'esecuzione di attività quando lo stato di allocazione è `steady` e tutti i nodi sono in esecuzione. 

## <a name="create-a-job"></a>Creare un processo

Dopo aver creato un pool, creare un processo da eseguire nel pool stesso.  Un processo Batch è un gruppo logico per una o più attività. Un processo include le impostazioni comuni per le attività, ad esempio la priorità e il pool nel quale eseguire le attività. Creare un processo Batch usando il comando [az batch job create](/cli/azure/batch/job#az_batch_job_create). Nell'esempio seguente viene creato un processo *myjob* nel pool *mypool*. Inizialmente il processo è privo di attività.

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Creare le attività

Usare ora il comando [az batch task create](/cli/azure/batch/task#az_batch_task_create) per creare alcune attività da eseguire nel processo. In questo esempio vengono create quattro attività identiche. Ogni attività esegue una `command-line` per visualizzare le variabili di ambiente Batch in un nodo di calcolo e quindi attende 90 secondi. Quando si usa Batch, in questa riga di comando si specifica l'app o lo script. Batch offre una serie di modi per distribuire app e script nei nodi di calcolo.

Lo script Bash seguente crea 4 attività parallele (da *mytask1* a *mytask4*).

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c printenv | grep AZ_BATCH; sleep 90s"
done
```

L'output del comando mostra le impostazioni per ognuna delle attività. Batch distribuisce le attività nei nodi di calcolo.

## <a name="view-task-status"></a>Visualizzare lo stato dell'attività

Dopo aver creato un'attività, Batch la accoda per l'esecuzione nel pool. Quando è disponibile un nodo per l'esecuzione, l'attività viene eseguita.

Usare il comando [az batch task show](/cli/azure/batch/task#az_batch_task_show) per visualizzare lo stato delle attività Batch. L'esempio seguente mostra i dettagli di *mytask1* in esecuzione in uno dei nodi del pool.

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

L'output del comando include molti dettagli, ma annotare il valore di `exitCode` della riga di comando dell'attività e il valore di `nodeId`. Un valore di `exitCode` pari a 0 indica che la riga di comando dell'attività è stata completata correttamente. Il valore di `nodeId` indica l'ID del nodo del pool in cui è stata eseguita l'attività.

## <a name="view-task-output"></a>Visualizzare l'output dell'attività

Per elencare i file creati da un'attività in un nodo di calcolo, usare il comando [az batch task file list](/cli/azure/batch/task#az_batch_task_file_list). Il comando seguente elenca i file creati da *mytask1*: 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

L'output è simile al seguente:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Per scaricare uno dei file di output in una directory locale, usare il comando [az batch task file download](/cli/azure/batch/task#az_batch_task_file_download). In questo esempio l'output dell'attività è in `stdout.txt`. 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Il contenuto di `stdout.txt` può essere visualizzato in un editor di testo. Il contenuto visualizza le variabili di ambiente di Azure Batch impostate nel nodo. Quando si creano i propri processi Batch, è possibile fare riferimento a queste variabili di ambiente nelle righe di comando delle attività e nelle app e negli script eseguiti dalle righe di comando.

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjob
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-1392786932_2-20171026t223740z
AZ_BATCH_POOL_ID=mypool-linux
AZ_BATCH_TASK_ID=mytask3
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>Pulire le risorse
Se si vuole proseguire con le esercitazioni e gli esempi di Batch, usare l'account Batch e l'account di archiviazione collegato creati in questa guida introduttiva. Non è previsto alcun addebito per l'account Batch in sé.

Vengono addebitati i costi dei pool mentre i nodi sono in esecuzione, anche se non sono pianificati processi. Quando un pool non è più necessario, eliminarlo con il comando [az batch pool delete](/cli/azure/batch/pool#az_batch_pool_delete):

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Quando non sono più necessari, è possibile eliminare il gruppo di risorse, l'account Batch, i pool e tutte le risorse correlate usando il comando [az group delete](/cli/azure/group#az_group_delete). Eliminare le risorse in questo modo:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un account Batch, un pool Batch e un processo Batch. Il processo ha eseguito attività di esempio ed è stato esaminato l'output creato in uno dei nodi. Ora che sono stati appresi i concetti fondamentali del servizio Batch, è possibile provare Batch con carichi di lavoro più realistici su scala più ampia. Per altre informazioni su Azure Batch, passare alle esercitazioni di Azure Batch. 


> [!div class="nextstepaction"]
> [Esercitazioni di Azure Batch](./tutorial-parallel-dotnet.md)
