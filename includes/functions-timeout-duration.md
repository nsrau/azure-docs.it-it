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
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963579"
---
## <a name="timeout"></a>Durata timeout app per le funzioni 

La durata del timeout di un'app per le funzioni è definita dalla proprietà functionTimeout nel file di progetto [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . La tabella seguente mostra i valori predefiniti e massimi in minuti per entrambi i piani e in entrambe le versioni di runtime:

| Piano | Versione runtime | Predefinito | Massima |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Servizio app | 1.x | Senza limitazioni | Senza limitazioni |
| Servizio app | 2.x | 30 | Senza limitazioni |

> [!NOTE] 
> Indipendentemente dall'impostazione di timeout dell'app per le funzioni, 230 secondi è la quantità massima di tempo che una funzione attivata da HTTP può intraprendere per rispondere a una richiesta. Questo è dovuto al [timeout di inattività predefinito del Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Per tempi di elaborazione più lunghi, provare a usare il [modello asincrono Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) o [rinviare il lavoro effettivo e restituire una risposta immediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
