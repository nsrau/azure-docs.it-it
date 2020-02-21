---
title: Serie Eav4 e serie Easv4-macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Eav4 e Easv4.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 96cd7b4f4627e7c0335df9c745d8193a3dac7044
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493774"
---
# <a name="eav4-and-easv4-series"></a>Serie Eav4 e Easv4

Le serie Eav4 e Easv4 usano il processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro con ottimizzazione per la memoria. La serie Eav4 e la serie Easv4 hanno le stesse configurazioni di memoria e disco della serie EV3 & Esv3.

## <a name="eav4-series"></a>Serie Eav4

ACU: 230-260

Archiviazione Premium: non supportata

Caching archiviazione Premium: non supportato

Le dimensioni della serie Eav4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz e usare l'unità SSD Premium. Le dimensioni della serie Eav4 sono ideali per applicazioni aziendali a uso intensivo di memoria. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare l'unità SSD Premium, usare le dimensioni della serie Easv4. I prezzi e i contatori di fatturazione per le dimensioni Easv4 sono uguali a quelli della serie Eav3.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | NIC massimo/larghezza di banda di rete prevista (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| \_standard E2a\_V4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| \_standard E4a\_V4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| \_standard E8a\_V4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| \_standard E16a\_V4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| \_standard E20a\_V4|20|160|500|32|30000/468/234|8 / 10000 |
| \_standard E32a\_V4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| \_standard E48a\_V4 <sup>**</sup> |48|384|1200|32| | |
| \_standard E64a\_V4 <sup>**</sup> |64|512|1600|32| | |
| \_standard E96a\_V4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Queste dimensioni sono disponibili in anteprima. Se si è interessati a provare queste dimensioni più grandi, iscriversi all' [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="easv4-series"></a>Serie Easv4

ACU: 230-260

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Le dimensioni della serie Easv4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz e usare l'unità SSD Premium. Le dimensioni della serie Easv4 sono ideali per applicazioni aziendali a uso intensivo di memoria.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NIC massimo/larghezza di banda di rete prevista (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Queste dimensioni sono disponibili in anteprima. Se si è interessati a provare queste dimensioni più grandi, iscriversi all' [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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