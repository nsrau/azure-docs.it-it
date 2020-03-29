---
title: Serie Fsv2 - Macchine virtuali di AzureFsv2-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali della serie Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164849"
---
# <a name="fsv2-series"></a>Serie Fsv2

La serie Fsv2 è basata sul processore Intel® Xeon® Platinum 8168. È dotato di una velocità di clock Turbo a tutto il core di 3,4 GHz e una frequenza massima di turbo single-core di 3,7 GHz. Le istruzioni Intel® AVX-512 sono una novità sui processori scalabili Intel. Queste istruzioni forniscono fino a un incremento delle prestazioni 2X ai carichi di lavoro di elaborazione vettoriale su operazioni a virgola mobile a precisione singola e doppia. In altre parole, sono molto veloci per qualsiasi carico di lavoro computazionale.

Le macchine virtuali serie Fsv2 dispongono di funzionalità Intel® tecnologia Hyper-Threading.

ACU: 195 - 210

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: supportato

Aggiornamenti di conservazione della memoria: supportati

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva di archiviazione massima memorizzata nella cache e temporanea: IOPS/MBps (dimensione della cache in GiB)Max cached and temp storage throughput: IOPS/MBps (cache size in GiB) | Velocità effettiva massima disco non memorizzato nella cache: IOPS/MBpsMax uncached disk throughput: IOPS/MBps | NiCs max/Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> L'uso di più di 64 vCPU richiede uno di questi sistemi operativi guest supportati:

- Windows Server 2016 o versione successiva
- Ubuntu 16.04 LTS o versione successiva, con kernel tuned Azure (4.15 kernel o versione successiva)Ubuntu 16.04 LTS or later, with Azure tuned kernel (4.15 kernel or later)
- SLES 12 SP2 o versione successiva
- RHEL o CentOS versione 6.7 a 6.10, con il pacchetto LIS fornito da Microsoft 4.3.1 (o versione successiva) installato
- RHEL o CentOS versione 7.3, con il pacchetto LIS 4.2.1 (o versione successiva) fornito da Microsoft installato
- RHEL o CentOS versione 7.6 o successiva
- Oracle Linux con UEK4 o versioni successive
- Debian 9 con kernel backports, Debian 10 o versioni successive
- CoreOS con un kernel 4.14 o versione successiva

<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

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
