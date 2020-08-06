---
title: Serie Dv3 e Dsv3
description: Specifiche per le macchine virtuali serie dv3 e Dsv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e4d5f2527c0ab8ff4026b93fd2250e324244634e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829238"
---
# <a name="dv3-and-dsv3-series"></a>Serie Dv3 e Dsv3

La serie dv3 viene eseguita su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) in una configurazione con hyperthreading, fornendo una proposta di valore migliore per i carichi di lavoro per utilizzo generico La memoria è stata estesa (da ~3,5 GiB/vCPU a 4 GiB/vCPU) mentre i limiti di rete e dei dischi sono stati modificati in base al core per consentire l'allineamento con il passaggio all'hyperthreading. La serie Dv3 non ha più le dimensioni delle macchine virtuali con memoria elevata della serie D/Dv2, che sono state spostate nella nuova [serie Ev3 e Esv3](ev3-esv3-series.md) ottimizzata per la memoria.

Esempi di casi d'uso di serie D sono le applicazioni di livello aziendale, i database relazionali, la memorizzazione nella cache in memoria e l'analisi.

## <a name="dv3-series"></a>Serie Dv3

Le dimensioni della serie dv3 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) con la tecnologia Intel Turbo Boost 2,0. Le dimensioni della serie Dv3 offrono una potente combinazione di vCPU, memoria e spazio di archiviazione temporaneo ideale per la maggior parte delle applicazioni di produzione.

L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare dischi di archiviazione Premium, usare le dimensioni Dsv3. I prezzi e i contatori di fatturazione per le dimensioni Dsv3 sono uguali a quelli della serie Dv3.

Le macchine virtuali serie dv3 includono tecnologia Intel® Hyper-Threading.

Unità di calcolo di Azure: 160-190

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Schede di interfaccia di rete max|Larghezza di banda di rete |
|---|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2|1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8|24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8|30000 |

## <a name="dsv3-series"></a>Serie Dsv3

Le dimensioni della serie Dsv3 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) con la tecnologia Intel Turbo Boost 2,0 e usano archiviazione Premium. Le dimensioni della serie Dsv3 offrono una potente combinazione di vCPU, memoria e spazio di archiviazione temporaneo ideale per la maggior parte delle applicazioni di produzione.

Le macchine virtuali serie Dsv3 includono tecnologia Intel® Hyper-Threading.

Unità di calcolo di Azure: 160-190

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2|1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2|2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4|4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8|8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

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