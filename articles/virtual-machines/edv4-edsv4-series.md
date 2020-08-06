---
title: Serie Edv4 e Edsv4
description: Specifiche per le macchine virtuali serie Ev4, Edv4, Esv4 ed Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 0fa4fb1fa55d5d3fa61c923bc024e6536b43c87a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829187"
---
# <a name="edv4-and-edsv4-series"></a>Serie Edv4 e Edsv4

Le macchine virtuali serie Edv4 ed Edsv4, dotate di processori Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) in una configurazione con tecnologia Hyper-Threading, sono ideali per varie applicazioni aziendali con utilizzo intensivo della memoria, disponendo di un massimo di 504 GiB di RAM e delle tecnologie [Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Supportano anche il [ &reg; potenziamento di Intel Deep Learning](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Le nuove dimensioni di queste macchine virtuali prevedono una risorsa di archiviazione locale più grande del 50%, oltre a un numero di operazioni di I/O al secondo, sia in lettura che in scrittura, più alto rispetto alle dimensioni [Ev3/Esv3](./ev3-esv3-series.md) delle [macchine virtuali Gen2](./linux/generation-2.md)

## <a name="edv4-series"></a>Serie Edv4

Le dimensioni serie Edv4, dotate di processori Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake), offrono fino a 504 GiB di RAM, oltre a una risorsa di archiviazione SSD locale veloce e di grandi dimensioni (fino a 2.400 GiB). Queste macchine virtuali sono ideali per applicazioni aziendali con utilizzo intensivo della memoria e applicazioni in grado di sfruttare una bassa latenza e una risorsa di archiviazione locale ad alta velocità. Alle macchine virtuali Edv4 è possibile collegare risorse di archiviazione disco SSD Standard e HDD Standard. 

ACU: 195 - 210

Archiviazione Premium:  Non supportato

Memorizzazione nella cache Archiviazione Premium:  Non supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


## <a name="edsv4-series"></a>Serie Edsv4

Le dimensioni serie Edsv4, dotate di processori Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake), offrono fino a 504 GiB di RAM, oltre a una risorsa di archiviazione SSD locale veloce e di grandi dimensioni (fino a 2.400 GiB). Queste macchine virtuali sono ideali per applicazioni aziendali con utilizzo intensivo della memoria e applicazioni in grado di sfruttare una bassa latenza e una risorsa di archiviazione locale ad alta velocità.

ACU: 195-210

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [Disponibili dimensioni core vincolate)](./windows/constrained-vcpu.md).


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