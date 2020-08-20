---
title: Serie Ev4 e Esv4-macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Ev4 e Esv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 94c47369d7b638640fab1971801177103779e896
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648939"
---
# <a name="ev4-and-esv4-series"></a>Serie Ev4 e Esv4

La serie Ev4 e Esv4 &reg; è in esecuzione sui processori Intel Xeon &reg; Platinum 8272CL (Cascade Lake) in una configurazione con Hyper-Threading, ideale per varie applicazioni aziendali con utilizzo intensivo di memoria e funzionalità fino a 504GIB di RAM.

> [!NOTE]
> Per le domande frequenti, vedere  [dimensioni delle macchine virtuali di Azure senza disco temporaneo locale](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Serie Ev4

Le dimensioni della serie Ev4 vengono eseguite su Intel Xeon &reg; Platinum 8272CL (Cascade Lake). Le istanze della serie Ev4 sono ideali per applicazioni aziendali a uso intensivo di memoria. Le macchine virtuali serie Ev4 includono la &reg; tecnologia Hyper-Threading Intel.

L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali. Per usare i dischi di archiviazione Premium, usare le dimensioni Esv4. I prezzi e i contatori di fatturazione per le dimensioni Esv4 sono uguali a quelli della serie Ev4.

> [!IMPORTANT]
> Queste nuove dimensioni sono attualmente in anteprima pubblica. [Qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)è possibile iscriversi a queste serie di Ev4 e Esv4. 

ACU: 195 - 210

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Solo archiviazione remota | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Solo archiviazione remota | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Solo archiviazione remota | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Solo archiviazione remota | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Solo archiviazione remota | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Solo archiviazione remota | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Solo archiviazione remota | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Solo archiviazione remota | 32| 8|30000 |


## <a name="esv4-series"></a>Serie Esv4

Le dimensioni della serie Esv4 vengono eseguite su Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Le istanze della serie Esv4 sono ideali per applicazioni aziendali a uso intensivo di memoria. Le macchine virtuali serie Evs4 includono la &reg; tecnologia Hyper-Threading Intel. L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali.

> [!IMPORTANT]
> Queste nuove dimensioni sono attualmente in anteprima pubblica. [Qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)è possibile iscriversi a queste serie di Ev4 e Esv4. 

ACU: 195-210

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima nella cache: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Solo archiviazione remota | 4 | 19000/120 (50) | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Solo archiviazione remota | 8 | 38500/242 (100) | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Solo archiviazione remota | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Solo archiviazione remota | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Solo archiviazione remota | 32 | 193000/1211 (500) | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Solo archiviazione remota | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Solo archiviazione remota | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Solo archiviazione remota | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [dimensioni core vincolate disponibili](./windows/constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [tipi di disco](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.