---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8bb607d0b3091de34c5c75b09c3b99257f6b2c2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467748"
---
| Dimensioni SSD Premium | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Dimensioni disco in GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1\.024 | 2\.048 | 4\.096 | 8,192 | 16.384 | 32.767 |
| IOPS per disco | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5\.000 | 7\.500 | 7\.500 | 16.000 | 18.000 | 20.000 |
| Velocità effettiva per disco | 25 MiB/sec | 25 MiB/sec | 25 MiB/sec |Fino a 25 MiB/sec | 50 MiB/sec | 100 MiB/sec | 125 MiB/sec | 150 MiB/sec | 200 MiB/sec | 250 MiB/sec | 250 MiB/sec| 500 MiB/sec | 750 MiB/sec | 900 MiB/sec |
| Numero massimo di operazioni di I/O al secondo in modalità burst per disco** | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Velocità effettiva massima in modalità burst per disco** | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec | 170 MiB/sec |
| Durata massima in modalità burst** | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*Indica una dimensione del disco attualmente in anteprima. Per informazioni sulla disponibilità a livello di area, vedere [dischi Premium: gestiti e non gestiti](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#premium-disks-managed-and-unmanaged).

\**Indica una funzionalità attualmente in anteprima. Per ulteriori informazioni, vedere [Bursting del disco](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability).
