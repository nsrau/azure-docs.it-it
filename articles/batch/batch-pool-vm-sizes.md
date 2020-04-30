---
title: Scegliere le dimensioni delle macchine virtuali per i pool
description: Come scegliere tra le dimensioni delle macchine virtuali disponibili per i nodi di calcolo nei pool di Azure Batch
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d33b97790d2ce9a276a552fd02fe28c796234b15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115857"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Scegliere le dimensioni delle macchine virtuali per i nodi di calcolo in un pool di Azure Batch

Quando si selezionano le dimensioni del nodo per un pool di Azure Batch, è possibile scegliere tra quasi tutte le dimensioni di VM disponibili in Azure. Azure offre un'ampia gamma di dimensioni per le VM Linux e Windows per carichi di lavoro diversi.

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:

* Alcune serie di VM o dimensioni di VM non sono supportate in batch.
* Alcune dimensioni delle macchine virtuali sono soggette a restrizioni e devono essere abilitate specificamente prima che sia possibile allocarle.

## <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni delle VM supportate

### <a name="pools-in-virtual-machine-configuration"></a>Pool in configurazione di tipo macchina virtuale

I pool di batch nella configurazione della macchina virtuale supportano quasi tutte le dimensioni delle VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Per altre informazioni sulle dimensioni e sulle restrizioni supportate, vedere la tabella seguente.

| Serie VM  | Dimensioni supportate |
|------------|---------|
| Basic A | Tutte le dimensioni *eccetto* Basic_A0 (a0) |
| Una | Tutte le dimensioni *eccetto* Standard_A0 |
| Av2 | Tutte le dimensioni |
| b | Nessuno |
| DC | Nessuno |
| Dv2, DSv2 | Tutte le dimensioni |
| Dv3, Dsv3 | Tutte le dimensioni |
| Dav4, Dasv4 | Nessuno-non ancora disponibile |
| EV3, Esv3 | Tutte le dimensioni, ad eccezione di E64is_v3 e E64i_v3 |
| Eav4, Easv4 | Nessuno-non ancora disponibile |
| F, FS | Tutte le dimensioni |
| Fsv2 | Tutte le dimensioni |
| G, GS | Tutte le dimensioni |
| H | Tutte le dimensioni |
| HB<sup>1</sup> | Tutte le dimensioni |
| HBv2<sup>1</sup> | Tutte le dimensioni |
| HC<sup>1</sup> | Tutte le dimensioni |
| Ls | Tutte le dimensioni |
| Lsv2 | Nessuno-non ancora disponibile |
| M<sup>1</sup> | Tutte le dimensioni, ad eccezione di M64, M64m, M128, M128m |
| Mv2 | Nessuno-non ancora disponibile |
| NC | Tutte le dimensioni |
| NCv2<sup>1</sup> | Tutte le dimensioni |
| NCv3<sup>1</sup> | Tutte le dimensioni |
| ND<sup>1</sup> | Tutte le dimensioni |
| NDv2<sup>1</sup> | Nessuno-non ancora disponibile |
| NV | Tutte le dimensioni |
| NVv3<sup>1</sup> | Tutte le dimensioni |
| NVv4 | Nessuno |
| SAP HANA | Nessuno |

<sup>1</sup> le dimensioni di queste VM possono essere allocate nei pool di batch nella configurazione della macchina virtuale, ma è necessario creare un nuovo account batch e richiedere un [aumento della quota](batch-quota-limit.md#increase-a-quota)specifico. Questa limitazione verrà rimossa quando la quota di vCPU per ogni serie di macchine virtuali è completamente supportata per gli account batch.

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

* **Disponibilità dell'area** : è possibile che una o più serie di macchine virtuali non siano disponibili nelle aree in cui si creano gli account batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* **Quote **: la [quota di core](batch-quota-limit.md#resource-quotas) nell'account Batch può limitare il numero di nodi con le dimensioni specificate che possono essere aggiunti a un pool di Batch. Per richiedere un aumento delle quote, vedere [questo articolo](batch-quota-limit.md#increase-a-quota). 

* **Configurazione del pool**: in generale, quando si crea un pool in configurazione macchina virtuale si hanno più opzioni di dimensioni delle VM rispetto alla configurazione servizio cloud.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
* Per informazioni sull'uso di dimensioni delle VM a elevato utilizzo di calcolo, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).
