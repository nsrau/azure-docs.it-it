---
title: Serie Ddv4 e Ddsv4
description: Specifiche per le macchine virtuali serie Dv4, Ddv4, Dsv4 e Ddsv4.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 6367a3c3e6a946068498c92456ba42cd3c7c4bdd
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042627"
---
# <a name="ddv4-and-ddsv4-series"></a>Serie Ddv4 e Ddsv4

Le macchine virtuali serie Ddv4 e Ddsv4, dotate di processori Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) in una configurazione con tecnologia Hyper-Threading, costituiscono una proposta di valore di livello più alto per i carichi di lavoro di uso più generale. Offre una velocità di clock di tutti i core di 3,4 GHz [, &reg; tecnologia Intel Turbo Boost 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Supportano anche il [ &reg; potenziamento di Intel Deep Learning](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Le nuove dimensioni di queste macchine virtuali prevedono una risorsa di archiviazione locale più grande del 50%, oltre a un numero di operazioni di I/O al secondo, sia in lettura che in scrittura, più alto rispetto alle dimensioni [Dv3/Dsv3](./dv3-dsv3-series.md) delle [macchine virtuali Gen2](./generation-2.md).

I casi d'uso per la serie D includono applicazioni di livello aziendale, database relazionali, memorizzazione nella cache in memoria e attività di analisi.

## <a name="ddv4-series"></a>Serie Ddv4

Le dimensioni serie Ddv4, dotate di processore Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake), offrono una combinazione di vCPU, memoria e disco temporaneo ideale per la maggior parte dei carichi di lavoro di produzione.

Le nuove dimensioni delle macchine virtuali Ddv4 prevedono una risorsa di archiviazione SSD locale veloce e più grande (fino a 2.400 GiB) e sono progettate per applicazioni in grado di sfruttare una bassa latenza e una risorsa di archiviazione locale ad alta velocità, come le applicazioni che richiedono un'alta velocità di lettura/scrittura per l'archiviazione temporanea o per le quali è necessaria l'archiviazione temporanea per cache o file temporanei. Alle macchine virtuali Ddv4 è possibile collegare risorse di archiviazione SSD Standard e HDD Standard. L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali.

[ACU](acu.md): 195-210<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
<br> 

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | <sup>**</sup> Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>**</sup> Questi valori di IOPs possono essere garantiti usando [macchine virtuali Gen2](generation-2.md)

## <a name="ddsv4-series"></a>Serie Ddsv4

Le macchine virtuali serie Ddsv4, dotate di processore Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake), offrono una combinazione di vCPU, memoria e disco temporaneo ideale per la maggior parte dei carichi di lavoro di produzione.

Le nuove dimensioni delle macchine virtuali Ddsv4 prevedono una risorsa di archiviazione SSD locale veloce e più grande (fino a 2.400 GiB) e sono progettate per applicazioni in grado di sfruttare una bassa latenza e una risorsa di archiviazione locale ad alta velocità, come le applicazioni che richiedono un'alta velocità di lettura/scrittura per l'archiviazione temporanea o per le quali è necessaria l'archiviazione temporanea per cache o file temporanei. 

 > [!NOTE]
 >I prezzi e i contatori di fatturazione per le dimensioni Ddsv4 sono uguali a quelli della serie Ddv4.

[ACU](acu.md): 195-210<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
<br> 

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | <sup>**</sup> Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>**</sup> Questi valori di IOPs possono essere garantiti usando [macchine virtuali Gen2](generation-2.md)

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
