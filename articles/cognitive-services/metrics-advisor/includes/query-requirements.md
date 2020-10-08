---
title: Requisiti per le query
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631368"
---
All'interno della query usare il parametro `@StartTime` per ottenere i dati delle metriche per un singolo timestamp. Advisor metriche sostituirà il parametro con una stringa in formato `yyyy-MM-ddTHH:mm:ss` durante l'esecuzione della query.

> [!IMPORTANT]
> La query deve restituire al massimo un record per ogni combinazione di dimensioni, in corrispondenza di ogni timestamp. Tutti i record restituiti dalla query devono avere inoltre gli stessi timestamp. Advisor metriche eseguirà questa query per ogni timestamp per inserire i dati. Per altre informazioni ed esempi, vedere la [sezione delle domande frequenti sulle query](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 