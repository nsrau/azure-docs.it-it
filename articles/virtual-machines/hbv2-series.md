---
title: Serie HBv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164798"
---
# <a name="hbv2-series"></a>Serie HBv2

Le macchine virtuali della serie HBv2 sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti e simulazione del serbatoio. Macchine virtuali HBv2 funzionalità 120 processori AMD EPYC 7742 core, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Ogni macchina virtuale HBv2 offre fino a 340 GB al secondo di larghezza di banda di memoria e fino a 4 teraflop di calcolo FP64.

Archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti con mantenimento della memoria: non supportato

| Dimensioni | vCPU | Processore | Memoria (GB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Archiviazione temporanea (GB) | Numero massimo di dischi dati | NIC Ethernet max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Tutti | 480 + 960 | 8 | 1 |


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