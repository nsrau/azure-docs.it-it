---
title: file di inclusione
description: file di inclusione
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941712"
---
## <a name="timeout"></a>Durata timeout app per le funzioni 

La durata del timeout di un'app per le funzioni è definita dalla proprietà functionTimeout nel file di progetto [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . La tabella seguente mostra i valori predefiniti e massimi in minuti per entrambi i piani e in entrambe le versioni di runtime:

| Piano | Versione runtime | Predefinito | Massima |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Consumo | 3.x | 5 | 10 |
| Servizio app | 1.x | Illimitato | Illimitato |
| Servizio app | 2.x | 30 | Illimitato |
| Servizio app | 3.x | 30 | Illimitato |

> [!NOTE] 
> Indipendentemente dall'impostazione di timeout dell'app per le funzioni, 230 secondi è la quantità massima di tempo che una funzione attivata da HTTP può intraprendere per rispondere a una richiesta. Questo è dovuto al [timeout di inattività predefinito del Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Per tempi di elaborazione più lunghi, provare a usare il [modello asincrono Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) o [rinviare il lavoro effettivo e restituire una risposta immediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
