---
title: Serie DC-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie DC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8f36220ab81c0fd9533bf2f025b801b37823da5f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493813"
---
# <a name="dc-series"></a>Serie DC

La [serie DC](#dc-series) è una famiglia di macchine virtuali in Azure che consente di proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Questi computer sono supportati dal processore Intel XEON E-2176G a 3,7 GHz con la tecnologia SGX. Con la tecnologia Intel Turbo Boost, queste macchine possono arrivare fino a 4,7 GHz. Le istanze della serie DC consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DC2s | 2 | 8  | 100 | 2 | 4000 / 32 (43) | 3200 /48 | 2 / 1500 |
| Standard_DC4s | 4 | 16 | 200 | 4 | 8000 / 64 (86) | 6400 /96 | 2 / 3000 |

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