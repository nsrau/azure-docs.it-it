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
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087054"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Scegliere le dimensioni delle macchine virtuali per i nodi di calcolo in un pool di Azure Batch

Quando si selezionano le dimensioni del nodo per un pool di Azure Batch, è possibile scegliere tra quasi tutte le dimensioni di VM disponibili in Azure. Azure offre un'ampia gamma di dimensioni per le VM Linux e Windows per carichi di lavoro diversi.

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:

* Alcune serie di macchine virtuali o dimensioni di VM non sono supportate in Batch.Some VM series or VM sizes are not supported in Batch.
* Alcune dimensioni delle macchine virtuali sono soggette a restrizioni e devono essere abilitate specificamente prima che sia possibile allocarle.

## <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni delle macchine virtuali supportateSupported VM series and sizes

### <a name="pools-in-virtual-machine-configuration"></a>Pool in configurazione di tipo macchina virtuale

I pool batch nella configurazione della macchina virtuale supportano quasi tutte le dimensioni delle macchine virtuali ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Per altre informazioni sulle dimensioni e le restrizioni supportate, vedere la tabella seguente.

| Serie VM  | Dimensioni supportate |
|------------|---------|
| Basic A | Tutte le *taglie tranne* Basic_A0 (A0) |
| Una  | Tutte le *taglie tranne* Standard_A0 |
| Av2 | Tutte le taglie |
| b | nessuno |
| DC | nessuno |
| Dv2, DSv2 | Tutte le taglie |
| Dv3, Dsv3 | Tutte le taglie |
| Dav4, Dasv4 | Nessuno - non ancora disponibile |
| Ev3, Esv3 | Tutte le taglie, ad eccezione di E64is_v3 e E64i_v3 |
| Eav4, Easv4 | Nessuno - non ancora disponibile |
| F, Fs | Tutte le taglie |
| Fsv2 | Tutte le taglie |
| G, Gs | Tutte le taglie |
| H | Tutte le taglie |
| HB<sup>1</sup> | Tutte le taglie |
| HBv2<sup>1</sup> | Tutte le taglie |
| HC<sup>1</sup> | Tutte le taglie |
| Ls | Tutte le taglie |
| Lsv2 | Nessuno - non ancora disponibile |
| M<sup>1</sup> | Tutte le taglie, ad eccezione di M64, M64m, M128, M128m |
| Mv2 (in tisa 2 | Nessuno - non ancora disponibile |
| NC | Tutte le taglie |
| NCv2<sup>1</sup> | Tutte le taglie |
| NCv3<sup>1</sup> | Tutte le taglie |
| ND<sup>1</sup> | Tutte le taglie |
| NDv2<sup>1</sup> | Nessuno - non ancora disponibile |
| NV | Tutte le taglie |
| NVv3<sup>1</sup> | Tutte le taglie |
| NVv4 | nessuno |
| SAP HANA | nessuno |

<sup>1</sup> Queste dimensioni di VM possono essere allocate nei pool Batch nella configurazione della macchina virtuale, ma è necessario creare un nuovo account Batch e richiedere un [aumento](batch-quota-limit.md#increase-a-quota)della quota specifico. Questa limitazione verrà rimossa una volta che la quota vCPU per ogni serie VM è completamente supportata per gli account Batch.This limitation will be removed once vCPU quota per VM series is fully supported for Batch accounts.

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

* **Disponibilità** dell'area: una serie o una dimensione di macchina virtuale potrebbe non essere disponibile nelle aree in cui si creano gli account Batch.Region availability - A VM series or size might not be available in the regions where you create your Batch accounts. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* **Quote **: la [quota di core](batch-quota-limit.md#resource-quotas) nell'account Batch può limitare il numero di nodi con le dimensioni specificate che possono essere aggiunti a un pool di Batch. Per richiedere un aumento delle quote, vedere [questo articolo](batch-quota-limit.md#increase-a-quota). 

* **Configurazione del pool**: in generale, quando si crea un pool in configurazione macchina virtuale si hanno più opzioni di dimensioni delle VM rispetto alla configurazione servizio cloud.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
* Per informazioni sull'uso di dimensioni delle VM a elevato utilizzo di calcolo, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).
