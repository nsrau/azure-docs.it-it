---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471697"
---
Per ora, solo gli SSD premium possono abilitare i dischi condivisi. Le dimensioni del disco che supportano questa funzionalità sono P15 e superiori. Dimensioni del disco diverse `maxShares` possono avere un limite `maxShares` diverso, che non è possibile superare quando si imposta il valore.

Per ogni disco, è `maxShares` possibile definire un valore che rappresenta il numero massimo di nodi che possono condividere contemporaneamente il disco. Ad esempio, se si prevede di configurare un cluster `maxShares=2`di failover a due nodi, è necessario impostare . Il valore massimo è un limite superiore. I nodi possono unire o lasciare il cluster (montare o smontare `maxShares` il disco) purché il numero di nodi sia inferiore al valore specificato.

> [!NOTE]
> Il `maxShares` valore può essere impostato o modificato solo quando il disco viene scollegato da tutti i nodi.

Nella tabella seguente vengono illustrati `maxShares` i valori massimi consentiti per dimensione del disco:

|Dimensione disco  |Limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

I limiti di iOPS e larghezza di `maxShares` banda per un disco non sono interessati dal valore. Ad esempio, le operazioni di I/O al secondo massime di un disco P15 sono 1100, indipendentemente dal fatto che maxShares sia uguale a 1 o maxShares > 1.