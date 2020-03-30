---
title: Eseguire il mapping dell'input ai campi di output
titleSuffix: Azure Cognitive Search
description: Estrarre e arricchire i campi dei dati di origine ed eseguire il mapping ai campi di output in un indice di Ricerca cognitiva di Azure.Extract and enrich source data fields, and map to output fields in an Azure Cognitive Search index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280976"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Come mappare i campi arricchiti di AI a un indice ricercabile

In questo articolo si apprenderà come eseguire il mapping dei campi di input migliorati ai campi di output in un indice ricercabile. Dopo avere [definito un set di competenze](cognitive-search-defining-skillset.md), è necessario eseguire il mapping dei campi di output di qualsiasi competenza che contribuisce direttamente ai valori di un determinato campo nell'indice di ricerca. 

I mapping dei campi di output sono necessari per spostare il contenuto dai documenti arricchiti nell'indice.  Il documento arricchito è in realtà un albero di informazioni e, anche se esiste il supporto per i tipi complessi nell'indice, a volte è possibile trasformare le informazioni dall'albero arricchito in un tipo più semplice (ad esempio, una matrice di stringhe). I mapping dei campi di output consentono di eseguire trasformazioni delle forme dei dati appiattindo le informazioni.

## <a name="use-outputfieldmappings"></a>Usare outputFieldMappings
Per eseguire il mapping dei campi, aggiungere `outputFieldMappings` alla definizione dell'indicizzatore, come illustrato di seguito:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
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
            "targetFieldName": "descriptions"
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

Per ogni mapping dei campi di output, impostare la posizione dei dati nell'albero del documento arricchito (sourceFieldName) e il nome del campo a cui si fa riferimento nell'indice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Appiattimento delle informazioni da tipi complessi 

Il percorso in sourceFieldName può rappresentare uno o più elementi. Nell'esempio precedente, ```/document/content/sentiment``` rappresenta un valore numerico singolo, mentre ```/document/content/organizations/*/description``` rappresenta più descrizioni di organizzazioni. 

Nei casi in cui sono presenti più elementi, questi vengono resi "flat" in una matrice che li contiene tutti. 

Più in concreto, nell'esempio ```/document/content/organizations/*/description``` i dati nel campo delle *descrizioni* dovrebbero essere come una matrice flat prima di essere indicizzati:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Questo è un principio importante, quindi forniremo un altro esempio. Si supponga di disporre di una serie di tipi complessi come parte dell'albero di arricchimento. Supponiamo che ci sia un membro chiamato customEntities che ha una matrice di tipi complessi come quello descritto di seguito.

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

Supponiamo che l'indice abbia un campo denominato 'malattie' di tipo Collection(Edm.String), in cui si desidera archiviare ognuno dei nomi delle entità. 

Questo può essere fatto facilmente\*utilizzando il simbolo " ", come segue:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Questa operazione sarà semplicemente "appiattire" ognuno dei nomi degli elementi customEntities in una singola matrice di stringhe come questa:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Passaggi successivi
Dopo che è stato eseguito il mapping dei campi migliorati ai campi ricercabili, è possibile impostare gli attributi per ogni campo ricercabile [come parte della definizione dell'indice](search-what-is-an-index.md).

Per altre informazioni sul mapping dei campi, vedere [Mapping dei campi negli indicizzatori di Ricerca cognitiva](search-indexer-field-mappings.md)di Azure.For more information about field mapping, see Field mappings in Azure Cognitive Search indexers .
