---
title: Prestazioni di macchine virtuali e dischi
description: Altre informazioni sul funzionamento delle macchine virtuali e dei dischi collegati in combinazione con le prestazioni
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518069"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestazioni di macchine virtuali e dischi
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Macchine virtuali non memorizzate nella cache rispetto ai limiti memorizzati nella cache
Le macchine virtuali abilitate per la memorizzazione nella cache di archiviazione Premium e archiviazione Premium hanno due limiti di larghezza di banda di archiviazione diversi. Si osservi Standard_D8s_v3 macchina virtuale come esempio. Di seguito è illustrata la documentazione relativa alla [serie Dsv3](../dv3-dsv3-series.md) e ai Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Eseguire un test di benchmarking sulla macchina virtuale e sulla combinazione di dischi che crea l'attività di i/o. Per informazioni su come eseguire il benchmarking di i/o di archiviazione in Azure, vedere [benchmark your application on archiviazione su disco di Azure](disks-benchmarks.md). Dallo strumento di benchmarking è possibile vedere che la combinazione di VM e dischi può raggiungere 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
