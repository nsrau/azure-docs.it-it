---
title: Serie Mv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie Mv2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: caa80443a189874bfd699dba412fb749c8711556
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493397"
---
# <a name="mv2-series"></a>Serie Mv2

La serie Mv2 offre una piattaforma a bassa latenza e velocità effettiva elevata in esecuzione su un processore Intel® Xeon® Platinum 8180M a 2,5 GHz (Skylake) con una frequenza di base di tutti i core di 2,5 GHz e una frequenza massima turbo di 3,8 GHz. Tutte le dimensioni delle macchine virtuali della serie Mv2 possono usare dischi permanenti standard e Premium. Le istanze della serie Mv2 sono dimensioni di VM con ottimizzazione per la memoria che offrono prestazioni di calcolo ineguagliabili per supportare database e carichi di lavoro in memoria di grandi dimensioni, con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e in memoria analisi.

Funzionalità della VM della serie Mv2 Intel® tecnologia Hyper-Threading

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Acceleratore di scrittura: [supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8/32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8/32000 |

<sup>1</sup> le macchine virtuali della serie Mv2 sono solo di generazione 2. Se si usa Linux, per istruzioni su come trovare e selezionare un'immagine, vedere [supporto per le macchine virtuali di seconda generazione in Azure](/linux/generation-2.md) .

<sup>2</sup> per le dimensioni M416ms_v2 e M416s_v2, si noti che è disponibile il supporto iniziale solo per l'immagine seguente: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 per le applicazioni SAP".

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.