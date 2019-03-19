---
title: Metodo CalcHistogram - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Usare il metodo CalcHistogram per calcolare la distribuzione dei valori di attributo per un set di entità documenti.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a228c5b90e47c9c24c5da70484a1a28f9a3054b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100480"
---
# <a name="calchistogram-method"></a>Metodo CalcHistogram

L'API REST **calchistogram** viene usata per calcolare la distribuzione dei valori di attributo per un set di entità documenti.          


**Endpoint REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parametri della richiesta

NOME  |Valore | Obbligatorio?  |DESCRIZIONE
-----------|----------|--------|----------
**expr**    |Stringa di testo | Sì  |Espressione di query che specifica le entità su cui calcolare gli istogrammi.
**model** |Stringa di testo | No  |Selezionare il nome del modello su cui si vuole eseguire la query.  Attualmente il valore predefinito è *latest*.
**attributes** | Stringa di testo | No <br>Impostazione predefinita: | Elenco delimitato da virgole che specifica i valori di attributo inclusi nella risposta. Per i nomi degli attributi viene fatta distinzione tra maiuscole e minuscole.
**count** |Number | No <br>Predefinito: 10 |Numero di risultati da restituire.
**offset**  |Number | No <br>Predefinito: 0 |Indice del primo risultato da restituire.
**timeout**  |Number | No <br>Predefinito: 1000 |Timeout in millisecondi. Vengono restituite solo le interpretazioni trovate prima del timeout.

## <a name="response-json"></a>Risposta (JSON)

NOME | DESCRIZIONE
--------|---------
**expr**  |Parametro expr della richiesta.
**num_entities** | Numero totale di entità corrispondenti.
**histograms** |  Una matrice di istogrammi, uno per ogni attributo specificato nella richiesta.
**histograms[x].attribute** | Nome dell'attributo su cui è stato calcolato l'istogramma.
**histograms[x].distinct_values** | Numero di valori distinti fra le entità corrispondenti per questo attributo.
**histograms[x].total_count** | Numero totale di istanze dei valori fra le entità corrispondenti per questo attributo.
**histograms[x].histogram** | Dati dell'istogramma per questo attributo.
**histograms[x].histogram[y].value** |  Un valore per l'attributo.
**histograms[x].histogram[y].logprob**  |Probabilità del logaritmo naturale totale delle entità corrispondenti con questo valore di attributo.
**histograms[x].histogram[y].count**  |Numero di entità corrispondenti con questo valore di attributo.
**aborted** | True se si è verificato il timeout della richiesta.


#### <a name="example"></a>Esempio:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>In questo esempio, per generare un istogramma del numero di pubblicazioni annuali per un particolare autore dal 2012, è possibile generare prima l'espressione di query usando l'API di **interpretazione** con la stringa di query: *papers by jaime teevan after 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>L'espressione nella prima interpretazione restituita dall'API di interpretazione è *And(Composite(AA.AuN=='jaime teevan'),Y>2012)*.
<br>Questo valore di espressione viene quindi passato all'API **calchistogram**. Il parametro *attributes=Y,F.FN* indica che le distribuzioni dei conteggi dei documenti dovrebbero essere in base a Year e Field of Study, ad esempio:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>La risposta a questa richiesta indica prima di tutto la presenza di 37 documenti che corrispondono all'espressione di query.  Per l'attributo *Year* esistono 3 valori distinti, uno per ogni anno dopo il 2012 (ossia 2013, 2014 e 2015) come specificato nella query.  Il numero totale dei documenti sui 3 valori distinti è 37.  Per ogni attributo *Year* l'istogramma mostra il valore, la probabilità del logaritmo naturale totale e il numero di entità corrispondenti.     

L'istogramma per l'attributo *Field of Study* indica la presenza di 34 campi distinti di studio. Poiché un documento può essere associato a più campi di studio, il numero totale (53) può essere superiore al numero di entità corrispondenti.  Nonostante siano presenti 34 valori distinti, la risposta include solo i primi 4 a causa del parametro *count=4*.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
