---
title: Scegliere le dimensioni delle macchine virtuali per i pool - Azure Batch | Microsoft Docs
description: Come scegliere tra le dimensioni delle macchine virtuali disponibili per i nodi di calcolo nei pool di Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 033e0865f23034b94e3133e0ba5890eca4e746ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080880"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Scegliere le dimensioni delle macchine virtuali per i nodi di calcolo in un pool di Azure Batch

Quando si selezionano le dimensioni del nodo per un pool di Azure Batch, è possibile scegliere tra quasi tutte le dimensioni di VM disponibili in Azure. Azure offre un'ampia gamma di dimensioni per le VM Linux e Windows per carichi di lavoro diversi.

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:

* Alcune serie di macchine Virtuali o dimensioni delle macchine Virtuali non sono supportati in Batch.
* Alcune dimensioni delle macchine virtuali sono soggette a restrizioni e devono essere abilitate specificamente prima che sia possibile allocarle.

## <a name="supported-vm-series-and-sizes"></a>Supportate macchine virtuali della serie e dimensioni

### <a name="pools-in-virtual-machine-configuration"></a>Pool in configurazione di tipo macchina virtuale

I pool di batch in configurazione della macchina virtuale supportano quasi tutte le dimensioni di macchina virtuale ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Vedere la tabella seguente per altre informazioni sulle dimensioni supportate e restrizioni.

Qualsiasi promozionali o dimensioni delle macchine Virtuali di anteprima non elencate non sono garantiti per il supporto.

| Serie VM  | Dimensioni supportate | Modalità di allocazione pool di account batch<sup>1</sup> |
|------------|---------|-----------------|
| Serie A Basic | Tutte le dimensioni *eccetto* Basic_A0 (A0) | Qualsiasi |
| Serie A | Tutte le dimensioni *eccetto* Standard_A0 | Qualsiasi |
| Serie Av2 | Tutte le dimensioni | Qualsiasi |
| Serie B | Nessuna | Non disponibile |
| Serie DC | Nessuna | Non disponibile |
| Dv2, serie Dsv2 | Tutte le dimensioni | Qualsiasi |
| Serie Dv3, serie Dsv3 | Tutte le dimensioni | Qualsiasi |
| [Dimensioni ottimizzate per la memoria](../virtual-machines/linux/sizes-memory.md) | Nessuna | Non disponibile |
| Serie Fsv2 | Tutte le dimensioni | Qualsiasi |
| Serie H | Tutte le dimensioni | Qualsiasi |
| Serie HB | Tutte le dimensioni | Modalità di sottoscrizione utente |
| Serie HC | Tutte le dimensioni | Modalità di sottoscrizione utente |
| Serie Ls | Tutte le dimensioni | Qualsiasi |
| Serie Lsv2 | Nessuna | Non disponibile |
| Serie M | Standard_M64ms Standard_M128s (con priorità bassa solo), (con priorità bassa solo) | Qualsiasi |  
| Serie NCv2<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie NCv3<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie ND<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| NDv2 serie | Tutte le dimensioni | Modalità di sottoscrizione utente |
| Serie NV | Tutte le dimensioni | Qualsiasi |
| Serie NVv3 | Nessuna | Non disponibile |
| SAP HANA | Nessuna | Non disponibile |

<sup>1</sup> alcune serie di macchine Virtuali più recenti sono parzialmente supportate inizialmente. Queste macchine virtuali della serie può essere allocata per gli account Batch con il **modalità di allocazione pool** impostata su **sottoscrizione utente**. Visualizzare [gli account Batch di gestione](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) per altre informazioni sulla configurazione dell'account Batch. Visualizzare [quote e limiti](batch-quota-limit.md) per informazioni su come richiedere una quota per queste parzialmente supportate macchine virtuali della serie per **sottoscrizione utente** account Batch.  

<sup>2</sup> queste dimensioni di VM possono essere allocate nel pool di Batch in configurazione macchina virtuale, ma è necessario richiedere uno specifico [aumento della quota](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pool in configurazione di tipo servizio cloud

I pool di Batch in configurazione servizio cloud supportano tutte le [dimensioni di VM per i servizi cloud](../cloud-services/cloud-services-sizes-specs.md) **tranne** le seguenti:

| Serie VM  | Dimensioni non supportate |
|------------|-------------------|
| Serie A   | Molto piccola       |
| Serie Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerazioni sulle dimensioni

* **Requisiti dell'applicazione**: tenere in considerazione le caratteristiche e i requisiti delle applicazioni che si eseguiranno nei nodi. Per determinare la dimensioni del nodo più appropriate e convenienti, considerare vari aspetti, ad esempio se si tratta di un'applicazione multithreading e la quantità di memoria che utilizza. Per [carichi di lavoro MPI](batch-mpi.md) a istanze multiple o applicazioni CUDA, prendere in considerazione rispettivamente le dimensioni specializzate [HPC](../virtual-machines/linux/sizes-hpc.md) o [abilitate per GPU](../virtual-machines/linux/sizes-gpu.md). Vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).

* **Attività per nodo**: le dimensioni del nodo vengono in genere selezionate presupponendo che in un nodo venga eseguita un'attività alla volta. Tuttavia, può risultare utile che più attività (e quindi più istanze dell'applicazione) vengano [eseguite in parallelo](batch-parallel-node-tasks.md) nei nodi di calcolo durante l'esecuzione del processo. In questo caso, è normale scegliere una dimensione multicore per il nodo per soddisfare la richiesta più elevata di esecuzione di attività parallele.

* **Livelli di carico per attività diverse**: tutti i nodi in un pool hanno le stesse dimensioni. Se si prevede di eseguire applicazioni con requisiti di sistema e/o livelli di carico diversi, è consigliabile usare pool separati.

* **Aree di disponibilità** -serie di macchine Virtuali o le dimensioni potrebbero non essere disponibili nelle aree in cui si crea l'account Batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* **Quote** : la [quota di core](batch-quota-limit.md#resource-quotas) nell'account Batch può limitare il numero di nodi con le dimensioni specificate che possono essere aggiunti a un pool di Batch. Per richiedere un aumento delle quote, vedere [questo articolo](batch-quota-limit.md#increase-a-quota). 

* **Configurazione del pool**: in generale, quando si crea un pool in configurazione macchina virtuale si hanno più opzioni di dimensioni delle VM rispetto alla configurazione servizio cloud.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
* Per informazioni sull'uso di dimensioni delle VM a elevato utilizzo di calcolo, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).
