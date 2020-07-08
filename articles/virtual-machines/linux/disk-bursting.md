---
title: Bursting del disco gestito
description: Informazioni sul bursting del disco per i dischi di Azure e per le macchine virtuali di Azure
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: f92fae38d49c51dfe87a68b023ba779e89b0e0bc
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295457"
---
# <a name="disk-bursting"></a>Bursting del disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting a livello di macchina virtuale
Il supporto del bursting a livello di macchina virtuale è abilitato in tutte le aree del cloud pubblico su queste dimensioni supportate: 
- [Serie Lsv2](../lsv2-series.md)

Per impostazione predefinita, il bursting è abilitato per le macchine virtuali che lo supportano.

## <a name="disk-level-bursting"></a>Bursting a livello del disco
Il bursting è disponibile anche nelle unità [SSD Premium](disks-types.md#premium-ssd) per le dimensioni dei dischi P20 e più piccole in tutte le aree dei cloud pubblico, per enti pubblici e per la Cina di Azure. Per impostazione predefinita, il bursting del disco è abilitato nelle distribuzioni nuove ed esistenti delle dimensioni dei dischi che lo supportano. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
