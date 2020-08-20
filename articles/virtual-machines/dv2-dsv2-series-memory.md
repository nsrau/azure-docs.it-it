---
title: VM con ottimizzazione per la memoria dv2 e serie DSv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie dv2 e DSv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e2c1b9bb8d8fa1e7b82bebfa31963be19d9024af
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648497"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Dv2 con ottimizzazione per la memoria e serie Dsv2

La serie dv2 e Dsv2, una versione successiva alla serie D originale, offre una CPU più potente. Le dimensioni della serie DSv2 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell). La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

## <a name="dv2-series-11-15"></a>Dv2-series 11-15

Le dimensioni della serie dv2 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

ACU: 210 - 250

Archiviazione Premium: non supportata

Caching archiviazione Premium: non supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Numero massimo di dischi dati/velocità effettiva: IOPS | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32X500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.
<sup>2</sup> 25000 Mbps con rete accelerata.

## <a name="dsv2-series-11-15"></a>DSv2-series 11-15

Le dimensioni della serie DSv2 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o i processori Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

ACU: 210 - 250 <sup>1</sup>

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Supportato

Manutenzione con mantenimento della memoria: Supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> La massima velocità effettiva del disco (IOPS o MBps) possibile con una VM serie DSv2 può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati.  Per maggiori dettagli, vedere [Progettazione per prestazioni elevate](./windows/premium-storage-performance.md).
<sup>2</sup>  l'istanza è isolata per l'hardware basato su Intel Haswell e dedicata a un singolo cliente.  
<sup>3</sup> Disponibili dimensioni core vincolate.  
<sup>4</sup> 25000 Mbps con rete accelerata.

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