---
title: Serie M-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie M.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 3e0552570d5bdb7f812852cd058710b833b7fdd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80521332"
---
# <a name="m-series"></a>Serie M

La serie M offre un numero elevato di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TiB). È ideale anche per database di dimensioni molto grandi o altre applicazioni che traggono vantaggio da conteggi vCPU elevati e grandi quantità di memoria. Le dimensioni della serie M sono supportate sia sulla CPU&reg; Intel&reg; Xeon E7-8890 V3 a 2.50 GHz che su Intel&reg; Xeon&reg; Platinum 8280M 2.7 GHz (Cascade Lake).

Funzionalità della VM della serie M,&reg; tecnologia Intel Hyper-Threading.

ACU: 160-180

Archiviazione Premium: supportata

Caching archiviazione Premium: supportato

Live Migration: non supportato

Aggiornamenti con mantenimento della memoria: non supportato

Acceleratore di scrittura: [supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Dimensioni | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Numero massimo di velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NIC massimo/larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218,75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437,5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1, 2, 3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> più di 64 vCPU richiedono uno di questi sistemi operativi guest supportati: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7,3 o Oracle Linux 7,3 con LIS 4.2.1.

<sup>2</sup> dimensioni core vincolate disponibili.

<sup>3</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

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
