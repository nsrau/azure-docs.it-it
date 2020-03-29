---
title: Dimensioni delle macchine virtuali di Azure - Archiviazione Documenti Microsoft
description: Elenca le diverse dimensioni ottimizzate di archiviazione disponibili per le macchine virtuali in Azure.Lists the different storage optimized sizes available for virtual machines in Azure. Elenca le informazioni sul numero di vCPU, dischi dati e schede di interfaccia di rete, nonché la velocità effettiva di archiviazione e la larghezza di banda di rete per le dimensioni in questa serie.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913337"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per l'archiviazione

Le dimensioni ottimizzate per l'archiviazione delle macchine virtuali offrono un'elevata velocità effettiva del disco e operazioni di I/O e sono ideali per Big Data, SQL, database NoSQL, data warehousing e database transazionali di grandi dimensioni.  Gli esempi includono Cassandra, MongoDB, Cloudera e Redis. In questo articolo vengono fornite informazioni sul numero di vCPU, dischi dati e schede di rete, nonché la velocità effettiva di archiviazione locale e la larghezza di banda di rete per ogni dimensione ottimizzata.

La [serie Lsv2](lsv2-series.md) offre un'elevata velocità effettiva, bassa latenza, uno storage NVMe locale mappato direttamente in esecuzione sul [processore AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) con una spinta di tutti i core di 2,55 GHz e una spinta massima di 3,0 GHz. Le macchine virtuali Serie Lsv2 sono disponibili in dimensioni comprese tra 8 e 80 vCPU in una configurazione multi-thread simultanea.  Sono disponibili 8 GiB di memoria per ogni vCPU e un dispositivo NVMe SSD M.2 da 1,92 TB per 8 vCPU, fino a 19,2 TB (10 x 1,92 TB) disponibili nella versione L80s v2.

## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.

Informazioni su come ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 per [Windows](windows/storage-performance.md) o [Linux.](linux/storage-performance.md)
