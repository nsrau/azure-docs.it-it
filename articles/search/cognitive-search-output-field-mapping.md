---
title: Eseguire il mapping dell'input ai campi di output
titleSuffix: Azure Cognitive Search
description: Estrarre e arricchire i campi dei dati di origine ed eseguire il mapping ai campi di output in un indice di ricerca cognitiva di Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935365"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Come eseguire il mapping di campi arricchiti di intelligenza artificiale a un indice ricercabile

![Fasi dell'indicizzatore](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "fasi dell'indicizzatore")

In questo articolo si apprenderà come eseguire il mapping dei campi di input migliorati ai campi di output in un indice ricercabile. Dopo avere [definito un set di competenze](cognitive-search-defining-skillset.md), è necessario eseguire il mapping dei campi di output di qualsiasi competenza che contribuisce direttamente ai valori di un determinato campo nell'indice di ricerca.

I mapping dei campi di output sono necessari per lo trasferimento di contenuto da documenti arricchiti all'indice.  Il documento arricchito è effettivamente un albero di informazioni e anche se è disponibile il supporto per i tipi complessi nell'indice, a volte può essere necessario trasformare le informazioni dall'albero arricchito in un tipo più semplice, ad esempio una matrice di stringhe. I mapping dei campi di output consentono di eseguire trasformazioni di forme dati mediante l'appiattimento delle informazioni. I mapping dei campi di output si verificano sempre dopo l'esecuzione del livello di competenze, sebbene sia possibile eseguire questa fase anche se non è stato definito alcun skillt.

Esempi di mapping dei campi di output:

* Nell'ambito delle proprie competenze, sono stati estratti i nomi delle organizzazioni citate in ognuna delle pagine del documento. A questo punto si desidera eseguire il mapping di ognuno di questi nomi di organizzazione in un campo nell'indice di tipo EDM. Collection (EDM. String).

* Come parte del tuo insieme di competenze, hai prodotto un nuovo nodo denominato "Document/translated_text". Si desidera eseguire il mapping delle informazioni su questo nodo a un campo specifico nell'indice.

* Non si dispone di un insieme di competenze, ma si indicizza un tipo complesso da un database Cosmos DB. Si vuole ottenere un nodo su tale tipo complesso ed eseguirne il mapping in un campo nell'indice.

> [!NOTE]
> Di recente è stata abilitata la funzionalità di mapping di funzioni nei mapping dei campi di output. Per altri dettagli sulle funzioni di mapping, vedere [funzioni di mapping dei campi](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>Usare outputFieldMappings

Per eseguire il mapping dei campi, aggiungere `outputFieldMappings` alla definizione dell'indicizzatore, come illustrato di seguito:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

Il corpo della richiesta è strutturato nel modo seguente:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Per ogni mapping di campi di output, impostare il percorso dei dati nell'albero del documento arricchito (sourceFieldName) e il nome del campo come a cui si fa riferimento nell'indice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Appiattimento delle informazioni dai tipi complessi 

Il percorso in sourceFieldName può rappresentare uno o più elementi. Nell'esempio precedente, ```/document/content/sentiment``` rappresenta un valore numerico singolo, mentre ```/document/content/organizations/*/description``` rappresenta più descrizioni di organizzazioni. 

Nei casi in cui sono presenti più elementi, questi vengono resi "flat" in una matrice che li contiene tutti. 

Più in concreto, nell'esempio ```/document/content/organizations/*/description``` i dati nel campo delle *descrizioni* dovrebbero essere come una matrice flat prima di essere indicizzati:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Si tratta di un principio importante, quindi verrà fornito un altro esempio. Si supponga di disporre di una matrice di tipi complessi come parte dell'albero di arricchimento. Supponiamo che sia presente un membro denominato customEntities con una matrice di tipi complessi come quello descritto di seguito.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Si supponga che l'indice includa un campo denominato "patologie" di tipo Collection (EDM. String), in cui si desidera archiviare ognuno dei nomi delle entità. 

Questa operazione può essere eseguita facilmente usando il \* simbolo "", come indicato di seguito:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Questa operazione consentirà semplicemente di "appiattire" ognuno dei nomi degli elementi customEntities in una singola matrice di stringhe come la seguente:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Passaggi successivi
Dopo che è stato eseguito il mapping dei campi migliorati ai campi ricercabili, è possibile impostare gli attributi per ogni campo ricercabile [come parte della definizione dell'indice](search-what-is-an-index.md).

Per altre informazioni sul mapping dei campi, vedere [mapping dei campi in Azure ricerca cognitiva Indexers](search-indexer-field-mappings.md).