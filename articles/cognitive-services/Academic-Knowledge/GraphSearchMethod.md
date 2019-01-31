---
title: Metodo Graph Search - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Usare il metodo Graph Search nell'API Academic Knowledge per restituire un set di entità accademiche basate su specifici modelli di grafo.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e8472323d99de9d0d81130eac5fbe3ec9e11c590
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197637"
---
# <a name="graph-search-method"></a>Metodo Graph Search

L'API REST **Graph Search** viene usata per restituire un set di entità accademiche basate sui modelli di grafo specificati.  La risposta è un set di percorsi di grafo che soddisfa i vincoli specificati dall'utente. Un percorso di grafo è una sequenza collegata dei nodi e degli archi del grafo nel formato _v0, e0, v1, e1, ..., vn_, dove _v0_ è il nodo di inizio del percorso.
<br>

**Endpoint REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parametri della richiesta  
NOME     | Valore | Obbligatorio?  | DESCRIZIONE
-----------|-----------|---------|--------
**mode**       | Stringa di testo | Yes | Nome della modalità che si vuole usare. Il valore può essere *json* oppure *lambda*.

Il metodo Graph Search deve essere chiamato tramite una richiesta HTTP POST. La richiesta POST deve includere l'intestazione del tipo di contenuto: **application/json**.

##### <a name="json-search"></a>Ricerca JSON 

Per la ricerca *json*, il corpo POST è un oggetto JSON. L'oggetto JSON descrive un modello di percorso con i vincoli specificati dall'utente (vedere la [specifica dell'oggetto JSON](JSONSearchSyntax.md) per la ricerca *json*).


##### <a name="lambda-search"></a>Ricerca lambda

Per la ricerca *lambda*, il corpo POST è una stringa di testo normale. Il corpo POST è una stringa di query lambda LIKQ, ovvero una singola istruzione C# (vedere la [specifica della stringa di query](LambdaSearchSyntax.md) per la ricerca *lambda*). 

<br>
## <a name="response-json"></a>Risposta (JSON)
NOME | DESCRIZIONE
-------|-----   
**results** | Matrice di 0 o più entità che corrispondono all'espressione di query. Ogni entità contiene i valori degli attributi richiesti. Questo campo è presente se la richiesta è stata elaborata correttamente.
**error** | Codici di stato HTTP. Questo campo è presente se la richiesta ha esito negativo.
**message** | Messaggio di errore. Questo campo è presente se la richiesta ha esito negativo.

Se una query non può essere elaborata entro _800 ms_ verrà restituito un errore di _timeout_. 

<br>
#### <a name="example"></a>Esempio:

##### <a name="json-search"></a>Ricerca JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Per la ricerca *json*, se si vogliono recuperare i documenti con titoli contenenti "graph engine" e scritti da "bin shao", è possibile specificare la query come indicato di seguito.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

L'output di una query è una matrice di percorsi del grafo. Un percorso del grafo è una matrice di oggetti nodo corrispondenti ai nodi specificati nel percorso della query. Questi oggetti nodo hanno almeno una proprietà *CellID*, che rappresenta l'ID dell'entità. Altre proprietà possono essere recuperate specificando i nomi delle proprietà tramite l'operatore di selezione di un [*oggetto per l'azione di attraversamento*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Ricerca lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Per la ricerca *lambda*, se si vogliono ottenere gli ID dell'autore di un documento specifico, è possibile scrivere una query simile a quella seguente.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Anche l'output di una ricerca *lambda* è una matrice di percorsi del grafo:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Schema del grafo

Lo schema del grafo è utile per la scrittura di query di ricerca nel grafo. Viene visualizzato nella figura seguente.

![Schema di Microsoft Academic Graph](./Images/AcademicGraphSchema.png)
