---
title: Avvio rapido di Azure - Creare un cluster Batch per intelligenza artificiale - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Avvio rapido - Creare un cluster Batch per intelligenza artificiale per eseguire il training di modelli di apprendimento automatico e di intelligenza artificiale - Interfaccia della riga di comando di Azure
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
ms.openlocfilehash: 1ea12c9a544704ea91b85ae944e611e6769b5592
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407134"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Guida introduttiva: creare un cluster per i processi di training di Batch per intelligenza artificiale usando l'interfaccia della riga di comando di Azure

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Questo avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare un cluster Batch per intelligenza artificiale da usare per il training di modelli di apprendimento automatico e di intelligenza artificiale. Batch per intelligenza artificiale è un servizio gestito che consente ai data scientist e ai ricercatori in ambito di intelligenza artificiale di eseguire il training di modelli di intelligenza artificiale e di apprendimento automatico su larga scala in cluster di macchine virtuali di Azure.

Inizialmente, il cluster ha un nodo GPU singolo. Dopo aver completato questo avvio rapido, sarà possibile aumentare il cluster e usarlo per eseguire il training dei modelli. Inviare i processi di training al cluster usando Batch per intelligenza artificiale, gli strumenti di [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) oppure [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.38 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Questo avvio rapido presuppone che i comandi vengano eseguiti in una shell Bash in Cloud Shell o nel computer locale.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti orientali 2*. Verificare di scegliere un'area in cui è disponibile il servizio Batch per intelligenza artificiale, ad esempio Stati Uniti orientali 2.

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

Per creare un cluster Batch per intelligenza artificiale, usare il comando `az batchai cluster create`. L'esempio seguente crea un cluster con le proprietà seguenti:

* Contiene un solo nodo nella dimensione della macchina virtuale NC6 che ha un processore GPU NVIDIA Tesla K80. 
* Esegue un'immagine di Ubuntu Server predefinita progettata per l'hosting di applicazioni basate su contenitore, che è possibile usare per la maggior parte dei carichi di lavoro di training. 
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

Nelle prime fasi di creazione del cluster, l'output è simile al seguente, e indica che il cluster è nello stato `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
Il cluster è pronto all'uso quando lo stato è `steady` e il nodo singolo è `Idle`.

## <a name="list-cluster-nodes"></a>Elencare i nodi del cluster 

È possibile ottenere le informazioni di connessione eseguendo il comando `az batchai cluster node list` se è necessario connettersi ai nodi del cluster (in questo caso, a un nodo singolo) per installare le applicazioni o eseguire attività di manutenzione:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

L'output JSON è simile a:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Usare queste informazioni per stabilire una connessione SSH con il nodo. Ad esempio, sostituire l'indirizzo IP del nodo nel comando seguente:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Chiudere la sessione SSH per continuare.

## <a name="resize-the-cluster"></a>Ridimensionare il cluster

Se il cluster viene usato per eseguire il training di un processo, potrebbero essere necessarie più risorse di calcolo. Ad esempio, per aumentare la dimensione a 2 nodi per un processo di training distribuito, eseguire il comando [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize):

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

Il ridimensionamento del cluster può richiedere alcuni minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Per proseguire con le esercitazioni e gli esempi di Batch per intelligenza artificiale, usare l'area di lavoro Batch per intelligenza artificiale creata in questo avvio rapido. 

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
    --resource-group myResourceGroup \
```

Se non serve più, è possibile usare il comando `az group delete` per rimuovere il gruppo di risorse per le risorse di Batch per intelligenza artificiale. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido si è appreso come creare un cluster Batch per intelligenza artificiale usando l'interfaccia della riga di comando di Azure. Per altre informazioni su come usare un cluster Batch per intelligenza artificiale per eseguire il training di un modello, passare all'avvio rapido per il training di un modello di apprendimento avanzato.

> [!div class="nextstepaction"]
> [Eseguire il training di un modello di apprendimento avanzato](./quickstart-tensorflow-training-cli.md)
