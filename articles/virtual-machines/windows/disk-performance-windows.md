---
title: Prestazioni di macchine virtuali e dischi
description: Altre informazioni sul funzionamento delle macchine virtuali e dei dischi collegati in combinazione con le prestazioni
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016460"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestazioni di macchine virtuali e dischi
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Macchine virtuali non memorizzate nella cache rispetto ai limiti memorizzati nella cache
 Le macchine virtuali che sono abilitate per l'archiviazione Premium e la memorizzazione nella cache di archiviazione Premium hanno due limiti di larghezza di banda di archiviazione diversi. Si procederà con la ricerca della macchina virtuale Standard_D8s_v3 come esempio. Di seguito è illustrata la documentazione relativa alla [serie Dsv3](../dv3-dsv3-series.md) e al Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Eseguiamo un test di benchmarking sulla macchina virtuale e sulla combinazione di dischi che creerà l'attività di i/o e potrai scoprire tutte le informazioni su come eseguire il benchmarking di i/o di archiviazione in Azure [qui](disks-benchmarks.md). Dallo strumento di benchmarking è possibile vedere che la combinazione di VM e dischi è in grado di raggiungere 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]