---
title: Serie Eav4 e serie Easv4 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Eav4 ed Easv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: ab45298dbcb8988da7d0211fcbde3087d0099038
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437071"
---
# <a name="eav4-and-easv4-series"></a>Serie Eav4 e Easv4

La serie Eav4 e la serie Easv4 utilizzano il processore AMD 2.35Ghz EPYC<sup>TM</sup> 7452 in una configurazione multi-thread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro ottimizzati per la memoria. La serie Eav4 e la serie Easv4 hanno le stesse configurazioni di memoria e disco della serie Ev3 & Esv3.

## <a name="eav4-series"></a>Serie Eav4

ACU: 230 - 260

Archiviazione Premium: Non supportata

Memorizzazione nella cache di archiviazione Premium: non supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

Le dimensioni della serie Eav4 sono basate sul processore 2.35Ghz AMD EPYC<sup>TM</sup> 7452 che può raggiungere una frequenza massima potenziata di 3,35GHz e utilizzare SSD premium. Le dimensioni della serie Eav4 sono ideali per applicazioni aziendali che richiedono un uso intensivo della memoria. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per utilizzare sSD premium, utilizzare le dimensioni della serie Easv4. I prezzi e i contatori di fatturazione per le taglie Easv4 sono gli stessi della serie Eav3.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | NiDi massima / Larghezza di banda di rete prevista (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Standard\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Standard\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standard\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Standard\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| Standard\_E48a\_v4|48|384|1200|32|96000 / 1000 (500)|8 / 24000 |
| Standard\_E64a\_v4|64|512|1600|32|96000 / 1000 (500)|8 / 30000 |
| Standard\_E96a\_v4|96|672|2400|32|96000 / 1000 (500)|8 / 30000 |

## <a name="easv4-series"></a>Serie Easv4

ACU: 230 - 260

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

Le dimensioni della serie Easv4 sono basate sul processore 2.35Ghz AMD EPYC<sup>TM</sup> 7452 che può raggiungere una frequenza massima migliorata di 3,35 GHz e utilizzare SSD premium. Le dimensioni della serie Easv4 sono ideali per applicazioni aziendali che richiedono un uso intensivo della memoria.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NiDi massima / Larghezza di banda di rete prevista (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)|76800 / 1148|8 / 24000 |
| Standard_E64as_v4|64|512|1024|32|128000 / 1020 (1600)|80000 / 1200|8 / 30000 |
| Standard_E96as_v4|96|672|1344|32|192000 / 1020 (2400)|80000 / 1200|8 / 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
