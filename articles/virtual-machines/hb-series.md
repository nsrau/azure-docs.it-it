---
title: Serie HB - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie HB.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164832"
---
# <a name="hb-series"></a>Serie HB

Le macchine virtuali della serie HB sono ottimizzate per le applicazioni guidate dalla larghezza di banda della memoria, ad esempio la fluidodinamica, l'analisi esplicita degli elementi finiti e la modellazione meteo. Le macchine virtuali HB sono dotate di 60 core del processore AMD EPYC 7551, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Una macchina virtuale HB fornisce fino a 260 GB/sec di larghezza di banda della memoria.

ACU: 199-216

Archiviazione Premium: supportata

Memorizzazione nella cache Archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda della memoria GB/s | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza monocore (GHz, picco) | Prestazioni RDMA (Gb/s) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Schede di interfaccia di rete Max Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tutti | 700 | 4 | 1 |

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