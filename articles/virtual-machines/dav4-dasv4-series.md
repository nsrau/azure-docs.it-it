---
title: Serie Dav4 e Dasv4 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Dav4 e Dasv4.Specifications for the Dav4 and Dasv4-series VMs.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 809843bddc4216df4dab44330172d36778248b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162180"
---
# <a name="dav4-and-dasv4-series"></a>Serie Dav4 e Dasv4

La serie Dav4 e la serie Dasv4 sono nuove dimensioni che utilizzano il processore AMD 2.35Ghz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB che dedicate 8 GB di quella cache L3 a ogni 8 core aumentando le opzioni dei clienti per l'esecuzione dei loro scopi generali. La serie Dav4 e la serie Dasv4 hanno le stesse configurazioni di memoria e disco della serie D & Dsv3.

## <a name="dav4-series"></a>Serie Dav4

ACU: 230-260

Archiviazione Premium: Non supportata

Memorizzazione nella cache di archiviazione Premium: non supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

Le dimensioni della serie Dav4 sono basate sul processore 2.35Ghz AMD EPYC<sup>TM</sup> 7452 in grado di raggiungere una frequenza massima migliorata di 3,35 GHz. Le dimensioni della serie Dav4 offrono una combinazione di vCPU, memoria e archiviazione temporanea per la maggior parte dei carichi di lavoro di produzione. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per utilizzare sSD premium, utilizzare le dimensioni Dasv4. I prezzi e i contatori di fatturazione per le taglie Dasv4 sono gli stessi della serie Dav4.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | NiDi massima / Larghezza di banda di rete prevista (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4<sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4<sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4<sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup>Queste dimensioni sono in anteprima.  Se sei interessato a provare queste taglie più grandi, iscriviti a [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dasv4-series"></a>Serie Dasv4

ACU: 230-260

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

Le dimensioni della serie Dasv4 sono basate sul processore 2.35Ghz AMD EPYC<sup>TM</sup> 7452 che può raggiungere una frequenza massima migliorata di 3,35 GHz e utilizzare SSD premium. Le dimensioni della serie Dasv4 offrono una combinazione di vCPU, memoria e archiviazione temporanea per la maggior parte dei carichi di lavoro di produzione.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NiDi massima / Larghezza di banda di rete prevista (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_D48as_v4<sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4<sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4<sup>**</sup>|96|384|768|32| | | 

<sup>**</sup>Queste dimensioni sono in anteprima.  Se sei interessato a provare queste taglie più grandi, iscriviti a [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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
