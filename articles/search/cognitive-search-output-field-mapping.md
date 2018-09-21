---
title: Eseguire il mapping dei campi di input migliorati di ricerca cognitiva ai campi di output negli indici di Ricerca di Azure | Microsoft Docs
description: Estrarre e migliorare i campi dati di origine ed eseguire il mapping ai campi di output in un indice di Ricerca di Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 77e7a1cc725dc56ee20d3c1999cfb7cf0039d67f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35775202"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Come eseguire il mapping dei campi migliorati a un indice ricercabile

In questo articolo si apprenderà come eseguire il mapping dei campi di input migliorati ai campi di output in un indice ricercabile. Dopo avere [definito un set di competenze](cognitive-search-defining-skillset.md), è necessario eseguire il mapping dei campi di output di qualsiasi competenza che contribuisce direttamente ai valori di un determinato campo nell'indice di ricerca. Per spostare il contenuto dai documenti migliorati all'indice è necessario eseguire i mapping.


## <a name="use-outputfieldmappings"></a>Usare outputFieldMappings
Per eseguire il mapping dei campi, aggiungere `outputFieldMappings` alla definizione dell'indicizzatore, come illustrato di seguito:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
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
Per ogni mapping di campi di output, impostare il nome del campo migliorato (sourceFieldName) e il nome del campo referenziato nell'indice (targetFieldName).

Il percorso in sourceFieldName può rappresentare uno o più elementi. Nell'esempio precedente, ```/document/content/sentiment``` rappresenta un valore numerico singolo, mentre ```/document/content/organizations/*/description``` rappresenta più descrizioni di organizzazioni. Nei casi in cui sono presenti più elementi, questi vengono resi "flat" in una matrice che li contiene tutti. Più in concreto, nell'esempio ```/document/content/organizations/*/description``` i dati nel campo delle *descrizioni* dovrebbero essere come una matrice flat prima di essere indicizzati:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Passaggi successivi
Dopo che è stato eseguito il mapping dei campi migliorati ai campi ricercabili, è possibile impostare gli attributi per ogni campo ricercabile [come parte della definizione dell'indice](search-what-is-an-index.md).

Per altre informazioni sul mapping dei campi, vedere [Mapping dei campi negli indicizzatori di Ricerca di Azure](search-indexer-field-mappings.md).