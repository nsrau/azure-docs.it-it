---
title: Bursting del disco gestito
description: Informazioni sul bursting del disco per i dischi di Azure e per le macchine virtuali di Azure
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889098"
---
# <a name="disk-bursting"></a>Bursting del disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting a livello di macchina virtuale
Il supporto del bursting a livello di macchina virtuale è abilitato in tutte le aree del cloud pubblico su queste dimensioni supportate: 
- [Serie Lsv2](../lsv2-series.md)

L'espansione a livello di macchina virtuale è disponibile anche negli Stati Uniti centro-occidentali per le dimensioni supportate seguenti:
- [Serie Dsv3](../dv3-dsv3-series.md)
- [Serie Esv3](../ev3-esv3-series.md)

Per impostazione predefinita, il bursting è abilitato per le macchine virtuali che lo supportano.

## <a name="disk-level-bursting"></a>Bursting a livello del disco
L'espansione è disponibile anche nelle unità [SSD Premium](disks-types.md#premium-ssd) per le dimensioni dei dischi P20 e più piccole in tutte le aree. Per impostazione predefinita, il bursting del disco è abilitato nelle nuove distribuzioni delle dimensioni dei dischi che lo supportano. Le dimensioni dei dischi esistenti, se supportano il bursting del disco, possono abilitarlo usando uno dei metodi seguenti: 
- **Riavviare la macchina virtuale** 
- **Scollegare e ricollegare il disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
