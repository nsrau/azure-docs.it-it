---
title: Serie H-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/04/2020
ms.author: lahugh
ms.openlocfilehash: 7b739e8a282fdd462ee6b465371bc04a9172ab46
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493072"
---
# <a name="h-series"></a>Serie H

Le macchine virtuali serie H sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o per i requisiti di memoria di grandi dimensioni. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, fino a 14 GB di RAM per core CPU e senza hyperthreading. Le funzionalità della serie H 56 GB/sec Mellanox FDR InfiniBand in una configurazione di albero FAT non bloccante per prestazioni RDMA coerenti. Le macchine virtuali serie H supportano Intel MPI 5. x e MS-MPI.

ACU: 290-300

Archiviazione Premium: non supportata

Memorizzazione nella cache Archiviazione Premium: non supportata

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | NIC Ethernet max |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 V3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 V3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> per le applicazioni MPI, la rete back-end RDMA dedicata viene abilitata dalla rete InfiniBand FDR.

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