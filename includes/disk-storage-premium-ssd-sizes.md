---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76837562"
---
| Dimensioni SSD Premium | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Dimensioni disco in GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1\.024 | 2\.048 | 4\.096 | 8,192 | 16.384 | 32.767 |
| IOPS per disco | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5\.000 | 7\.500 | 7\.500 | 16.000 | 18.000 | 20.000 |
| Velocità effettiva per disco | 25 MiB/sec | 25 MiB/sec | 25 MiB/sec | 25 MiB/sec | 50 MiB/sec | 100 MiB/sec | 125 MiB/sec | 150 MiB/sec | 200 MiB/sec | 250 MiB/sec | 250 MiB/sec| 500 MiB/sec | 750 MiB/sec | 900 MiB/sec |
| Numero massimo di operazioni di I/O al secondo in modalità burst per disco** | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Velocità effettiva massima in modalità burst per disco** | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec |
| Durata massima in modalità burst** | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |
| Idoneo per la prenotazione | No  | No  | No  | No  | No  | No  | No  | No  | Sì, fino a un anno | Sì, fino a un anno | Sì, fino a un anno | Sì, fino a un anno | Sì, fino a un anno | Sì, fino a un anno |

\*Indica una dimensione del disco attualmente in anteprima. Per informazioni sulla disponibilità a livello di area, vedere [Nuove dimensioni dei dischi: gestiti e non gestiti](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).  
\*\*Indica una funzionalità attualmente in anteprima. Per altre informazioni, vedere [Bursting del disco](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability).
