---
title: Usare i cluster di Azure Batch per intelligenza artificiale | Microsoft Docs
description: Di seguito viene spiegato come scegliere una configurazione cluster Batch per intelligenza artificiale, nonché come creare e gestire un cluster per intelligenza artificiale
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 9af8ce84805e48dd3c91dd7fb4fcf0b136fbfc60
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410092"
---
# <a name="work-with-batch-ai-clusters"></a>Usare i cluster di Batch per intelligenza artificiale 

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Questo articolo illustra come usare i cluster in Azure Batch per intelligenza artificiale. Introduce il concetto di cluster e i tipi di configurazioni possibili e riporta alcuni esempi. La maggior parte degli esempi per creare e gestire un cluster in questo articolo usa l’interfaccia della riga di comando di Azure. Tuttavia, per usare i cluster è possibile impiegare altri strumenti, tra cui il portale di Azure e gli SDK di Azure Batch per intelligenza artificiale.

Un cluster di Batch per intelligenza artificiale è una delle diverse risorse nel servizio. Vedere le [panoramica delle risorse di Batch per intelligenza artificiale](resource-concepts.md) per comprendere l'ambito dei cluster nel servizio.

## <a name="introduction-to-clusters"></a>Introduzione ai cluster

Un cluster in Batch per intelligenza artificiale contiene le risorse di calcolo per l'esecuzione di processi di training per l’apprendimento automatico e l’intelligenza artificiale. Tutti i nodi in un cluster hanno le stesse dimensioni macchina virtuale e immagine del sistema operativo. Batch per intelligenza artificiale offre numerose opzioni per la creazione di cluster personalizzati in base a diverse esigenze. In genere, si configura un cluster diverso per ogni categoria della potenza di elaborazione necessaria per completare un progetto. È possibile ridimensionare o aumentare il numero di nodi in un cluster in base alla domanda e al budget. Le operazioni di provisioning o condivisione dei cluster possono essere eseguite all’interno di un team, oppure i singoli utenti possono eseguire il provisioning del proprio cluster. 

Ogni cluster esiste in una risorsa di Batch per intelligenza artificiale chiamata *area di lavoro*. Prima del provisioning del cluster, è necessario disporre di un'area di lavoro di Batch per intelligenza artificiale configurata. Ad esempio, se si usa l’interfaccia della riga di comando di Azure, usare il comando [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) per configurare un'area di lavoro. 

Dopo aver creato un cluster, è possibile inviare processi uno alla volta in coda. Batch per intelligenza artificiale gestisce quindi il processo di allocazione di risorse per l'esecuzione dei processi nel cluster. 

## <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Quando si pianifica un cluster, determinare prima di tutto i requisiti di calcolo. Batch per intelligenza artificiale offre opzioni di configurazione flessibili in modo che sia possibile personalizzare un cluster in base alle singole esigenze. Di seguito sono riportate le principali opzioni da prendere in considerazione durante il provisioning di un cluster:

* **Dimensioni VM**: scegliere una qualsiasi [dimensione della macchina virtuale](../virtual-machines/linux/sizes.md) disponibile in un’[area supportata](https://azure.microsoft.com/global-infrastructure/services/) per i nodi del cluster. Ogni cluster può contenere solo una dimensione di macchina virtuale, pertanto se l'attività richiede più tipi di macchine virtuali, è necessario eseguire il provisioning di un cluster separato per ogni tipo di requisito di calcolo. Per eseguire il training di apprendimento automatico o di modelli di intelligenza artificiale sviluppati con i framework che sfruttano le GPU, consultare le [dimensioni delle macchine virtuali ottimizzate per la GPU](../virtual-machines/linux/sizes-gpu.md) in Azure.
  
* **Priorità VM**: Batch per intelligenza artificiale offre macchine virtuali dedicate o con priorità bassa per un cluster. Le macchine virtuali dedicate sono riservate per l'uso del cluster. L'opzione con priorità bassa alloca capacità inutilizzata di una macchina virtuale di Azure con notevoli risparmi sui costi in cambio della possibilità di annullare i processi se Azure recupera le macchine virtuali. I processi eseguiti per più di 24 ore in una macchina virtuale con priorità bassa vengono allo stesso modo annullati automaticamente. Se il budget rappresenta un problema, considerare l'utilizzo di macchine virtuali con priorità bassa per brevi processi di sperimentazione. Passare quindi a macchine virtuali dedicate quando è il momento di eseguire processi di maggiore durata.

* **Numero di nodi** -Batch per intelligenza artificiale offre opzioni di scalabilità automatica e manuale per il numero di nodi nel cluster. Con l'opzione manuale, è possibile controllare quando applicare la scalabilità verticale, in modo da poter gestire i propri costi di calcolo. L'opzione di scalabilità automatica garantisce che il cluster venga sempre ridimensionato quando non in uso, riducendo così al minimo i costi di calcolo. 

  Se si sceglie di ridimensionare manualmente il cluster, si deve definire la destinazione iniziale durante la creazione del cluster. La destinazione è il numero di nodi che Batch per intelligenza artificiale consente di allocare inizialmente. In un secondo momento, è possibile ridimensionare manualmente il numero di nodi.  

  Se si sceglie l'opzione di scalabilità automatica, è necessario definire il numero minimo e massimo di nodi di destinazione durante la creazione del cluster. La destinazione può variare da 0 al numero massimo di nodi supportati per la [quota di core di Batch per intelligenza artificiale](quota-limits.md). Una destinazione pari a 0 consente di mantenere la configurazione del cluster senza dover sostenere eventuali costi di calcolo. Se si richiede una destinazione maggiore rispetto a quella supportata dal limite di quota, il provisioning avrà esito negativo. 

* **Immagine VM** -Batch per intelligenza artificiale per impostazione predefinita esegue il provisioning di macchine virtuali del cluster con un'immagine predefinita di Ubuntu Server che supporta i carichi di lavoro dei contenitori. È possibile scegliere un'altra immagine preconfigurata di Linux da Azure Marketplace, ad esempio una [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md). 

* **Archiviazione**: Batch per intelligenza artificiale offre un'opzione di archiviazione automatica, che è possibile scegliere quando si crea un cluster tramite l’interfaccia della riga di comando di Azure. Questa opzione crea automaticamente un contenitore BLOB e di condivisione di file di Azure con un nuovo account di archiviazione. Queste risorse di archiviazione vengono montate in ciascuno dei nodi del cluster durante la fase di esecuzione, consentendo di accedere ai file da un percorso locale. I nomi degli account di archiviazione, il nome condivisione file, il nome del contenitore BLOB e i percorsi di montaggio hanno tutti valori predefiniti, che possono essere personalizzati anche usando i parametri aggiuntivi durante la creazione del cluster. 

  Se nessuna opzione di archiviazione è definita, è necessario creare le risorse di archiviazione separatamente e definirle durante l'invio di processi. Questa opzione è utile se il cluster è gestito a livello di organizzazione, ma lo spazio di archiviazione è gestito a livello di utente. Per altre informazioni su come caricare file in archiviazione di Azure e accedervi durante la fase di esecuzione, consultare [Archiviare input e output del processo di Batch per intelligenza artificiale con Archiviazione di Azure](use-azure-storage.md).

* **Accesso utente**: Batch per intelligenza artificiale consente di generare file di chiave SSH pubblici e privati durante la creazione di un cluster o di fornire le proprie chiavi SSH in modo che sia possibile usare SSH nei nodi singoli. È anche possibile definire un nome utente (impostato come utente corrente per impostazione predefinita) e una password. Queste credenziali possono essere usate per accedere ai nodi durante l'esecuzione al fine di visualizzare varie metriche o ottenere altre informazioni dettagliate relative ai processi.

* **Attività di installazione**: Batch per intelligenza artificiale consente di definire gli argomenti della riga di comando da eseguire in ogni nodo al momento dell'allocazione. È inoltre possibile definire il percorso in cui il file di output deve essere registrato. Usare questa opzione quando si dispone di ulteriori passaggi di provisioning oltre l'immagine di base.

* **Configurazione aggiuntiva**: esistono diversi scenari meno comuni che potrebbero richiedere configurazioni più specializzate. In questo caso, un [file di configurazione cluster](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) può essere collegato con l’interfaccia della riga di comando di Azure per creare un cluster. 

## <a name="create-the-cluster"></a>Creare il cluster

Dopo aver deciso sulle opzioni di configurazione del cluster, usare l’interfaccia della riga di comando di Azure, il portale di Azure o le API di Batch per intelligenza artificiale per creare il cluster. Ad esempio, per creare un cluster tramite l’interfaccia della riga di comando di Azure, è possibile seguire la documentazione [az batchai cluster create](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) per creare il comando esatto che offre le configurazioni necessarie. 

Nella configurazione di base, il seguente comando esegue il provisioning di un cluster Standard_NC6 con un nodo e accesso SSH. Per impostazione predefinita, questo cluster contiene macchine virtuali dedicate che eseguono l'immagine di Ubuntu Server predefinita più recente.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

L'esempio seguente mostra il provisioning di un cluster Standard_NC6 che scala automaticamente da 0 a 4 nodi e utilizza nodi a bassa priorità e un account di archiviazione automatica. Questa configurazione è ideale se si ha bisogno di un cluster a basso costo e facile da gestire. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

L'esempio seguente mostra il provisioning di un cluster Standard_NC6 che include un'immagine della Data Science Virtual Machine, opzioni di montaggio e archiviazione personalizzate, credenziali SSH personalizzate, un'attività di configurazione che installa il pacchetto *unzip* e un file di configurazione del cluster per un'ulteriore configurazione. Questa configurazione è un esempio di cluster più personalizzato in base alle proprie esigenze.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>Monitorare il cluster

I comandi [az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show), e [az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) possono essere usati per monitorare varie informazioni per ogni cluster.

### <a name="list-all-clusters"></a>Elencare tutti i cluster

Il seguente comando elenca tutti i cluster in un'area di lavoro.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>Visualizzare informazioni su un cluster

Il comando seguente mostra le informazioni complete su un cluster specifico in formato tabella.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Se è stato effettuato il provisioning del cluster usando l'opzione di archiviazione automatica, è opportuno recuperare il nome dell’account di archiviazione da usare durante il caricamento di script e processi di training. Usare il comando seguente:

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

L'output dovrebbe essere simile al seguente.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Elencare i nodi del cluster

Se è necessario connettersi ai nodi del cluster, il comando seguente recupera l'elenco di nodi e le informazioni di connessione.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

L'output per ogni nodo sarà simile al seguente:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
È possibile utilizzare queste informazioni per stabilire una connessione SSH a un nodo usando un comando simile al seguente.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Inviare processi al cluster

Dopo aver eseguito il provisioning del cluster, è quindi possibile inviare i processi da eseguire nei nodi. Vedere il comando [az batchai job](/cli/azure/batchai/job?view=azure-cli-latest) per le diverse modalità di preparazione, invio e monitoraggio dei processi tramite l’interfaccia della riga di comando di Azure. 

## <a name="downscale-cluster-for-later-use"></a>Ridimensionamento dei cluster per un uso successivo

Dopo aver completato l'esecuzione di processi, si dovrà effettuare il ridimensionamento del cluster. È consigliabile sempre ridimensionare i cluster quando non sono in uso per risparmiare sui costi di calcolo. Il ridimensionamento di un cluster specificando 0 nodi consente di interrompere la fatturazione senza dover effettuare nuovamente il provisioning dei cluster in futuro quando è necessario scalare nuovamente. Se durante la creazione di cluster è stata selezionata la scalabilità automatica, il cluster deve automaticamente effettuare il ridimensionamento alla destinazione minima. Se è stata selezionata la scalabilità manuale, ridimensionare il cluster usando il comando seguente.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Eliminazione di un cluster

Una volta terminato l'uso di un cluster, usare il comando [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) per eliminarlo.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

L'eliminazione del gruppo di risorse elimina automaticamente anche tutti i cluster di cui è stato eseguito il provisioning sotto quel gruppo di risorse.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori esempi di creazione di un cluster di Batch per intelligenza artificiale, vedere la guida rapida per il [portale](quickstart-create-cluster-portal.md) o l’[interfaccia della riga di comando di Azure](quickstart-create-cluster-cli.md) o le [recipe di Batch per intelligenza artificiale](https://github.com/Azure/BatchAI/tree/master/recipes) su GitHub.
