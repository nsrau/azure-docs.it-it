---
title: Serie M - Macchine virtuali di AzureM-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali della serie M.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/05/2019
ms.author: lahugh
ms.openlocfilehash: e8952218675493710cbdd1f92661e9a9c077f34c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164050"
---
# <a name="m-series"></a>Serie M

La serie M offre un elevato numero di vCPU (fino a 128 vCPU) e una grande quantità di memoria (fino a 3,8 TiB). È ideale anche per database estremamente grandi o altre applicazioni che traggono vantaggio da elevati conteggi vCPU e grandi quantità di memoria. Le dimensioni della serie M sono basate sull'Intel® Xeon® CPU E7-8890 v3

Funzionalità di M-series VM Intel® tecnologia Hyper-Threading

ACU: 160-180

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

Acceleratore di scrittura: [supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva di archiviazione massima memorizzata nella cache e temporanea: IOPS/MBps (dimensione della cache in GiB)Max cached and temp storage throughput: IOPS/MBps (cache size in GiB) | Velocità effettiva massima disco non memorizzato nella cache: IOPS/MBpsMax uncached disk throughput: IOPS/MBps | NiCs max/Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| <sup>Standard_M8ms 2</sup>       | 8   | 218,75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437,5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1,2,3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> Più di 64 vCPU richiedono uno di questi sistemi desktop guest supportati: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 o Oracle Linux 7.3 con LIS 4.2.1.

<sup>2</sup> dimensioni del nucleo vincolate disponibili.

<sup>3</sup> L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

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
