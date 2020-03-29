---
title: Serie HBv2 - Macchine virtuali di AzureHBv2-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali della serie HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164798"
---
# <a name="hbv2-series"></a>Serie HBv2

Le macchine virtuali della serie HBv2 sono ottimizzate per le applicazioni guidate dalla larghezza di banda della memoria, ad esempio la fluidodinamica, l'analisi degli elementi finiti e la simulazione del serbatoio. Le macchine virtuali HBv2 dispongono di 120 core del processore AMD EPYC 7742, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Ogni macchina virtuale HBv2 fornisce fino a 340 GB/sec di larghezza di banda della memoria e fino a 4 teraFLOPS di calcolo FP64.

Archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

| Dimensione | vCPU | Processore | Memoria (GB) | Larghezza di banda della memoria GB/s | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza monocore (GHz, picco) | Prestazioni RDMA (Gb/s) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | Schede di interfaccia di rete Max Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Tutti | 480 x 960 | 8 | 1 |


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