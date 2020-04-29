---
title: Evento di creazione pool di Azure Batch
description: Riferimento per l'evento di creazione del pool di batch, che viene generato dopo la creazione di un pool. Il contenuto del registro espone informazioni generali sul pool.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 755fbe548b4be729d20788597db05f4d9678ebea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116554"
---
# <a name="pool-create-event"></a>Evento di creazione di pool

 Questo evento viene generato dopo aver creato un pool. Il contenuto del registro espone informazioni generali sul pool. Si noti che se le dimensioni del pool di destinazione sono maggiori di 0 nodi di calcolo, l'evento viene seguito immediatamente da un evento di avvio ridimensionamento pool.

 Nell'esempio seguente viene illustrato il corpo di un evento di creazione del pool per un pool `CloudServiceConfiguration` creato utilizzando la proprietà.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Type|Note|
|-------------|----------|-----------|
|`id`|Stringa|ID del pool.|
|`displayName`|Stringa|Nome visualizzato del pool.|
|`vmSize`|Stringa|Dimensioni delle macchine virtuali nel pool. Le macchine virtuali di un pool hanno tutte le stesse dimensioni. <br/><br/> Per informazioni sulle dimensioni disponibili per le macchine virtuali per i pool dei Servizi cloud (pool creati con cloudServiceConfiguration), vedere [Dimensioni dei servizi Cloud](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch supporta tutte le dimensioni delle VM dei Servizi cloud, ad eccezione di `ExtraSmall`.<br/><br/> Per informazioni sulle dimensioni delle VM disponibili per i pool che usano immagini del Marketplace delle macchine virtuali (pool creati con virtualMachineConfiguration), vedere [Dimensioni delle macchine virtuali](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) o [Dimensioni delle macchine virtuali](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch supporta tutte le dimensioni delle VM di Azure tranne `STANDARD_A0` e quelle con l'archiviazione Premium (serie `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|`imageType`|Stringa|Metodo di distribuzione per l'immagine. I valori supportati `virtualMachineConfiguration` sono o`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Tipo complesso|La configurazione del servizio cloud per il pool.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Tipo complesso|La configurazione della macchina virtuale per il pool.|
|[`networkConfiguration`](#bk_netconf)|Tipo complesso|La configurazione della rete per il pool.|
|`resizeTimeout`|Tempo|Il timeout per l'allocazione dei nodi di calcolo per il pool specificato per l'ultima operazione di ridimensionamento del pool.  L'impostazione iniziale delle dimensioni, eseguita quando si crea il pool, viene considerata come un ridimensionamento.|
|`targetDedicatedNodes`|Int32|Numero di nodi di calcolo dedicati richiesti per il pool.|
|`targetLowPriorityNodes`|Int32|Numero di nodi di calcolo con priorità bassa richiesti per il pool.|
|`enableAutoScale`|Bool|Specifica se le dimensioni del pool vengono regolate automaticamente nel tempo.|
|`enableInterNodeCommunication`|Bool|Specifica se il pool consente o meno la comunicazione diretta tra nodi.|
|`isAutoPool`|Bool|Specifica se il pool è stato creato tramite il meccanismo di pool automatico di un processo.|
|`maxTasksPerNode`|Int32|Numero massimo di attività che possono essere eseguite simultaneamente in un singolo nodo di calcolo nel pool.|
|`vmFillType`|Stringa|Definisce il modo in cui il servizio Batch distribuisce le attività tra i nodi di calcolo nel pool. I valori validi sono Spread o Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfiguration

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`osFamily`|Stringa|La famiglia del sistema operativo guest Azure da installare nelle macchine virtuali nel pool.<br /><br /> I valori possibili sono:<br /><br /> **2** - Famiglia 2 del sistema operativo, equivalente a Windows Server 2008 R2 SP1.<br /><br /> **3** - Famiglia 3 del sistema operativo, equivalente a Windows Server 2012.<br /><br /> **4** - Famiglia 4 del sistema operativo, equivalente a Windows Server 2012 R2.<br /><br /> Per altre informazioni, vedere [Rilasci del sistema operativo guest Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Stringa|La versione del sistema operativo guest Azure da installare nelle macchine virtuali nel pool.<br /><br /> Il valore predefinito è **\*** che specifica la versione più recente del sistema operativo per la famiglia specificata.<br /><br /> Per altri valori consentiti, vedere [Rilasci del sistema operativo guest Azure ](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Tipo complesso|Specifica le informazioni sulla piattaforma o sull'immagine del Marketplace da usare.|
|`nodeAgentId`|Stringa|Lo SKU dell'agente di nodo Batch sottoposto a provisioning nel nodo di calcolo.|
|[`windowsConfiguration`](#bk_winconf)|Tipo complesso|Specifica le impostazioni del sistema operativo Windows nella macchina virtuale. Questa proprietà non deve essere specificata se imageReference fa riferimento a un'immagine del sistema operativo Linux.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imageReference

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`publisher`|Stringa|L'autore dell'immagine.|
|`offer`|Stringa|L'offerta dell'immagine.|
|`sku`|Stringa|Lo SKU dell'immagine.|
|`version`|Stringa|La versione dell'immagine.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsConfiguration

|Nome dell'elemento|Type|Note|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|Indica se la macchina virtuale è abilitata per gli aggiornamenti automatici. Se questa proprietà non è specificata, viene usato il valore predefinito.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>networkConfiguration

|Nome dell'elemento|Type|Note|
|------------------|--------------|----------|
|`subnetId`|Stringa|Specifica l'identificatore di risorsa della subnet in cui vengono creati i nodi di calcolo del pool.|
