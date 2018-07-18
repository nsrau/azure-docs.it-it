---
title: 'Ricerca personalizzata Bing: Ottenere i suggerimenti automatici personalizzati | Microsoft Docs'
description: Descrive come recuperare i suggerimenti automatici personalizzati
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374708"
---
# <a name="get-custom-suggestions"></a>Ottenere suggerimenti personalizzati
Prima di inviare query a Ricerca personalizzata Bing, chiamare l'API Suggerimenti automatici personalizzati per creare dei suggerimenti dei termini di ricerca e migliorare l'esperienza di ricerca. L'API Suggerimenti automatici personalizzati restituisce un elenco di query suggerite in base a una stringa di query parziale fornita dall'utente. I termini di query personalizzati pertinenti che vengono specificati verranno visualizzati prima dei suggerimenti generati da Suggerimenti automatici. Per altre informazioni, vedere [Definire i suggerimenti di ricerca personalizzati](define-custom-suggestions.md).

## <a name="endpoint"></a>Endpoint
Per ottenere query suggerite tramite l'API Ricerca personalizzata Bing, inviare una richiesta `GET` all'endpoint seguente.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Risposta JSON
La risposta contiene un elenco di oggetti SearchAction contenenti i termini della query suggerita.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Ogni suggerimento include i campi `displayText` e `query`. Il campo `displayText` contiene la query suggerita che si usa per popolare l'elenco a discesa della casella di ricerca.

Se l'utente seleziona una query suggerita dall'elenco a discesa, usare il termine della query nel campo `query` quando si chiama l'[API Ricerca personalizzata Bing](overview.md).

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Chiamare la ricerca personalizzata](./search-your-custom-view.md)
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)