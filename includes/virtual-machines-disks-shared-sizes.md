---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81008343"
---
Per il momento, solo dischi Ultra e SSD Premium possono abilitare i dischi condivisi. Dimensioni del disco diverse possono avere un `maxShares` limite diverso, che non può essere superato quando si imposta il `maxShares` valore. Per le unità SSD Premium, le dimensioni dei dischi che supportano la condivisione dei dischi sono P15 e versioni successive.

Per ogni disco è possibile definire un `maxShares` valore che rappresenta il numero massimo di nodi che possono condividere simultaneamente il disco. Ad esempio, se si prevede di configurare un cluster di failover a 2 nodi, è necessario impostare `maxShares=2` . Il valore massimo è un limite superiore. I nodi possono partecipare o uscire dal cluster (montare o smontare il disco) finché il numero di nodi è inferiore al `maxShares` valore specificato.

> [!NOTE]
> Il `maxShares` valore può essere impostato o modificato solo quando il disco viene scollegato da tutti i nodi.

### <a name="premium-ssd-ranges"></a>Intervalli di SSD Premium

La tabella seguente illustra i valori massimi consentiti per `maxShares` dalle dimensioni dei dischi Premium:

|Dimensione disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Il valore non influisce sui limiti di IOPS e larghezza di banda per un disco `maxShares` . Ad esempio, il numero massimo di IOPS di un disco P15 è 1100 se maxShares = 1 o maxShares > 1.

### <a name="ultra-disk-ranges"></a>Intervalli di dischi ultra

Il `maxShares` valore minimo è 1, mentre il `maxShares` valore massimo è 5. Non sono previste restrizioni relative alle dimensioni per i dischi Ultra, le dimensioni ultra disk possono usare qualsiasi valore per `maxShares` , fino a includere il valore massimo.