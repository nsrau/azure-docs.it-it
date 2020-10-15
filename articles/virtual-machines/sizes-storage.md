---
title: Dimensioni delle macchine virtuali di Azure-archiviazione | Microsoft Docs
description: Elenca le diverse dimensioni ottimizzate per l'archiviazione disponibili per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
ms.subservice: sizes
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5af7b3373993dce1939ecd7534140e58db688579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835579"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per l'archiviazione

Le dimensioni delle macchine virtuali ottimizzate per l'archiviazione offrono velocità effettiva elevata del disco e i/o e sono ideali per Big data, database SQL, NoSQL, data warehousing e database transazionali di grandi dimensioni.  Gli esempi sono Cassandra, MongoDB, Cloudera e Redis. Questo articolo fornisce informazioni sul numero di vCPU, dischi dati e NIC, nonché sulla velocità effettiva di archiviazione locale e sulla larghezza di banda di rete per ogni dimensione ottimizzata.

La [serie Lsv2](lsv2-series.md) offre una velocità effettiva elevata, bassa latenza, archiviazione NVMe locale con mapping diretto in esecuzione sul [processore AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) con un Boost di tutti i core di 2.55 GHz e un aumento massimo di 3,0 GHz. Le macchine virtuali Serie Lsv2 sono disponibili in dimensioni comprese tra 8 e 80 vCPU in una configurazione multi-thread simultanea.  Sono disponibili 8 GiB di memoria per ogni vCPU e un dispositivo NVMe SSD M.2 da 1,92 TB per 8 vCPU, fino a 19,2 TB (10 x 1,92 TB) disponibili nella versione L80s v2.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

Informazioni su come ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 per [Windows](windows/storage-performance.md) o [Linux](linux/storage-performance.md).

Per altre informazioni sul modo in cui Azure denomina le proprie macchine virtuali, vedere [convenzioni di denominazione di macchine virtuali di Azure](./vm-naming-conventions.md).