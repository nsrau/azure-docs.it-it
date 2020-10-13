---
title: Serie H-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 56112107bbd1bb506fcbff615ec524e043d54d1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653724"
---
# <a name="h-series"></a>Serie H

Le macchine virtuali serie H sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o per i requisiti di memoria di grandi dimensioni. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, fino a 14 GB di RAM per core CPU e senza hyperthreading. Le funzionalità della serie H 56 GB/sec Mellanox FDR InfiniBand in una configurazione di albero FAT non bloccante per prestazioni RDMA coerenti. Le macchine virtuali della serie H non sono attualmente abilitate per SR-IOV e supportano Intel MPI 5. x e MS-MPI.

[ACU](acu.md): 290-300<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1<br>
<br>

| Dimensione | vCPU | Processore | Memoria (GiB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Spazio di archiviazione temp (GiB) | Numero massimo di dischi dati | Velocità effettiva del disco max: IOPS | NIC Ethernet max |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 V3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 V3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> Tra le [macchine virtuali](sizes-hpc.md#rdma-capable-instances)con supporto per RDMA, la serie H non è-SR-IOV abilitata. Pertanto, le [Immagini di VM](./workloads/hpc/configure.md#vm-images)supportate, i requisiti dei [driver InfiniBand](./workloads/hpc/enable-infiniband.md) e le [librerie MPI](./workloads/hpc/setup-mpi.md) supportate sono diverse dalle macchine virtuali abilitate per SR-IOV.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [configurazione delle macchine virtuali](./workloads/hpc/configure.md), sull' [Abilitazione di InfiniBand](./workloads/hpc/enable-infiniband.md), sulla configurazione di [MPI](./workloads/hpc/setup-mpi.md) e sull'ottimizzazione di applicazioni HPC per Azure in [carichi di lavoro HPC](./workloads/hpc/overview.md).
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
