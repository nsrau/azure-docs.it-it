---
title: Copia dati nell'indice di ricerca
description: Informazioni su come eseguire il push o la copia di dati in un indice di Ricerca di Azure usando l'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 8a5b7bd366c504f0f5f4652728bf265289fb92e8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929684"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Copiare dati in un indice di ricerca cognitiva di Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-search-connector.md)
> * [Versione corrente](connector-azure-search.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare i dati in Azure ricerca cognitiva index. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da qualsiasi archivio dati di origine supportato nell'indice di ricerca. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Inizia ora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche del connettore Azure ricerca cognitiva.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure ricerca cognitiva sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureSearch** | SÌ |
| url | URL per il servizio di ricerca. | SÌ |
| key | Chiave di amministrazione per il servizio di ricerca. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | SÌ |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non diversamente specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

> [!IMPORTANT]
> Quando si copiano dati da un archivio dati cloud a un indice di ricerca, in Azure ricerca cognitiva servizio collegato è necessario fare riferimento a una Azure Integration Runtime con area esplicita in connactVia. Impostare l'area come quella in cui risiede il servizio di ricerca. Altre informazioni da [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Esempio:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati ricerca cognitiva di Azure.

Per copiare dati in ricerca cognitiva di Azure, sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzureSearchIndex** | SÌ |
| indexName | Nome dell'indice di ricerca. Il servizio Data Factory non crea l'indice. L'indice deve esistere in ricerca cognitiva di Azure. | SÌ |

**Esempio:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine ricerca cognitiva di Azure.

### <a name="azure-cognitive-search-as-sink"></a>Azure ricerca cognitiva come sink

Per copiare i dati in ricerca cognitiva di Azure, impostare il tipo di origine nell'attività di copia su **AzureSearchIndexSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureSearchIndexSink** | SÌ |
| writeBehavior | Specifica se eseguire un'unione o una sostituzione quando nell'indice esiste già un documento. Vedere la [proprietà WriteBehavior](#writebehavior-property).<br/><br/>I valori consentiti sono: **Merge** (predefinito) e **Carica**. | No |
| writeBatchSize | Carica i dati nell'indice di ricerca quando la dimensione del buffer raggiunge writeBatchSize. Per informazioni dettagliate, vedere la [proprietà WriteBatchSize](#writebatchsize-property).<br/><br/>I valori consentiti sono: integer da 1 a 1.000; il valore predefinito è 1000. | No |

### <a name="writebehavior-property"></a>Proprietà WriteBehavior

Durante la scrittura di dati, AzureSearchSink esegue operazioni di upsert. In altre parole, quando si scrive un documento, se la chiave del documento esiste già nell'indice di ricerca, Azure ricerca cognitiva aggiorna il documento esistente anziché generare un'eccezione di conflitto.

Le operazioni di upsert eseguite da AzureSearchSink sono le seguenti (con AzureSearch SDK):

- **Merge**: le colonne del nuovo documento vengono unite con quelle del documento esistente. Per le colonne del nuovo documento con valore Null, viene mantenuto il valore del documento esistente.
- **Upload**: il nuovo documento sostituisce quello esistente. Per le colonne del nuovo documento non specificate, il valore è impostato su Null indipendentemente dalla presenza o meno di un valore diverso da Null nel documento esistente.

L'operazione predefinita è **Merge**.

### <a name="writebatchsize-property"></a>Proprietà WriteBatchSize

Il servizio ricerca cognitiva di Azure supporta la scrittura di documenti come batch. Un batch può contenere da 1 a 1000 azioni e un'azione gestisce un documento per eseguire l'operazione di caricamento/unione.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Supporto dei tipi di dati

La tabella seguente specifica se un tipo di dati di ricerca cognitiva di Azure è supportato o meno.

| Tipo di dati ricerca cognitiva di Azure | Supportato in Azure ricerca cognitiva sink |
| ---------------------- | ------------------------------ |
| Stringa | S |
| Int32 | S |
| Int64 | S |
| DOUBLE | S |
| boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

Attualmente, altri tipi di dati, ad esempio ComplexType, non sono supportati. Per un elenco completo dei tipi di dati supportati da Azure ricerca cognitiva, vedere [tipi di dati supportati (ricerca cognitiva di Azure)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
