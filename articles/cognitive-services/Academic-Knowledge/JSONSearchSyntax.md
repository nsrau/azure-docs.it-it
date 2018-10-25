---
title: Sintassi di ricerca JSON - API Academic Knowledge
titlesuffix: Azure Cognitive Services
description: Informazioni sulla sintassi di ricerca JSON che è possibile usare nell'API Academic Knowledge.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5ece028f89ad9e93840211383db97a5d8a80069a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900411"
---
# <a name="json-search-syntax"></a>Sintassi di ricerca JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

I nomi dei nodi in un percorso di query (_v0, v1,..._ ) fungono da identificatori dei nodi a cui è possibile fare riferimento nell'oggetto query. I nomi dei limiti (_e0, e1,..._ ) nel percorso rappresentano i tipi di limiti corrispondenti. È possibile usare un asterisco _*_ come nome di un nodo o di un limite (ad eccezione del nodo iniziale, che deve essere specificato) per dichiarare che non ci sono vincoli per tale elemento. Un percorso di query `/v0/*/v1/e1/*/`, ad esempio, recupera i percorsi dal grafico senza restrizioni per il tipo di limite _(v0, v1)_. La query non ha nemmeno vincoli sulla destinazione (l'ultimo nodo) del percorso.

Quando un percorso contiene un solo nodo, ad esempio _v0_, la query restituisce semplicemente tutte le entità che soddisfano i vincoli. Un oggetto vincolo applicato al nodo iniziale viene detto *oggetto query iniziale* e la relativa specifica è illustrata di seguito.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Quando un percorso contiene più di un nodo iniziale, Query Processor esegue un attraversamento grafico seguendo il modello di percorso specificato. Quando viene raggiunto un nodo, vengono attivate le azioni di attraversamento specificate dall'utente, che determinano se arrestarsi nel nodo corrente e restituire il risultato o continuare a esplorare il grafico. Quando non è specificata alcuna azione di attraversamento, vengono eseguite le azioni predefinite. Per un nodo intermedio, l'azione predefinita consiste nel continuare a esplorare il grafico. Per l'ultimo nodo di un percorso, l'azione predefinita consiste nell'arrestarsi e restituire il risultato. Un oggetto vincolo che specifica le azioni di attraversamento è detto *oggetto azione di attraversamento*. La relativa specifica è illustrata di seguito:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

Il corpo POST di una query di ricerca *json* deve contenere almeno un modello *path*. Gli oggetti azione di attraversamento sono facoltativi. Ecco due esempi.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

