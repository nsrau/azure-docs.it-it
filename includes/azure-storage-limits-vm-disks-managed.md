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
ms.openlocfilehash: 3ad66d235e00d9866794ca90be9934e174bf4102
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553685"
---
**Unità disco rigido standard di managed disks**

| Tipo di disco standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*             | S70*             | S80*             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB          | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8,192     | 16.384     | 32.767     |
| IOPS per disco       | Fino a 500              | Fino a 500              | Fino a 500              | Fino a 500 | Fino a 500              | Fino a 500              | Fino a 500             | Fino a 500              | Fino a 1.300              | Fino a 2.000              | Fino a 2.000              |
| Velocità effettiva per disco | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec| Fino a 300 MiB/sec | Fino a 500 MiB/sec | Fino a 500 MiB/sec |

**SSD standard di managed disks**

| Tipo di disco SSD standard | E4                | E6                | E10               | E15               | E20             | E30              | E40              | E50              | E60*             | E70*             | E80*             |
|------------------------|-------------------|-------------------|-------------------|-------------------|-----------------|------------------|------------------|------------------|-------------------|-------------------|-------------------|
| Dimensioni disco in GiB       | 32                | 64                | 128               | 256               | 512             | 1.024            | 2.048            | 4.095            | 8,192             | 16.384            | 32.767            |
| IOPS per disco          | Fino a 120         | Fino a 240         | Fino a 500         | Fino a 500         | Fino a 500       | Fino a 500        | Fino a 500        | Fino a 500        | Fino a 1.300       | Fino a 2.000       | Fino a 2.000       |
| Velocità effettiva per disco    | Fino a 25 MB/sec   | Fino a 50 MB/sec   | Fino a 60 MB/sec   | Fino a 60 MB/sec   | Fino a 60 MB/sec | Fino a 60 MB/sec  | Fino a 60 MB/sec  | Fino a 60 MB/sec  | Fino a 300 MiB/sec | Fino a 500 MiB/sec | Fino a 500 MiB/sec |

**Managed disks Premium SSD: Limiti per disco**

| Tipo di disco Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*             | P70*             | P80*             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco in GiB           | 32             | 64             | 128            | 256  | 512            | 1.024    | 2.048     | 4.095    | 8,192     | 16.384     | 32.767     |
| IOPS per disco       | Fino a 120 | Fino a 240              | Fino a 500              | Fino a 1.100 | Fino a 2.300              | Fino a 5.000              | Fino a 7.500             | Fino a 7.500              | Fino a 12.500              | Fino a 15.000              | Fino a 20.000              |
| Velocità effettiva per disco | Fino a 25 MiB/sec | Fino a 50 MiB/sec | Fino a 100 MiB/sec | Fino a 125 MiB/sec | Fino a 150 MiB/sec | Fino a 200 MiB/sec | Fino a 250 MiB/sec | Fino a 250 MiB/sec| Fino a 480 MiB/sec | Fino a 750 MiB/sec | Fino a 750 MiB/sec |

**Managed disks Premium SSD: Limiti per ogni VM**

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di IOPS per ogni macchina virtuale |80.000 IOPS con VM GS5 |
| Velocità effettiva massima per macchina virtuale |2.000 MB/s con VM GS5 |
