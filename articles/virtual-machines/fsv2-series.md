---
title: Serie Fsv2-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164849"
---
# <a name="fsv2-series"></a>Serie Fsv2

La serie Fsv2 è basata sul processore Intel® Xeon® Platinum 8168. Include una velocità di clock di base di livello principale di 3,4 GHz e una frequenza Turbo Single Core massima di 3,7 GHz. Le istruzioni Intel® AVX-512 sono nuove per i processori Intel scalabili. Queste istruzioni forniscono fino a un incremento delle prestazioni di 2X ai carichi di lavoro di elaborazione vettoriali nelle operazioni a virgola mobile a precisione singola e doppia. In altre parole, sono molto veloci per qualsiasi carico di lavoro di calcolo.

Le macchine virtuali serie Fsv2 includono tecnologia Intel® Hyper-Threading.

ACU: 195 - 210

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Live Migration: supportato

Aggiornamenti con mantenimento della memoria: supportati

| Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Numero massimo di velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> l'uso di più di 64 vCPU richiede uno di questi sistemi operativi guest supportati:

- Windows Server 2016 o versione successiva
- Ubuntu 16,04 LTS o versioni successive, con kernel ottimizzato per Azure (kernel 4,15 o versione successiva)
- SLES 12 SP2 o versione successiva
- RHEL o CentOS dalla versione 6,7 alla 6,10, con il pacchetto LIS fornito da Microsoft (o versioni successive) installato
- RHEL o CentOS versione 7,3, con il pacchetto LIS fornito da Microsoft (o versioni successive) installato
- RHEL o CentOS versione 7,6 o successiva
- Oracle Linux con UEK4 o versione successiva
- Debian 9 con il kernel di backports, Debian 10 o versione successiva
- CoreOS con un kernel 4,14 o versione successiva

<sup>2</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

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
