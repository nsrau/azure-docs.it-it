---
title: Selezionare lo SKU di VM corretto per il cluster di Azure Esplora dati
description: Questo articolo descrive come selezionare le dimensioni ottimali dello SKU per il cluster Azure Esplora dati.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: b0cf6eab86b0b932e44b6824305c23df01f35808
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383812"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selezionare lo SKU di VM corretto per il cluster di Azure Esplora dati 

Azure Esplora dati dispone di più SKU di VM da cui scegliere durante la creazione di un nuovo cluster o l'ottimizzazione del cluster per un carico di lavoro modificabile. Le macchine virtuali sono state scelte con attenzione per consentire il costo ottimale per qualsiasi carico di lavoro. 

Le dimensioni e lo SKU di VM del cluster di gestione dati sono completamente gestiti dal servizio Esplora dati di Azure. È determinato da fattori quali le dimensioni della macchina virtuale del motore e il carico di lavoro di inserimento. 

Lo SKU della macchina virtuale per il cluster del motore può essere modificato in qualsiasi momento aumentando [il cluster](manage-cluster-vertical-scaling.md). Pertanto, è preferibile iniziare con la dimensione minima dello SKU adatta allo scenario iniziale. Tenere presente che la scalabilità verticale del cluster comporta tempi di inattività fino a 30 minuti mentre il cluster viene ricreato con il nuovo SKU della macchina virtuale.

> [!TIP]
> Il servizio di calcolo [ri (istanze riservate)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) è applicabile per il cluster Esplora dati di Azure.  

Questo articolo descrive le diverse opzioni dello SKU di VM e fornisce i dettagli tecnici che consentono di effettuare la scelta ottimale.

## <a name="select-the-cluster-type"></a>Selezionare il tipo di cluster

Azure Esplora dati offre due tipi di cluster:

* **Ambiente di produzione**: I cluster di produzione contengono due nodi per i cluster di gestione del motore e di dati e vengono gestiti con il [contratto](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)di servizio di Azure Esplora dati.

* **Sviluppo/test (senza SLA)** : I cluster di sviluppo/test includono un singolo nodo D11_v2 per il cluster di motore e un singolo nodo D1 per il cluster di gestione dati. Questo tipo di cluster è la configurazione di costo più basso a causa del numero di istanze basso e nessun addebito per il markup del motore. Non esiste alcun contratto di servizio per questa configurazione cluster perché manca la ridondanza.

## <a name="sku-types"></a>Tipi di SKU

Quando si crea un cluster Esplora dati di Azure, selezionare lo SKU di VM *ottimale* per il carico di lavoro pianificato. Azure Esplora dati dispone di due famiglie di SKU tra cui scegliere:

* **D_V2**: Lo SKU D è ottimizzato per il calcolo e fornito in due versioni.
    * VM stessa
    * VM in bundle con dischi di archiviazione Premium

* **LS**: Lo SKU L è ottimizzato per l'archiviazione. Il numero di unità SSD è molto maggiore rispetto allo SKU con prezzo equivalente **D** .

La tabella seguente illustra le differenze principali tra i tipi di SKU disponibili:
 
|**Attributo** | **SKU D** | **SKU L**
|---|---|---
|**SKU di piccole dimensioni**|Le dimensioni minime sono pari a 11' con due core|La dimensione minima è' L4' con quattro core
|**Disponibilità**|Disponibile in tutte le aree (la versione di DS + PS ha una disponibilità più limitata)|Disponibile in alcune aree geografiche
|**Costo per GB di cache per core**|Alta con lo SKU D, bassa con la versione DS + PS|Più economico con l'opzione con *pagamento in base* al consumo
|**Prezzi di RI (istanze riservate)**|Sconto elevato (oltre il 55% per un impegno di tre anni)|Sconto inferiore (20% per un impegno di tre anni)  

## <a name="select-your-cluster-vm"></a>Selezionare la macchina virtuale del cluster 

Per selezionare la macchina virtuale del cluster, [configurare la scalabilità verticale](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Le diverse opzioni dello SKU di VM consentono di ottimizzare i costi per le prestazioni e i requisiti della cache a caldo necessari per lo scenario desiderato. Se lo scenario richiede prestazioni ottimali per un volume di query elevato, lo SKU ideale dovrebbe essere ottimizzato per il calcolo. D'altra parte, se lo scenario richiede l'esecuzione di query su grandi volumi di dati con un carico di query relativamente inferiore, lo SKU ottimizzato per l'archiviazione ridurrà i costi garantendo allo stesso tempo prestazioni ottimali.

Il numero di istanze per cluster per gli SKU di piccole dimensioni è limitato, quindi è preferibile usare macchine virtuali di dimensioni maggiori con RAM maggiore. La dimensione della RAM è necessaria per alcuni tipi di query che richiedono più richieste sulla risorsa RAM, ad esempio le query che `joins`usano. Pertanto, quando si considerano le opzioni di scalabilità, è consigliabile eseguire la scalabilità verticale in uno SKU più grande rispetto alla scalabilità orizzontale aggiungendo più istanze.

## <a name="vm-options"></a>Opzioni VM

La tabella seguente fornisce le specifiche tecniche per le macchine virtuali del cluster Esplora dati di Azure:

|**Nome**| **Categoria** | **Dimensioni unità SSD** | **Core** | **RAM** | **Dischi di archiviazione Premium (1 TB)**| **Numero minimo di istanze per cluster** | **Numero massimo di istanze per cluster**
|---|---|---|---|---|---|---|---
|D11_v2| ottimizzato per il calcolo | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (ad eccezione dello SKU di sviluppo/test in cui è 1)
|D12_v2| ottimizzato per il calcolo | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| ottimizzato per il calcolo | 307 GB   | 8 | 56 GB | 0 | 2 | 1\.000
|D14_v2| ottimizzato per il calcolo | 614 GB   | 16| 112 GB | 0 | 2 | 1\.000
|DS13_v2 + 1 TB PS| ottimizzato per l'archiviazione | 1 TB | 8 | 56 GB | 1 | 2 | 1\.000
|DS13_v2 + 2 TB PS| ottimizzato per l'archiviazione | 2 TB | 8 | 56 GB | 2 | 2 | 1\.000
|DS14_v2 + 3TB PS| ottimizzato per l'archiviazione | 3 TB | 16 | 112 GB | 2 | 2 | 1\.000
|DS14_v2 + 4 TB PS| ottimizzato per l'archiviazione | 4 TB | 16 | 112 GB | 4 | 2 | 1\.000
|L4s_v1| ottimizzato per l'archiviazione | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| ottimizzato per l'archiviazione | 1,3 TB | 8 | 64 GB | 0 | 2 | 1\.000
|L16s_1| ottimizzato per l'archiviazione | 2,6 TB | 16| 128 GB | 0 | 2 | 1\.000

* Visualizzare l'elenco di SKU di VM aggiornato per area usando l' [API ListSkus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)di Azure Esplora dati. 
* Altre informazioni sui [diversi SKU di calcolo](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Passaggi successivi

* È possibile [aumentare o ridurre](manage-cluster-vertical-scaling.md) il cluster del motore in qualsiasi momento modificando lo SKU della macchina virtuale in base a esigenze diverse. 

* È possibile aumentare [o](manage-cluster-horizontal-scaling.md) ridurre le dimensioni del cluster del motore per modificare la capacità con richieste mutevoli.

