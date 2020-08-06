---
title: Serie Mv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie Mv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: f86400f1fb45217133bbd715788986994f5f77cc
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836259"
---
# <a name="mv2-series"></a>Serie Mv2

La serie Mv2 offre una piattaforma a bassa latenza e velocità effettiva elevata in esecuzione su un processore Intel® Xeon® Platinum 8180M a 2,5 GHz (Skylake) con una frequenza di base di tutti i core di 2,5 GHz e una frequenza massima turbo di 3,8 GHz. Tutte le dimensioni delle macchine virtuali della serie Mv2 possono usare dischi permanenti standard e Premium. Le istanze della serie Mv2 sono dimensioni di VM con ottimizzazione per la memoria che offrono prestazioni di calcolo ineguagliabili per supportare database e carichi di lavoro in memoria di grandi dimensioni, con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e analisi in memoria.

Funzionalità della VM della serie Mv2 Intel® tecnologia Hyper-Threading

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

Acceleratore di scrittura: [supportato](./windows/how-to-enable-write-accelerator.md)

|Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> le macchine virtuali della serie Mv2 sono solo di seconda generazione e supportano un subset di immagini supportate di seconda generazione. Per l'elenco completo delle immagini supportate per la serie Mv2, vedere di seguito. Se si usa Linux, per istruzioni su come trovare e selezionare un'immagine, vedere [supporto per le macchine virtuali di seconda generazione in Azure](./linux/generation-2.md) . Se si usa Windows, vedere [supporto per le macchine virtuali di seconda generazione in Azure](./windows/generation-2.md) per istruzioni su come trovare e selezionare un'immagine. 

- Windows Server 2019 o versione successiva
- SUSE Linux Enterprise Server 12 SP4 e versioni successive o SUSE Linux Enterprise Server 15 SP1 e versioni successive
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 o versione successiva 
- Oracle Enterprise Linux 7,7 o versione successiva



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [tipi di disco](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.