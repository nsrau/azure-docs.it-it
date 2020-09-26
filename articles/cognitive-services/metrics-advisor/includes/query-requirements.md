---
title: Requisiti per le query
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377142"
---
All'interno della query usare il parametro `@StartTime` per ottenere i dati delle metriche per un particolare timestamp. Verrà sostituito con una stringa in formato `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Verificare che la query restituisca solo i dati delle metriche di **un singolo timestamp**. Advisor metriche eseguirà la query su ogni timestamp per ottenere i dati delle metriche corrispondenti. Ad esempio, una query per una metrica con *granularità* giornaliera deve contenere un solo timestamp, ad esempio `2020-06-21T00:00:00Z` quando si esegue la query una sola volta. 
