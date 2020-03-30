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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198327"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Durata del timeout dell'app per le funzioni 

La durata di timeout di un'app per le `functionTimeout` funzioni è definita dalla proprietà nel file di progetto [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) La tabella seguente mostra i valori predefiniti e massimi in minuti sia per i piani che per le diverse versioni di runtime:

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
> Indipendentemente dall'impostazione di timeout dell'app per le funzioni, 230 secondi è la quantità massima di tempo che una funzione attivata HTTP può richiedere per rispondere a una richiesta. Ciò è dovuto al [timeout di inattività predefinito di Azure Load Balancer.](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) Per tempi di elaborazione più lunghi, è consigliabile usare il [modello asincrono funzioni permanenti](../articles/azure-functions/durable/durable-functions-overview.md#async-http) o [rinviare il lavoro effettivo e restituire una risposta immediata.](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)
