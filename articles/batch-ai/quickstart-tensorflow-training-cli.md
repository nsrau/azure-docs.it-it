---
title: Guida introduttiva di Azure - Training di apprendimento avanzato - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Guida introduttiva - Imparare rapidamente a eseguire il training di una rete neurale di apprendimento avanzato TensorFlow su una singola GPU con Batch per intelligenza artificiale usando l'interfaccia della riga di comando di Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408069"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Guida introduttiva: Eseguire il training di un modello di apprendimento avanzato con Batch per intelligenza artificiale

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Questa guida introduttiva illustra come eseguire il training di un modello di apprendimento avanzato di esempio su una macchina virtuale abilitata per GPU, gestita da Batch per intelligenza artificiale. Batch per intelligenza artificiale è un servizio gestito che consente ai data scientist e ai ricercatori in ambito di intelligenza artificiale di eseguire il training di modelli di intelligenza artificiale e di apprendimento automatico su larga scala in cluster di macchine virtuali di Azure. 

In questo esempio si usa l'interfaccia della riga di comando di Azure per configurare Batch per intelligenza artificiale per il training di una rete neurale [TensorFlow](https://www.tensorflow.org/) di esempio sul [database MNIST](http://yann.lecun.com/exdb/mnist/) di cifre scritte a mano. Dopo il completamento dell'avvio rapido saranno chiari i concetti fondamentali relativi all'utilizzo di Batch per intelligenza artificiale per eseguire il training di un modello di intelligenza artificiale o di apprendimento automatico e sarà possibile provare a eseguire il training di modelli diversi su larga scala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.38 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Questo avvio rapido presuppone che i comandi vengano eseguiti in una shell Bash in Cloud Shell o nel computer locale. Se già stato completato l'avvio rapido per [creare un cluster Batch per intelligenza artificiale con l'interfaccia della riga di comando di Azure](quickstart-create-cluster-cli.md), ignorare i primi due passaggi per creare un gruppo di risorse e un cluster Batch per intelligenza artificiale.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti orientali 2*. Assicurarsi di scegliere la località Stati Uniti orientali 2 o un'altra località in cui è disponibile il servizio Batch per intelligenza artificiale. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Creare un cluster Batch AI

Usare innanzi tutto il comando `az batchai workspace create` per creare un'*area di lavoro* Batch per intelligenza artificiale. È necessaria un'area di lavoro per organizzare i cluster Batch per intelligenza artificiale e le altre risorse.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Per creare un cluster Batch per intelligenza artificiale, usare il comando `az batchai cluster create`. L'esempio seguente crea un cluster con un solo nodo con le proprietà seguenti:

* Usa la dimensione di macchina virtuale NC6 con una GPU NVIDIA Tesla K80. Azure offre diverse dimensioni con GPU NVIDIA diverse.
* Esegue un'immagine di Ubuntu Server predefinita progettata per ospitare applicazioni basate su contenitori. È possibile eseguire la maggior parte dei carichi di lavoro di training su questa distribuzione. 
* Aggiunge un account utente denominato *myusername* e genera le chiavi SSH, se non sono già presenti nella posizione predefinita (*~/.ssh*) nell'ambiente locale.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

L'output del comando mostra le proprietà del cluster. La creazione e l'avvio del nodo richiedono alcuni minuti. Per visualizzare lo stato del cluster, eseguire il comando `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

All'inizio della creazione del cluster, l'output è simile al seguente per indicare che lo stato del cluster è `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Continuare la procedura seguente per caricare lo script di training e creare il processo di training mentre lo stato del cluster cambia. Il cluster è pronto per eseguire il processo di training quando lo stato è `steady` e il nodo singolo è `Idle`.

## <a name="upload-training-script"></a>Caricare lo script di training

Usare il comando `az storage account create` per creare un account di archiviazione in cui archiviare lo script e l'output del training.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Nell'account creare una condivisione file di Azure denominata `myshare` usando il comando `az storage share create`:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Usare il comando `az storage directory create` per creare le directory nella condivisione file di Azure. Creare la directory `scripts` per lo script di training e `logs` per l'output di training:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

Nella shell Bash creare una directory di lavoro locale e scaricare l'esempio [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) di TensorFlow. Lo script Python esegue il training di una rete neurale convoluzionale nel set di immagini MINIST di 60.000 cifre scritte a mano, da 0 a 9. Verifica quindi il modello in una serie di esempi di test.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Caricare lo script nella directory `scripts` nella condivisione usando il comando `az storage file upload`.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Inviare il processo di training

Creare prima di tutto un *esperimento* Batch per intelligenza artificiale nell'area di lavoro usando il comando `az batchai experiment create`. Un esperimento è un contenitore logico per i processi Batch per intelligenza artificiale correlati.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Nella directory di lavoro creare un file di configurazione del processo di training `job.json` con il contenuto seguente. Questo file di configurazione viene passato quando si invia il processo di training.

Questo file `job.json` include le impostazioni per individuare il file di script Python ed eseguirlo in un contenitore TensorFlow nel nodo GPU. Specifica anche la posizione in cui salvare i file di output del processo in Archiviazione di Azure. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` indica che il nome dell'account di archiviazione sarà specificato durante l'invio del processo.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Usare il comando `az batchai job create` per inviare il processo sul nodo, passando il file di configurazione `job.json` e il nome dell'account di archiviazione:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

Il comando restituisce le proprietà del processo e richiede pochi minuti per il completamento. Per monitorare lo stato di avanzamento di questo processo, usare il comando `az batchai job file stream` per trasmettere il file `stdout-wk-0.txt` dalla directory di output standard del nodo. Lo script di training genera questo file dopo l'avvio dell'esecuzione del processo.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Output di esempio:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

Il flusso si arresta al completamento del processo. Lo script di esempio esegue il training in 10 *periodi* o il pass-through al training set. In questo esempio, dopo 10 periodi, il modello sottoposto a training viene eseguito con un errore di test pari a solo 0,8%.

## <a name="get-job-output"></a>Ottenere l'output del processo

Batch per intelligenza artificiale crea una struttura di cartelle univoca nell'account di archiviazione per l'output di ogni processo. Impostare la variabile di ambiente JOB_OUTPUT_PATH con questo percorso. Elencare quindi i file di output nella risorsa di archiviazione usando il comando `az storage file list`:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

L'output è simile a:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Usare il comando `az storage file download` per scaricare uno o più file nella directory di lavoro locale. Ad esempio: 

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per proseguire con le esercitazioni e gli esempi di Batch per intelligenza artificiale, usare l'area di lavoro, il cluster e l'account di archiviazione di Batch per intelligenza artificiale creati in questo avvio rapido. 

Quando i nodi sono in esecuzione, viene addebitato il cluster Batch per intelligenza artificiale. Per mantenere la configurazione del cluster quando non sono previsti processi da eseguire, ridimensionare il cluster su 0 nodi. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Per eseguire i processi, è possibile eseguire un successivo ridimensionamento a 1 o più nodi. Quando un cluster non è più necessario, eliminarlo con il comando `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Quando non serve più, è possibile usare il comando `az group delete` per rimuovere il gruppo di risorse per Batch per intelligenza artificiale e le risorse di archiviazione. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
In questo avvio rapido è stato appreso come usare Batch per intelligenza artificiale per eseguire il training di un modello di apprendimento avanzato TensorFlow di esempio su una VM con singola GPU usando l'interfaccia della riga di comando di Azure. Per altre informazioni su come distribuire il training del modello in un cluster GPU di dimensioni maggiori, continuare con l'esercitazione su Batch per intelligenza artificiale.

> [!div class="nextstepaction"]
> [Esercitazione di training distribuito](./tutorial-horovod-tensorflow.md)