---
title: Scegliere le dimensioni delle macchine virtuali per i pool - Azure Batch | Microsoft Docs
description: Come scegliere tra le dimensioni delle macchine virtuali disponibili per i nodi di calcolo nei pool di Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: be19de19dab92bc40ca5529ad578e033a98929cd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023566"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Scegliere le dimensioni delle macchine virtuali per i nodi di calcolo in un pool di Azure Batch

Quando si selezionano le dimensioni del nodo per un pool di Azure Batch, è possibile scegliere tra quasi tutte le dimensioni di VM disponibili in Azure. Azure offre un'ampia gamma di dimensioni per le VM Linux e Windows per carichi di lavoro diversi.

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:

* Alcune serie di VM o dimensioni di VM non sono supportate in batch.
* Alcune dimensioni delle macchine virtuali sono soggette a restrizioni e devono essere abilitate specificamente prima che sia possibile allocarle.

## <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni delle VM supportate

### <a name="pools-in-virtual-machine-configuration"></a>Pool in configurazione di tipo macchina virtuale

I pool di batch nella configurazione della macchina virtuale supportano quasi tutte le dimensioni delle VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Per altre informazioni sulle dimensioni e sulle restrizioni supportate, vedere la tabella seguente.

Eventuali dimensioni di VM promozionali o di anteprima non elencate non sono garantite per il supporto.

| Serie VM  | Dimensioni supportate | Modalità di allocazione pool di account batch<sup>1</sup> |
|------------|---------|-----------------|
| Serie A Basic | Tutte le dimensioni *eccetto* Basic_A0 (a0) | Qualsiasi |
| Serie A | Tutte le dimensioni *eccetto* Standard_A0 | Qualsiasi |
| Serie Av2 | Tutte le dimensioni | Qualsiasi |
| Serie B | Nessuno | Non disponibile |
| Serie DC | Nessuno | Non disponibile |
| Dv2, serie DSv2 | Tutte le dimensioni | Qualsiasi |
| Dv3, serie Dsv3 | Tutte le dimensioni | Qualsiasi |
| EV3, serie Esv3 | Tutte le dimensioni | Qualsiasi |
| Serie Fsv2 | Tutte le dimensioni | Qualsiasi |
| Serie H | Tutte le dimensioni | Qualsiasi |
| Serie HB<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie HC<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie Ls | Tutte le dimensioni | Qualsiasi |
| Serie Lsv2 | Nessuno | Non disponibile |
| Serie M | Standard_M64ms (solo con priorità bassa), Standard_M128s (solo con priorità bassa) | Qualsiasi |
| Serie Mv2 | Nessuno | Non disponibile |
| Serie NC | Tutte le dimensioni | Qualsiasi |
| Serie NCv2<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie NCv3<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie ND<sup>2</sup> | Tutte le dimensioni | Qualsiasi |
| Serie NDv2 | Tutte le dimensioni | Modalità di sottoscrizione utente |
| Serie NV | Tutte le dimensioni | Qualsiasi |
| Serie NVv3 | Nessuno | Non disponibile |
| SAP HANA | Nessuno | Non disponibile |

<sup>1</sup> alcune serie di VM più recenti sono inizialmente supportate in parte. Queste serie di macchine virtuali possono essere allocate da account batch con la **modalità di allocazione pool** impostata su **sottoscrizione utente**. Per ulteriori informazioni sulla configurazione dell'account batch, vedere [gestire gli account batch](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) . Vedere [quote e limiti](batch-quota-limit.md) per informazioni su come richiedere una quota per queste serie di VM parzialmente supportate per gli account batch di **sottoscrizione utente** .  

<sup>2</sup> queste dimensioni delle VM possono essere allocate nei pool di batch nella configurazione della macchina virtuale, ma è necessario richiedere un [aumento della quota](batch-quota-limit.md#increase-a-quota)specifico.

### <a name="pools-in-cloud-service-configuration"></a>Pool in configurazione di tipo servizio cloud

I pool di batch nella configurazione del servizio cloud supportano tutte le [dimensioni delle macchine virtuali per i servizi cloud](../cloud-services/cloud-services-sizes-specs.md) , **ad eccezione** di quanto segue:

| Serie VM  | Dimensioni non supportate |
|------------|-------------------|
| Serie A   | Molto piccola       |
| Serie Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerazioni sulle dimensioni

* **Requisiti dell'applicazione**: tenere in considerazione le caratteristiche e i requisiti delle applicazioni che si eseguiranno nei nodi. Per determinare la dimensioni del nodo più appropriate e convenienti, considerare vari aspetti, ad esempio se si tratta di un'applicazione multithreading e la quantità di memoria che utilizza. Per [carichi di lavoro MPI](batch-mpi.md) a istanze multiple o applicazioni CUDA, prendere in considerazione rispettivamente le dimensioni specializzate [HPC](../virtual-machines/linux/sizes-hpc.md) o [abilitate per GPU](../virtual-machines/linux/sizes-gpu.md). Vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).

* **Attività per nodo**: le dimensioni del nodo vengono in genere selezionate presupponendo che in un nodo venga eseguita un'attività alla volta. Tuttavia, può risultare utile che più attività (e quindi più istanze dell'applicazione) vengano [eseguite in parallelo](batch-parallel-node-tasks.md) nei nodi di calcolo durante l'esecuzione del processo. In questo caso, è normale scegliere una dimensione multicore per il nodo per soddisfare la richiesta più elevata di esecuzione di attività parallele.

* **Livelli di carico per attività diverse**: tutti i nodi in un pool hanno le stesse dimensioni. Se si prevede di eseguire applicazioni con requisiti di sistema e/o livelli di carico diversi, è consigliabile usare pool separati.

* **Disponibilità dell'area** : è possibile che una o più serie di macchine virtuali non siano disponibili nelle aree in cui si creano gli account batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* **Quote** : la [quota di core](batch-quota-limit.md#resource-quotas) nell'account Batch può limitare il numero di nodi con le dimensioni specificate che possono essere aggiunti a un pool di Batch. Per richiedere un aumento delle quote, vedere [questo articolo](batch-quota-limit.md#increase-a-quota). 

* **Configurazione del pool**: in generale, quando si crea un pool in configurazione macchina virtuale si hanno più opzioni di dimensioni delle VM rispetto alla configurazione servizio cloud.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
* Per informazioni sull'uso di dimensioni delle VM a elevato utilizzo di calcolo, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).
