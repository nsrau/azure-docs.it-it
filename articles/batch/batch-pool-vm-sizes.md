---
title: Scegliere le dimensioni delle macchine virtuali per i pool
description: Come scegliere tra le dimensioni delle macchine virtuali disponibili per i nodi di calcolo nei pool di Azure Batch
ms.topic: conceptual
ms.date: 06/10/2020
ms.custom: seodec18
ms.openlocfilehash: e56632ce66cb25bf023813f2b98be6141f651465
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143523"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Scegliere le dimensioni delle macchine virtuali per i nodi di calcolo in un pool di Azure Batch

Quando si selezionano le dimensioni del nodo per un pool di Azure Batch, è possibile scegliere tra quasi tutte le dimensioni di VM disponibili in Azure. Azure offre un'ampia gamma di dimensioni per le VM Linux e Windows per carichi di lavoro diversi.

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:

* Alcune famiglie o dimensioni di macchine virtuali non sono supportati in Azure Batch.
* Alcune dimensioni delle macchine virtuali sono soggette a restrizioni e devono essere abilitate specificamente prima che sia possibile allocarle.

## <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni di macchine virtuali supportate

### <a name="pools-in-virtual-machine-configuration"></a>Pool in configurazione di tipo macchina virtuale

I pool di Azure Batch in configurazione macchina virtuale supportano quasi tutte le dimensioni di VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Per altre informazioni sulle dimensioni e sulle restrizioni supportate, vedere la tabella seguente.

| Serie VM  | Dimensioni supportate |
|------------|---------|
| Basic A | Tutte le dimensioni *tranne* Basic_A0 (a0) |
| Una | Tutte le dimensioni *tranne* Standard_A0 |
| Av2 | Tutte le dimensioni |
| b | nessuno |
| DC | nessuno |
| Dv2, DSv2 | Tutte le dimensioni |
| Dv3, Dsv3 | Tutte le dimensioni |
| Dav4<sup>1</sup> | Nessuna - non ancora disponibile |
| Dasv4<sup>1</sup> | Tutte le dimensioni, ad eccezione di Standard_D48as_v4, Standard_D64as_v4, Standard_D96as_v4 |
| Ddv4, Ddsv4 |  Nessuna - non ancora disponibile |
| Ev3, Esv3 | Tutte le dimensioni, ad eccezione di E64is_v3 e E64i_v3 |
| Eav4<sup>1</sup> | Tutte le dimensioni, ad eccezione di Standard_E48a_v4, Standard_E64a_v4, Standard_E96a_v4 |
| Easv4<sup>1</sup> | Tutte le dimensioni, ad eccezione di Standard_E48as_v4, Standard_E64as_v4, Standard_E96as_v4 |
| Edv4, Edsv4 |  Nessuna - non ancora disponibile |
| F, Fs | Tutte le dimensioni |
| Fsv2 | Tutte le dimensioni |
| G, Gs | Tutte le dimensioni |
| H | Tutte le dimensioni |
| HB<sup>1</sup> | Tutte le dimensioni |
| HBv2<sup>1</sup> | Tutte le dimensioni |
| HC<sup>1</sup> | Tutte le dimensioni |
| Ls | Tutte le dimensioni |
| Lsv2<sup>1</sup> | Tutte le dimensioni |
| M<sup>1</sup> | Tutte le dimensioni |
| Mv2 | Nessuna - non ancora disponibile |
| NC | Tutte le dimensioni |
| NCv2<sup>1</sup> | Tutte le dimensioni |
| NCv3<sup>1</sup> | Tutte le dimensioni |
| ND<sup>1</sup> | Tutte le dimensioni |
| NDv2<sup>1</sup> | Nessuna - non ancora disponibile |
| NV | Tutte le dimensioni |
| NVv3<sup>1</sup> | Tutte le dimensioni |
| NVv4 | Nessuno |
| SAP HANA | nessuno |

<sup>1</sup> queste dimensioni delle macchine virtuali possono essere allocate nei pool di Azure Batch nella configurazione della macchina virtuale, ma è necessario creare un nuovo account Batch e richiedere un [aumento della quota](batch-quota-limit.md#increase-a-quota) specifico. Questa limitazione verrà rimossa quando la quota di vCPU per ogni serie di macchine virtuali sarà completamente supportata per gli account Batch.

### <a name="pools-in-cloud-service-configuration"></a>Pool in configurazione di tipo servizio cloud

I pool di Azure Batch in configurazione servizio cloud supportano tutte le [dimensioni di VM per i servizi cloud](../cloud-services/cloud-services-sizes-specs.md) **tranne** le seguenti:

| Serie VM  | Dimensioni non supportate |
|------------|-------------------|
| Serie A   | Extra Small       |
| Serie Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerazioni sulle dimensioni

* **Requisiti dell'applicazione**: tenere in considerazione le caratteristiche e i requisiti delle applicazioni che si eseguiranno nei nodi. Per determinare la dimensioni del nodo più appropriate e convenienti, considerare vari aspetti, ad esempio se si tratta di un'applicazione multithreading e la quantità di memoria che utilizza. Per [carichi di lavoro MPI](batch-mpi.md) a istanze multiple o applicazioni CUDA, prendere in considerazione rispettivamente le dimensioni specializzate [HPC](../virtual-machines/sizes-hpc.md) o [abilitate per GPU](../virtual-machines/sizes-gpu.md). Vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).

* **Attività per nodo**: le dimensioni del nodo vengono in genere selezionate presupponendo che in un nodo venga eseguita un'attività alla volta. Tuttavia, può risultare utile che più attività (e quindi più istanze dell'applicazione) vengano [eseguite in parallelo](batch-parallel-node-tasks.md) nei nodi di calcolo durante l'esecuzione del processo. In questo caso, è normale scegliere una dimensione multicore per il nodo per soddisfare la richiesta più elevata di esecuzione di attività parallele.

* **Livelli di carico per attività diverse**: tutti i nodi in un pool hanno le stesse dimensioni. Se si prevede di eseguire applicazioni con requisiti di sistema e/o livelli di carico diversi, è consigliabile usare pool separati.

* **Disponibilità a livello di area**: è possibile che una famiglia o dimensione di VM non sia disponibile nelle aree in cui si creano gli account Batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* **Quote** : la [quota di core](batch-quota-limit.md#resource-quotas) nell'account Batch può limitare il numero di nodi con le dimensioni specificate che possono essere aggiunti a un pool di Batch. Per richiedere un aumento delle quote, vedere [questo articolo](batch-quota-limit.md#increase-a-quota). 

* **Configurazione del pool**: in generale, quando si crea un pool in configurazione macchina virtuale si hanno più opzioni di dimensioni delle VM rispetto alla configurazione servizio cloud.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [Flusso di lavoro del servizio Azure Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
* Per informazioni sull'uso di dimensioni delle VM a elevato utilizzo di calcolo, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).
