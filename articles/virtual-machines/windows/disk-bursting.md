---
title: Bursting del disco gestito
description: Informazioni sul bursting del disco per i dischi di Azure e per le macchine virtuali di Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594445"
---
# <a name="disk-bursting"></a>Bursting del disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting a livello di macchina virtuale
Il supporto del bursting a livello di macchina virtuale è abilitato in tutte le aree del cloud pubblico su queste dimensioni supportate: 
- [Serie Lsv2](../lsv2-series.md)

Per impostazione predefinita, il bursting è abilitato per le macchine virtuali che lo supportano.

## <a name="disk-level-bursting"></a>Bursting a livello del disco
L'espansione è disponibile anche nelle unità [SSD Premium](disks-types.md#premium-ssd) per le dimensioni dei dischi P20 e più piccole in tutte le aree. Per impostazione predefinita, il bursting del disco è abilitato nelle nuove distribuzioni delle dimensioni dei dischi che lo supportano. Le dimensioni dei dischi esistenti, se supportano il bursting del disco, possono abilitarlo usando uno dei metodi seguenti: 
- **Riavviare la macchina virtuale** 
- **Scollegare e ricollegare il disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
