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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198327"
---
## <a name="timeout"></a>Durata timeout app per le funzioni 

La durata del timeout di un'app per le funzioni è definita dalla proprietà `functionTimeout` nel file di progetto [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . La tabella seguente mostra i valori predefiniti e massimi in minuti per entrambi i piani e per le diverse versioni di runtime:

| Piano | Versione runtime | Predefinito | Massimo |
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
> Indipendentemente dall'impostazione di timeout dell'app per le funzioni, 230 secondi è la quantità massima di tempo che una funzione attivata da HTTP può intraprendere per rispondere a una richiesta. Questo è dovuto al [timeout di inattività predefinito del Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Per tempi di elaborazione più lunghi, provare a usare il [modello asincrono Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) o [rinviare il lavoro effettivo e restituire una risposta immediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
