---
title: Serie Dv2 e Dsv2 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Dv2 e Dsv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164424"
---
# <a name="dv2-and-dsv2-series"></a>Serie Dv2 e DSv2

Le serie Dv2 e Dsv2, un follow-on della serie D originale, presentano una CPU più potente e una configurazione ottimale da CPU a memoria che li rende adatti per la maggior parte dei carichi di lavoro di produzione. La serie Dv2 è circa 35% più veloce rispetto alla serie D. La serie Dv2 viene eseguita sui processori Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 a 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) con Intel Turbo Boost Technology 2.0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

## <a name="dv2-series"></a>Serie Dv2

Le dimensioni della serie Dv2 vengono eseguite su Intel® Xeon® 8171M 2.1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) o sui processori Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) con Intel Turbo Boost Technology 2.0.

ACU: 210-250

Archiviazione Premium: non supportata

Memorizzazione nella cache di archiviazione Premium: non supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva di archiviazione temporanea massima: IOPS/Read MBps/Write MBpsMax temp storage throughput: IOPS/Read MBps/Write MBps | Numero massimo di dischi dati | Velocità effettiva: operazioni di I/O al secondo | NiCs max/Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>Serie DSv2

Le dimensioni della serie DSv2 vengono eseguite sui processori® Skylake (Skylake® 8171M 2,1 GHz (Skylake) o Intel® Xeon® E5-2673 v4 2,3 GHz®® (Broadwell) e utilizzano storage premium.

ACU: 210-250

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva di archiviazione massima memorizzata nella cache e temporanea: IOPS/MBps (dimensione della cache in GiB)Max cached and temp storage throughput: IOPS/MBps (cache size in GiB) | Velocità effettiva massima disco non memorizzato nella cache: IOPS/MBpsMax uncached disk throughput: IOPS/MBps | NiCs max/Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
