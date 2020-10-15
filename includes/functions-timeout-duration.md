---
title: includere file
description: includere file
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198327"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Durata del timeout di un'app per le funzioni 

La durata del timeout di un'app per le funzioni è definita dalla proprietà `functionTimeout` nel file di progetto [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout). La tabella seguente mostra i valori predefiniti e quelli massimi in minuti per i piani e per le diverse versioni di runtime:

| Piano | Versione di runtime | Predefinito | Massimo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Consumo | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Nessuna limitazione |
| Premium | 2.x | 30 | Nessuna limitazione |
| Premium | 3.x | 30 | Nessuna limitazione |
| Servizio app | 1.x | Nessuna limitazione | Nessuna limitazione |
| Servizio app | 2.x | 30 | Nessuna limitazione |
| Servizio app | 3.x | 30 | Nessuna limitazione |

> [!NOTE] 
> Indipendentemente dall'impostazione di timeout dell'app per le funzioni, 230 secondi è la quantità di tempo massima che una funzione attivata da HTTP può impiegare per rispondere a una richiesta. Il motivo è legato al [timeout di inattività predefinito di Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Per tempi di elaborazione più lunghi, provare a usare lo [schema asincrono di Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) oppure [rinviare il processo effettivo e restituire una risposta immediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
