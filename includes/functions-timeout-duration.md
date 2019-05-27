---
title: File di inclusione
description: File di inclusione
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131361"
---
## <a name="timeout"></a>Durata del timeout di app (funzione) 

La durata del timeout di un'app per le funzioni viene definita dalla proprietà functionTimeout nel [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) file di progetto. Nella tabella seguente mostra i valori massimi e predefiniti in minuti per entrambi i piani e in entrambe le versioni runtime:

| Pianificazione | Versione runtime | Predefinito | Massima |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Servizio app | 1.x | Senza limitazioni | Senza limitazioni |
| Servizio app | 2.x | 30 | Senza limitazioni |

> [!NOTE] 
> Indipendentemente dall'impostazione di timeout dell'app di funzione, 230 secondi è la quantità massima di tempo che può eseguire una funzione attivata tramite HTTP per rispondere a una richiesta. Ciò avviene perché il [predefinito di timeout di inattività del servizio Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Per tempi di elaborazione più lunghi, è consigliabile usare la [modello asincrono di funzioni permanenti](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) oppure [rinviare l'effettiva operazione e restituire una risposta immediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
