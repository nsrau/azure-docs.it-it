---
title: Evento di creazione pool di Azure Batch | Microsoft Docs
description: "Riferimento per l’evento di creazione del pool di batch."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 67edaa55d7ccd00d4aebb309f11bcf95486e87fa
ms.lasthandoff: 04/22/2017

---

# <a name="pool-create-event"></a>Evento di creazione di pool

 Questo evento viene generato dopo aver creato un pool. Il contenuto del registro espone informazioni generali sul pool. Si noti che se le dimensioni del pool di destinazione sono maggiori di 0 nodi di calcolo, l'evento viene seguito immediatamente da un evento di avvio ridimensionamento pool.

 Nell'esempio seguente viene illustrato il corpo di un evento di creazione di pool per un pool creato usando la proprietà CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Note|
|-------------|----------|-----------|
|id|String|ID del pool.|
|displayName|String|Nome visualizzato del pool.|
|vmSize|String|Dimensioni delle macchine virtuali nel pool. Le macchine virtuali di un pool hanno tutte le stesse dimensioni. <br/><br/> Per informazioni sulle dimensioni disponibili per le macchine virtuali per i pool dei Servizi cloud (pool creati con cloudServiceConfiguration), vedere [Dimensioni dei servizi Cloud](http://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch supporta tutte le dimensioni delle VM dei Servizi cloud, ad eccezione di `ExtraSmall`.<br/><br/> Per informazioni sulle dimensioni delle VM disponibili per i pool che usano immagini del Marketplace delle macchine virtuali (pool creati con virtualMachineConfiguration), vedere [Dimensioni delle macchine virtuali](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) o [Dimensioni delle macchine virtuali](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch supporta tutte le dimensioni delle VM di Azure tranne `STANDARD_A0` e quelle con l'archiviazione Premium (serie `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Tipo complesso|La configurazione del servizio cloud per il pool.|
|[virtualMachineConfiguration](#bk_vmconf)|Tipo complesso|La configurazione della macchina virtuale per il pool.|
|[networkConfiguration](#bk_netconf)|Tipo complesso|La configurazione della rete per il pool.|
|resizeTimeout|Time|Il timeout per l'allocazione dei nodi di calcolo per il pool specificato per l'ultima operazione di ridimensionamento del pool.  L'impostazione iniziale delle dimensioni, eseguita quando si crea il pool, viene considerata come un ridimensionamento.|
|targetDedicated|Int32|Numero di nodi di calcolo richiesti per il pool.|
|enableAutoScale|Booleano|Specifica se le dimensioni del pool vengono regolate automaticamente nel tempo.|
|enableInterNodeCommunication|Booleano|Specifica se il pool consente o meno la comunicazione diretta tra nodi.|
|isAutoPool|Booleano|Specifica se il pool è stato creato tramite il meccanismo di pool automatico di un processo.|
|maxTasksPerNode|Int32|Numero massimo di attività che possono essere eseguite simultaneamente in un singolo nodo di calcolo nel pool.|
|vmFillType|String|Definisce il modo in cui il servizio Batch distribuisce le attività tra i nodi di calcolo nel pool. I valori validi sono Spread o Pack.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Nome dell'elemento|Tipo|Note|
|------------------|----------|-----------|
|osFamily|String|La famiglia del sistema operativo guest Azure da installare nelle macchine virtuali nel pool.<br /><br /> I valori possibili sono:<br /><br /> **2** - Famiglia 2 del sistema operativo, equivalente a Windows Server 2008 R2 SP1.<br /><br /> **3** - Famiglia 3 del sistema operativo, equivalente a Windows Server 2012.<br /><br /> **4** - Famiglia 4 del sistema operativo, equivalente a Windows Server 2012 R2.<br /><br /> Per altre informazioni, vedere [Rilasci del sistema operativo guest Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|String|La versione del sistema operativo guest Azure da installare nelle macchine virtuali nel pool.<br /><br /> Il valore predefinito è  **\*** , che specifica la versione più recente del sistema operativo per la famiglia specificata.<br /><br /> Per altri valori consentiti, vedere [Rilasci del sistema operativo guest Azure ](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Nome dell'elemento|Tipo|Note|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Tipo complesso|Specifica le informazioni sulla piattaforma o sull'immagine del Marketplace da usare.|
|nodeAgentSKUId|String|Lo SKU dell'agente di nodo Batch sottoposto a provisioning nel nodo di calcolo.|
|[windowsConfiguration](#bk_winconf)|Tipo complesso|Specifica le impostazioni del sistema operativo Windows nella macchina virtuale. Questa proprietà non deve essere specificata se imageReference fa riferimento a un'immagine del sistema operativo Linux.|

###  <a name="bk_imgref"></a> imageReference

|Nome dell'elemento|Tipo|Note|
|------------------|----------|-----------|
|publisher|String|L'autore dell'immagine.|
|offer|String|L'offerta dell'immagine.|
|sku|String|Lo SKU dell'immagine.|
|version|String|La versione dell'immagine.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Nome dell'elemento|Tipo|Note|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolean|Indica se la macchina virtuale è abilitata per gli aggiornamenti automatici. Se questa proprietà non è specificata, viene usato il valore predefinito.|

###  <a name="bk_netconf"></a> networkConfiguration

|Nome dell'elemento|Tipo|Note|
|------------------|--------------|----------|
|subnetId|String|Specifica l'identificatore di risorsa della subnet in cui vengono creati i nodi di calcolo del pool.|

