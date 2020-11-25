---
title: Scegliere le dimensioni e le immagini delle macchine virtuali per i pool
description: Come scegliere tra le dimensioni delle macchine virtuali disponibili e le versioni del sistema operativo per i nodi di calcolo nei pool di Azure Batch
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb54a4db62f56f442f7cec81e6768241a05ffee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895231"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Scegliere le dimensioni e l'immagine di una macchina virtuale per i nodi di calcolo in un pool di Azure Batch

Quando si selezionano le dimensioni del nodo per un pool di Azure Batch, è possibile scegliere tra quasi tutte le dimensioni di VM disponibili in Azure. Azure offre un'ampia gamma di dimensioni per le VM Linux e Windows per carichi di lavoro diversi.

## <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni di macchine virtuali supportate

Esistono alcune eccezioni e limitazioni per la scelta delle dimensioni della macchina virtuale per il pool di batch:

- Alcune famiglie o dimensioni di macchine virtuali non sono supportati in Azure Batch.
- Alcune dimensioni delle macchine virtuali sono soggette a restrizioni e devono essere abilitate specificamente prima che sia possibile allocarle.

### <a name="pools-in-virtual-machine-configuration"></a>Pool in configurazione di tipo macchina virtuale

I pool di batch nella configurazione della macchina virtuale supportano quasi tutte le [dimensioni delle VM](../virtual-machines/sizes.md). Per altre informazioni sulle dimensioni e sulle restrizioni supportate, vedere la tabella seguente.

| Serie VM  | Dimensioni supportate |
|------------|---------|
| Basic A | Tutte le dimensioni *tranne* Basic_A0 (a0) |
| Una | Tutte le dimensioni *tranne* Standard_A0 |
| Av2 | Tutte le dimensioni |
| b | Non supportate |
| DC | Non supportate |
| Dv2, DSv2 | Tutte le dimensioni |
| Dv3, Dsv3 | Tutte le dimensioni |
| Dav4<sup>1</sup> | Tutte le dimensioni |
| Dasv4<sup>1</sup> | Tutte le dimensioni |
| Ddv4, Ddsv4 |  Tutte le dimensioni |
| DV4, Dsv4 | Non supportate |
| Ev3, Esv3 | Tutte le dimensioni, ad eccezione di E64is_v3 |
| Eav4<sup>1</sup> | Tutte le dimensioni |
| Easv4<sup>1</sup> | Tutte le dimensioni |
| Edv4, Edsv4 |  Tutte le dimensioni |
| Ev4, Esv4 | Non supportate |
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
| Mv2<sup>1, 2</sup> | Tutte le dimensioni |
| NC | Tutte le dimensioni |
| NCv2<sup>1</sup> | Tutte le dimensioni |
| NCv3<sup>1</sup> | Tutte le dimensioni |
| NCasT4_v3 | Nessuna - non ancora disponibile |
| ND<sup>1</sup> | Tutte le dimensioni |
| NDv2<sup>1</sup> | Nessuna - non ancora disponibile |
| NV | Tutte le dimensioni |
| NVv3<sup>1</sup> | Tutte le dimensioni |
| NVv4<sup>1</sup> | Tutte le dimensioni |
| SAP HANA | Non supportate |

<sup>1</sup> queste serie di VM possono essere allocate nei pool di batch nella configurazione della macchina virtuale, ma è necessario creare un nuovo account batch e richiedere un [aumento della quota](batch-quota-limit.md#increase-a-quota)specifico. Questa limitazione verrà rimossa quando la quota di vCPU per ogni serie di macchine virtuali sarà completamente supportata per gli account Batch.

<sup>2</sup> queste serie di macchine virtuali possono essere usate solo con le immagini di macchina virtuale di seconda generazione.

### <a name="using-generation-2-vm-images"></a>Uso delle immagini di VM di seconda generazione

Alcune serie di macchine virtuali, ad esempio [Mv2](../virtual-machines/mv2-series.md), possono essere usate solo con le [Immagini di macchine virtuali di seconda generazione](../virtual-machines/generation-2.md). Le immagini di macchina virtuale di seconda generazione vengono specificate come qualsiasi immagine di macchina virtuale, usando la proprietà' SKU ' della configurazione [' imageReference '](/rest/api/batchservice/pool/add#imagereference) . le stringhe ' SKU ' hanno un suffisso, ad esempio "-G2" o "-Gen2". Per ottenere un elenco delle immagini di VM supportate da batch, incluse le immagini di generazione 2, usare l'API [' Elenca immagini supportate '](/rest/api/batchservice/account/listsupportedimages) , [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)o l'interfaccia della riga di comando di [Azure](/cli/azure/batch/pool/supported-images).

### <a name="pools-in-cloud-service-configuration"></a>Pool in configurazione di tipo servizio cloud

I pool di Azure Batch in configurazione servizio cloud supportano tutte le [dimensioni di VM per i servizi cloud](../cloud-services/cloud-services-sizes-specs.md) **tranne** le seguenti:

| Serie VM  | Dimensioni non supportate |
|------------|-------------------|
| Serie A   | Extra Small       |
| Serie Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerazioni sulle dimensioni

- **Requisiti dell'applicazione**: tenere in considerazione le caratteristiche e i requisiti delle applicazioni che si eseguiranno nei nodi. Per determinare la dimensioni del nodo più appropriate e convenienti, considerare vari aspetti, ad esempio se si tratta di un'applicazione multithreading e la quantità di memoria che utilizza. Per [carichi di lavoro MPI](batch-mpi.md) a istanze multiple o applicazioni CUDA, prendere in considerazione rispettivamente le dimensioni specializzate [HPC](../virtual-machines/sizes-hpc.md) o [abilitate per GPU](../virtual-machines/sizes-gpu.md). Per altre informazioni, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).

- **Attività per nodo**: le dimensioni del nodo vengono in genere selezionate presupponendo che in un nodo venga eseguita un'attività alla volta. Tuttavia, può risultare utile che più attività (e quindi più istanze dell'applicazione) vengano [eseguite in parallelo](batch-parallel-node-tasks.md) nei nodi di calcolo durante l'esecuzione del processo. In questo caso, è normale scegliere una dimensione multicore per il nodo per soddisfare la richiesta più elevata di esecuzione di attività parallele.

- **Livelli di carico per attività diverse**: tutti i nodi in un pool hanno le stesse dimensioni. Se si prevede di eseguire applicazioni con requisiti di sistema e/o livelli di carico diversi, è consigliabile usare pool separati.

- **Disponibilità a livello di area**: è possibile che una famiglia o dimensione di VM non sia disponibile nelle aree in cui si creano gli account Batch. Per verificare la disponibilità di una dimensione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

- **Quote** : la [quota di core](batch-quota-limit.md#resource-quotas) nell'account Batch può limitare il numero di nodi con le dimensioni specificate che possono essere aggiunti a un pool di Batch. Quando necessario, è possibile [richiedere un aumento della quota](batch-quota-limit.md#increase-a-quota).

- **Configurazione del pool**: in generale, quando si crea un pool in configurazione macchina virtuale si hanno più opzioni di dimensioni delle VM rispetto alla configurazione servizio cloud.

## <a name="supported-vm-images"></a>Immagini delle VM supportate

Usare una delle API seguenti per restituire un elenco di immagini di macchine virtuali Windows e Linux attualmente supportate da batch, inclusi gli ID SKU dell'agente del nodo per ogni immagine:

- API REST del servizio batch: [elencare le immagini supportate](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- INTERFACCIA della riga [di comando di Azure: AZ batch pool supported-images](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Azure Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Per informazioni sull'uso di dimensioni delle VM a elevato utilizzo di calcolo, vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).
