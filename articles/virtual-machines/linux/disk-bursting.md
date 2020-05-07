---
title: Espansione disco gestito
description: Informazioni sul picco del disco per i dischi di Azure e il picchi del disco per le macchine virtuali di Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594747"
---
# <a name="disk-bursting"></a>Bursting del disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Espansione a livello di macchina virtuale
Il supporto per l'espansione a livello di macchina virtuale è abilitato in tutte le aree nel cloud pubblico su queste dimensioni supportate: 
- [Serie Lsv2](../lsv2-series.md)

Per impostazione predefinita, l'espansione è abilitata per le macchine virtuali che lo supportano.

## <a name="disk-level-bursting"></a>Espansione a livello di disco
L'espansione è disponibile anche nelle unità [SSD Premium](disks-types.md#premium-ssd) per le dimensioni dei dischi P20 e più piccole in tutte le aree. Per impostazione predefinita, l'espansione del disco è abilitata nelle nuove distribuzioni delle dimensioni dei dischi che la supportano. Le dimensioni dei dischi esistenti, se supportano la modalità di espansione del disco, possono consentire l'espansione tramite uno dei metodi seguenti: 
- **Riavviare la macchina virtuale** 
- **Scollegare e riconnettere il disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
