---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/12/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8e9d04c7d374aabf3870a3260bc1f5d808b8ee9c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906319"
---
**Dischi gestiti HDD Standard**

| Tipo di disco Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60 *             | S70 *             | S80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB          | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8,192     | 16.384     | 32.767     |
| IOPS per disco       | Fino a 500              | Fino a 500              | Fino a 500              | Fino a 500 | Fino a 500              | Fino a 500              | Fino a 500             | Fino a 500              | Fino a 1.300              | Fino a 2.000              | Fino a 2.000              |
| Velocità effettiva per disco | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec| Fino a 300 MiB/sec | Fino a 500 MiB/sec | Fino a 500 MiB/sec |

**Dischi gestiti SSD Standard**

| Tipo di disco SSD Standard | E4                | E6                | E10               | E15               | E20             | E30              | E40              | E50              | E60 *             | E70 *             | E80 *             |
|------------------------|-------------------|-------------------|-------------------|-------------------|-----------------|------------------|------------------|------------------|-------------------|-------------------|-------------------|
| Dimensioni disco in GiB       | 32                | 64                | 128               | 256               | 512             | 1.024            | 2.048            | 4.095            | 8,192             | 16.384            | 32.767            |
| IOPS per disco          | Fino a 120         | Fino a 240         | Fino a 500         | Fino a 500         | Fino a 500       | Fino a 500        | Fino a 500        | Fino a 500        | Fino a 1.300       | Fino a 2.000       | Fino a 2.000       |
| Velocità effettiva per disco    | Fino a 25 MB/sec   | Fino a 50 MB/sec   | Fino a 60 MB/sec   | Fino a 60 MB/sec   | Fino a 60 MB/sec | Fino a 60 MB/sec  | Fino a 60 MB/sec  | Fino a 60 MB/sec  | Fino a 300 MiB/sec | Fino a 500 MiB/sec | Fino a 500 MiB/sec |

**Dischi gestiti SSD Premium: limiti per disco**

| Tipo di disco Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60 *             | P70 *             | P80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB           | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8,192     | 16.384     | 32.767     |
| IOPS per disco       | Fino a 120 | Fino a 240              | Fino a 500              | Fino a 1.100 | Fino a 2.300              | Fino a 5.000              | Fino a 7.500             | Fino a 7.500              | Fino a 12.500              | Fino a 15.000              | Fino a 20.000              |
| Velocità effettiva per disco | Fino a 25 MiB/sec | Fino a 50 MiB/sec | Fino a 100 MiB/sec | Fino a 125 MiB/sec | Fino a 150 MiB/sec | Fino a 200 MiB/sec | Fino a 250 MiB/sec | Fino a 250 MiB/sec| Fino a 480 MiB/sec | Fino a 750 MiB/sec | Fino a 750 MiB/sec |

**Dischi gestiti SSD Premium: limiti per VM**

| Risorsa | Limite predefinito |
| --- | --- |
| Max operazioni IOPS per VM |80.000 IOPS con VM GS5 |
| Max velocità effettiva per VM |2.000 MB/s con VM GS5 |
