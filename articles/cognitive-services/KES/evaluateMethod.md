---
title: Metodo Evaluate - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare il metodo Evaluate nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814099"
---
# <a name="evaluate-method"></a>Metodo evaluate

Il metodo *evaluate* valuta e restituisce l'output di un'espressione di query strutturata in base ai dati dell'indice.

In genere, si otterrà un'espressione da una risposta al metodo interpret.  Ma è possibile anche comporre manualmente le espressioni di query (vedere [Espressioni di query strutturate](Expressions.md)).  

## <a name="request"></a>Richiesta 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

NOME|Value|DESCRIZIONE
----|----|----
expr       | Stringa di testo | Espressione di query strutturata che seleziona un subset di entità di indice.
attributes | Stringa di testo | Elenco delimitato da virgole di attributi da includere nella risposta.
count      | Numero (valore predefinito=10) | Numero massimo di risultati da restituire.
offset     | Numero (valore predefinito=0) | Indice del primo risultato da restituire.
orderby |   Stringa di testo | Nome dell'attributo usato per ordinare i risultati, seguito dall'ordinamento facoltativo (valore predefinito=asc): "*attrname*[:(asc&#124;desc)]".  Se non è specificato, i risultati vengono restituiti in ordine di probabilità logaritmica naturale decrescente.
timeout  | Numero (valore predefinito=1000) | Timeout in millisecondi. Vengono restituiti solo i risultati calcolati prima del timeout.

Usando i parametri *count* e *offset*, un numero elevato di risultati può essere ottenuto in modo incrementale attraverso più richieste.
  
## <a name="response-json"></a>Risposta (JSON)
JSONPath|DESCRIZIONE
----|----
$.expr | Parametro *expr* della richiesta.
$.entities | Matrice di 0 o più entità oggetto che corrispondono all'espressione di query strutturata. 
$.aborted | True se si è verificato il timeout della richiesta.

Ogni entità contiene un valore *logprob* e i valori degli altri attributi richiesti.

## <a name="example"></a>Esempio
Nell'esempio relativo alle pubblicazioni accademiche, la richiesta seguente passa un'espressione di query strutturata (potenzialmente da un output di una richiesta *interpret*) e recupera alcuni attributi per le prime 2 entità corrispondenti:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

La risposta contiene le prime 2 ("count=2") entità corrispondenti.  Per ogni entità vengono restituiti titolo, anno, nome autore e ID autore.  Si noti come la struttura dei valori dell'attributo composito corrisponda al modo in cui i valori sono specificati nel file di dati. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
