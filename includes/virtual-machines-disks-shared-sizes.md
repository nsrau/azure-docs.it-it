---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008343"
---
Per ora, solo i dischi ultra e gli SSD premium possono abilitare i dischi condivisi. Dimensioni del disco diverse `maxShares` possono avere un limite `maxShares` diverso, che non è possibile superare quando si imposta il valore. Per gli sSD premium, le dimensioni dei dischi che supportano la condivisione dei dischi sono P15 e superiori.

Per ogni disco, è `maxShares` possibile definire un valore che rappresenta il numero massimo di nodi che possono condividere contemporaneamente il disco. Ad esempio, se si prevede di configurare un cluster `maxShares=2`di failover a due nodi, è necessario impostare . Il valore massimo è un limite superiore. I nodi possono unire o lasciare il cluster (montare o smontare `maxShares` il disco) purché il numero di nodi sia inferiore al valore specificato.

> [!NOTE]
> Il `maxShares` valore può essere impostato o modificato solo quando il disco viene scollegato da tutti i nodi.

### <a name="premium-ssd-ranges"></a>Gamma SSD Premium

La tabella seguente illustra i `maxShares` valori massimi consentiti per le dimensioni premium del disco:

|Dimensione disco  |Limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

I limiti di iOPS e larghezza di `maxShares` banda per un disco non sono interessati dal valore. Ad esempio, il numero massimo di operazioni di I/O al secondo di un disco P15 è 1100 se maxShares è 1 o maxShares > 1.

### <a name="ultra-disk-ranges"></a>Intervalli di dischi ultra

Il `maxShares` valore minimo è 1, mentre il valore massimo `maxShares` è 5. Non ci sono restrizioni di dimensione sui dischi ultra, `maxShares`qualsiasi dimensione ultra disco può utilizzare qualsiasi valore per , fino a , compreso il valore massimo.