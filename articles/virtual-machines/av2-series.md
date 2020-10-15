---
title: Serie Av2
description: Specifiche per le macchine virtuali della serie AV2.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 65aab240dd0be97aafa370883aa953f8eb766cf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650749"
---
# <a name="av2-series"></a>Serie Av2

Le macchine virtuali di serie Av2 possono essere distribuite su una vasta gamma di tipi di hardware e processori. Le macchine virtuali della serie AV2 hanno configurazioni di memoria e prestazioni della CPU più adatte per carichi di lavoro a livello di voce quali sviluppo e test. La dimensione è limitata per offrire prestazioni del processore coerenti per l'istanza in esecuzione, indipendentemente dall'hardware in cui è distribuita. Per determinare l'hardware fisico in cui viene distribuita questa dimensione, eseguire una query nell'hardware virtuale dall'interno della macchina virtuale. Alcuni casi d'uso di esempio includono server di sviluppo e test, server Web con traffico ridotto, database di piccole e medie dimensioni, modelli di prova e repository di codice.

[ACU](acu.md): 100<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata <br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato <br>
[Live Migration](maintenance-and-updates.md): supportato <br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati <br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 <br>
<br>

| Dimensione | vCore | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/Mbps di scrittura | Numero massimo di dischi dati/velocità effettiva: IOPS | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

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
