---
title: Selezionare lo SKU della macchina virtuale corretto per il cluster di Azure Data ExplorerSelect correct VM SKU for your Azure Data Explorer cluster
description: Questo articolo descrive come selezionare la dimensione ottimale dello SKU per il cluster di Azure Data Explorer.This article describes how to select the optimal SKU size for Azure Data Explorer cluster.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561851"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selezionare lo SKU della macchina virtuale corretto per il cluster di Azure Data ExplorerSelect the correct VM SKU for your Azure Data Explorer cluster 

Quando si crea un nuovo cluster o si ottimizza un cluster per un carico di lavoro in evoluzione, Azure Data Explorer offre più SKU di macchine virtuali (VM) tra cui scegliere. Le macchine virtuali sono state scelte con cura per offrire il costo ottimale per qualsiasi carico di lavoro. 

Le dimensioni e lo SKU della macchina virtuale del cluster di gestione dei dati sono completamente gestiti dal servizio Azure Data Explorer.The size and VM SKU of the data-management cluster are fully managed by the Azure Data Explorer service. Sono determinati da fattori quali le dimensioni della macchina virtuale del motore e il carico di lavoro di inserimento. 

È possibile modificare lo SKU della macchina virtuale per il cluster del motore in qualsiasi momento [aumentando il cluster.](manage-cluster-vertical-scaling.md) È consigliabile iniziare con la dimensione SKU più piccola che si adatta allo scenario iniziale. Tenere presente che la scalabilità verticale del cluster comporta tempi di inattività fino a 30 minuti mentre il cluster viene ricreato con il nuovo SKU della macchina virtuale.

> [!TIP]
> Compute Reserved Instances (RI) è applicabile al cluster Azure Data Explorer.Compute [Reserved Instances (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) is applicable to the Azure Data Explorer cluster.  

Questo articolo descrive varie opzioni di SKU di VM e fornisce i dettagli tecnici che consentono di scegliere al meglio.

## <a name="select-a-cluster-type"></a>Selezionare un tipo di cluster

Azure Data Explorer offre due tipi di cluster:Azure Data Explorer offers two types of clusters:

* **Produzione:** i cluster di produzione contengono due nodi per i cluster di motore e di gestione dei dati e vengono gestiti con il servizio [di archiviazione di](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)Azure Data Explorer.

* **Dev/Test (nessun sLA)**: i cluster di sviluppo/test dispongono di un singolo nodo D11 v2 per il cluster del motore e di un singolo nodo D1 per il cluster di gestione dati. Questo tipo di cluster è la configurazione con il costo più basso a causa del numero di istanze basso e dell'addebito del markup del motore. Non esiste alcun sLA per questa configurazione del cluster, perché manca di ridondanza.

## <a name="sku-types"></a>Tipi di SKU

Quando si crea un cluster di Azure Data Explorer, selezionare lo SKU di vm *ottimale* per il carico di lavoro pianificato. È possibile scegliere tra le due famiglie di SKU di Azure Data Explorer seguenti:You can choose from the following two Azure Data Explorer SKU families:

* **D v2**: Lo SKU D è ottimizzato per il calcolo ed è disponibile in due gusti:
    * VM
    * La macchina virtuale in bundle con dischi di archiviazione Premium

* **LS**: Lo SKU L è ottimizzato per lo storage. Ha una dimensione SSD molto maggiore rispetto allo SKU D prezzo simile.

Le differenze principali tra i tipi di SKU disponibili sono descritte nella tabella seguente:The key differences between the available SKU types are described in the following table:
 
| Attributo | D SKU | L SKU |
|---|---|---
|**SKU piccoli**|La dimensione minima è D11 con due core|La dimensione minima è L4 con quattro core |
|**Disponibilità**|Disponibile in tutte le regioni (la versione DS-PS ha una disponibilità più limitata)|Disponibile in alcune regioni |
|**Costo&nbsp;per cache GB per core**|Alto con lo SKU D, basso con la versione DS-PS|Più basso con l'opzione Pay-As-You-Go |
|**Prezzi delle istanze riservate (RI)**|Sconto elevato (oltre&nbsp;il 55% per un impegno triennale)|Sconto inferiore (20%&nbsp;per un impegno triennale) |  

## <a name="select-your-cluster-vm"></a>Selezionare la macchina virtuale del clusterSelect your cluster VM 

Per selezionare la macchina virtuale del cluster, configurare la [scalabilità verticale.](manage-cluster-vertical-scaling.md#configure-vertical-scaling) 

Con varie opzioni di SKU di VM tra cui scegliere, è possibile ottimizzare i costi per i requisiti di prestazioni e hot-cache per lo scenario. 
* Se sono necessarie le prestazioni ottimali per un volume di query elevato, lo SKU ideale deve essere ottimizzato per il calcolo. 
* Se è necessario eseguire query su grandi volumi di dati con un carico di query relativamente inferiore, lo SKU ottimizzato per l'archiviazione consente di ridurre i costi e fornire comunque prestazioni eccellenti.

Poiché il numero di istanze per cluster per gli SKU di piccole dimensioni è limitato, è preferibile usare macchine virtuali di dimensioni maggiori con RAM maggiore. Sono necessarie più RAM per alcuni tipi di query che richiedono `joins`una maggiore richiesta alla risorsa RAM, ad esempio le query che utilizzano . Pertanto, quando si considerano le opzioni di scalabilità, è consigliabile passare a uno SKU più grande anziché aumentare la scalabilità orizzontale aggiungendo più istanze.

## <a name="vm-options"></a>Opzioni VM

Le specifiche tecniche per le macchine virtuali del cluster Azure Data Explorer sono descritte nella tabella seguente:The technical specifications for the Azure Data Explorer cluster VMs are described in the following table:

|**Nome**| **Category** | **Dimensioni SSD** | **Core** | **Ram** | **Dischi di archiviazione&nbsp;Premium (1 TB)**| **Numero minimo di istanze per cluster** | **Numero massimo di istanze per clusterMaximum instance count per cluster**
|---|---|---|---|---|---|---|---
|D11 v2| ottimizzato per il calcolo | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (ad eccezione dello SKU di sviluppo/test, che è 1)
|D12 v2| ottimizzato per il calcolo | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| ottimizzato per il calcolo | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1.000
|D14 v2| ottimizzato per il calcolo | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1.000
|DS13 v2&nbsp;-&nbsp;1 TB PS| ottimizzato per lo storage | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1.000
|DS13 v2&nbsp;-&nbsp;2 TB PS| ottimizzato per lo storage | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1.000
|DS14 v2&nbsp;-&nbsp;3 TB PS| ottimizzato per lo storage | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1.000
|DS14 v2&nbsp;-&nbsp;4 TB PS| ottimizzato per lo storage | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1.000
|L4s v1| ottimizzato per lo storage | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8 v1| ottimizzato per lo storage | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1.000
|L16s_1| ottimizzato per lo storage | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1.000

* È possibile visualizzare l'elenco di SKU vm aggiornato per ogni area usando [l'API ListSkus di](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)Azure Data Explorer. 
* Ulteriori informazioni sui [vari SKU](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Passaggi successivi

* È possibile [aumentare o ridurre](manage-cluster-vertical-scaling.md) le prestazioni del cluster del motore in qualsiasi momento modificando lo SKU della macchina virtuale, a seconda delle diverse esigenze. 

* È possibile [aumentare o ridurre](manage-cluster-horizontal-scaling.md) le dimensioni del cluster motore per modificare la capacità, a seconda delle esigenze che cambiano.

