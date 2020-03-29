---
title: Serie Av2 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali di serie Av2.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: fe27a1cc78d1f37d535f364c03803a5196090a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163948"
---
# <a name="av2-series"></a>Serie Av2

Le macchine virtuali di serie Av2 possono essere distribuite su una vasta gamma di tipi di hardware e processori. Le macchine virtuali avventate di serie dispongono di configurazioni di memoria e prestazioni della CPU più adatte per carichi di lavoro entry level come sviluppo e test. La dimensione viene limitata per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui viene distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale. Alcuni casi d'uso di esempio includono server di sviluppo e test, server Web a basso traffico, database di piccole e medie dimensioni, proof-of-concepts e repository di codice.

ACU: 100

Archiviazione Premium: non supportata

Memorizzazione nella cache di archiviazione Premium: non supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva di archiviazione temporanea massima: IOPS/Read MBps/Write MBpsMax temp storage throughput: IOPS/Read MBps/Write MBps | Numero massimo di dischi dati/velocità effettiva: IOPSMax data disks/throughput: IOPS | NiCs max/Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

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
