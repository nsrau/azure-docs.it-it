---
title: Bursting del disco gestito
description: Informazioni sul picco del disco per i dischi di Azure e il picchi di dischi per le macchine virtuali di Azure in Linux.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 2e5fc8bde2c79a355fb7963c9101c4b040f97fa7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275158"
---
# <a name="managed-disk-bursting"></a>Bursting del disco gestito
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting a livello di macchina virtuale
Il supporto del bursting a livello di macchina virtuale è abilitato in tutte le aree del cloud pubblico su queste dimensioni supportate: 
- [Serie Lsv2](../lsv2-series.md)

L'espansione a livello di macchina virtuale è disponibile anche negli Stati Uniti centro-occidentali per le dimensioni supportate seguenti:
- [Serie Dsv3](../dv3-dsv3-series.md)
- [Serie Esv3](../ev3-esv3-series.md)

Per impostazione predefinita, il bursting è abilitato per le macchine virtuali che lo supportano.

## <a name="disk-level-bursting"></a>Bursting a livello del disco
Il bursting è disponibile anche nelle unità [SSD Premium](disks-types.md#premium-ssd) per le dimensioni dei dischi P20 e più piccole in tutte le aree dei cloud pubblico, per enti pubblici e per la Cina di Azure. Per impostazione predefinita, il bursting del disco è abilitato nelle distribuzioni nuove ed esistenti delle dimensioni dei dischi che lo supportano. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
