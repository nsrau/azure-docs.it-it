---
title: Serie HC-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie HC.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b73051ab7cc122f1f371ddac9c77e7243bfa0d73
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923544"
---
# <a name="hc-series"></a>Serie HC

Le macchine virtuali della serie HC sono ottimizzate per le applicazioni basate su un calcolo denso, ad esempio l'analisi degli elementi limitati implicita, le dinamiche molecolari e la chimica computazionale. Macchine virtuali HC funzionalità 44 Core processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta il ricco ecosistema di strumenti software di Intel, ad esempio la libreria del kernel matematico di Intel.

ACU: 297-315

Archiviazione Premium: supportata

Memorizzazione nella cache Archiviazione Premium: supportata

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | NIC Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | Tutti | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [configurazione delle macchine virtuali](./workloads/hpc/configure.md), sull' [Abilitazione di InfiniBand](./workloads/hpc/enable-infiniband.md), sulla configurazione di [MPI](./workloads/hpc/setup-mpi.md)e sull'ottimizzazione di applicazioni HPC per Azure in [carichi di lavoro HPC](./workloads/hpc/overview.md).
- Per informazioni sugli annunci più recenti e su alcuni esempi e risultati HPC, vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione dell'architettura di alto livello dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
