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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471697"
---
Per il momento, solo le unità SSD Premium possono abilitare i dischi condivisi. Le dimensioni del disco che supportano questa funzionalità sono P15 e versioni successive. Dimensioni del disco diverse possono avere un limite di `maxShares` diverso, che non può essere superato quando si imposta il valore di `maxShares`.

Per ogni disco è possibile definire un valore `maxShares` che rappresenta il numero massimo di nodi che possono condividere simultaneamente il disco. Se ad esempio si prevede di configurare un cluster di failover a 2 nodi, impostare `maxShares=2`. Il valore massimo è un limite superiore. I nodi possono partecipare o uscire dal cluster (montare o smontare il disco) finché il numero di nodi è inferiore al valore di `maxShares` specificato.

> [!NOTE]
> Il valore di `maxShares` può essere impostato o modificato solo quando il disco viene scollegato da tutti i nodi.

Nella tabella seguente sono illustrati i valori massimi consentiti per `maxShares` per dimensione del disco:

|Dimensione disco  |limite maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

I limiti di IOPS e larghezza di banda per un disco non sono interessati dal valore `maxShares`. Ad esempio, il numero massimo di IOPS di un disco P15 è 1100 se maxShares = 1 o maxShares > 1.