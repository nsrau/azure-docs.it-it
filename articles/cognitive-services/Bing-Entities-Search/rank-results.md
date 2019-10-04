---
title: Uso della classificazione per visualizzare le risposte - Ricerca entità Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la classificazione per visualizzare le risposte restituite dall'API Ricerca entità Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423911"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Uso della classificazione per visualizzare i risultati della ricerca di entità  

La risposta di ogni ricerca di entità include una risposta [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) che specifica come devono essere visualizzati i risultati della ricerca restituiti dall'API Ricerca entità Bing. La risposta di classificazione raggruppa i risultati in contenuto prominente, principale e della barra laterale. Il risultato prominente è il risultato più importante o significativo e deve essere visualizzato per primo. Se i risultati rimanenti non vengono visualizzati in un formato di contenuto principale e della barra laterale tradizionale, è necessario dare al contenuto principale una maggiore visibilità rispetto al contenuto della barra laterale. 
  
All'interno di ogni gruppo, la matrice [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica l'ordine in cui deve essere visualizzato il contenuto. Ogni elemento offre due modi per identificare il risultato all'interno di una risposta.  
 

|Campo | Descrizione  |
|---------|---------|
|`answerType` e `resultIndex` | `answerType` identifica la risposta (Entity o Place) e `resultIndex` identifica un risultato all'interno di tale risposta (ad esempio, un'entità). L'indice è in base 0.|
|`value`    | `value` Contiene un ID che corrisponde all'ID di una risposta o di un risultato all'interno della risposta. L'ID è incluso nel riscontro o nei risultati, ma non in entrambi. |
  
L'uso di `answerType` e `resultIndex` è un processo in due passaggi. Innanzitutto, usare `answerType` per identificare la risposta contenente i risultati da visualizzare. Usare quindi `resultIndex` per indicizzare i risultati della risposta e ottenere il risultato da visualizzare. Il valore `answerType` è il nome del campo nell'oggetto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse). Se occorre visualizzare i risultati del riscontro tutti insieme, l'elemento della risposta di classificazione non include il campo `resultIndex`.

Per usare l'ID è necessario che l'ID di classificazione corrisponda all'ID di una risposta o a uno dei risultati. Se un oggetto risposta include un campo `id`, tutti i risultati della risposta vengono visualizzati insieme. Ad esempio, se l'oggetto `Entities` include il campo `id`, gli articoli di tutte le entità vengono visualizzati insieme. Se l'oggetto `Entities` non include il campo `id`, ogni entità conterrà un campo `id` e la risposta di classificazione combinerà le entità con i risultati di Places.  
  
## <a name="ranking-response-example"></a>Esempio di risposta di classificazione

Di seguito viene illustrato un esempio di [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

In base alla risposta di classificazione, la barra laterale visualizza i due risultati di entità correlati a Jimi Hendrix.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](tutorial-bing-entities-search-single-page-app.md)
