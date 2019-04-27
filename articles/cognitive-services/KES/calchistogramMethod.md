---
title: Metodo CalcHistogram - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare il metodo CalcHistogram nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: aaa5b3a85c08f11d821557257de451b8ffc8a3fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814164"
---
# <a name="calchistogram-method"></a>Metodo calchistogram
Il metodo *calchistogram* elabora gli oggetti che corrispondono a un'espressione di query strutturata e calcola la distribuzione dei relativi valori di attributo.

## <a name="request"></a>Richiesta
`http://<host>/calchistogram?expr=<expr>[&options]` 

NOME|Value|DESCRIZIONE
----|-----|-----------
expr | Stringa di testo | Espressione di query strutturata che specifica le entità di indice su cui calcolare gli istogrammi.
attributes | Stringa di testo (valore predefinito="") | Elenco delimitato da virgole di attributi da includere nella risposta.
count   | Numero (valore predefinito=10) | Numero di risultati da restituire.
offset  | Numero (valore predefinito=0) | Indice del primo risultato da restituire.

## <a name="response-json"></a>Risposta (JSON)
JSONPath | DESCRIZIONE
----|----
$.expr | Parametro *expr* della richiesta.
$.num_entities | Numero totale di entità corrispondenti.
$.histograms |  Matrice di istogrammi, uno per ogni attributo richiesto.
$.histograms[\*].attribute | Nome dell'attributo su cui è stato calcolato l'istogramma.
$.histograms[\*].distinct_values | Numero di valori distinti fra le entità corrispondenti per questo attributo.
$.histograms[\*].total_count | Numero totale di istanze dei valori fra le entità corrispondenti per questo attributo.
$.histograms[\*].histogram | Dati dell'istogramma per questo attributo.
$.histograms[\*].histogram[\*].value | Valore dell'attributo.
$.histograms[\*].histogram[\*].logprob  | Probabilità del logaritmo naturale totale delle entità corrispondenti con questo valore di attributo.
$.histograms[\*].histogram[\*].count    | Numero di entità corrispondenti con questo valore di attributo.
$.aborted | True se si è verificato il timeout della richiesta.

### <a name="example"></a>Esempio
Nell'esempio delle pubblicazioni accademiche, il codice seguente calcola un istogramma di conteggi di pubblicazioni per anno e per parola chiave per un determinato autore dal 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

La risposta indica la presenza di 37 documenti corrispondenti all'espressione di query.  Per l'attributo *Year* esistono 3 valori distinti, uno per ogni anno dopo il 2013.  Il numero totale dei documenti sui 3 valori distinti è 37.  Per ogni attributo *Year* l'istogramma mostra il valore, la probabilità del logaritmo naturale totale e il numero di entità corrispondenti.     

L'istogramma per l'attributo *Keyword* mostra la presenza di 34 parole chiave distinte. Poiché un documento può essere associato a più parole chiave, il numero totale (53) può essere superiore al numero di entità corrispondenti.  Nonostante siano presenti 34 valori distinti, la risposta include solo i primi 4 a causa del parametro count=4.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
