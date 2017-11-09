---
title: Copiare dati da/in Azure Cosmos DB usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi dati di origine supportati in Azure Cosmos DB o da Cosmos DB in archivi sink supportati usando Data Factory.
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 7d914684a0ee5598cee7972b78c3ec6296184466
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Copiare dati da o in Azure Cosmos DB usando Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-azure-documentdb-connector.md)
> * [Versione 2 - Anteprima](connector-azure-cosmos-db.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Cosmos DB (API DocumentDB). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Azure Cosmos DB nella versione 1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-scenarios"></a>Scenari supportati

È possibile copiare dati da Azure Cosmos DB in qualsiasi archivio dati di sink supportato o da qualsiasi archivio dati di origine supportato in Azure Cosmos DB. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Azure Cosmos DB supporta:

- [API DocumentDB](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction) per Cosmos DB.
- L'importazione/esportazione di documenti JSON così come sono, nonché la copia di dati da/in un set di dati tabulare, ad esempio un database SQL, un file CSV e così via.

Per copiare documenti così come sono da/in file JSON o in un'altra raccolta Cosmos DB, vedere [Importare/esportare documenti JSON](#importexport-json-documents).

## <a name="getting-started"></a>introduttiva
È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Azure Cosmos DB.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure Cosmos DB sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **CosmosDb**. | Sì |
| connectionString |Specificare le informazioni necessarie per connettersi al database di Azure Cosmos DB. È necessario specificare le informazioni sul database nella stringa di connessione come illustrato nell'esempio seguente. Contrassegnare questo campo come SecureString. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Azure Cosmos DB.

Per copiare dati da/in Azure Cosmos DB, impostare la proprietà type del set di dati su **DocumentDbCollection**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **DocumentDbCollection** |Sì |
| collectionName |Nome della raccolta documenti di Cosmos DB. |Sì |

**Esempio:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema da Data Factory

Per gli archivi dati privi di schema come Azure Cosmos DB, l'attività di copia deduce lo schema in uno dei modi descritti di seguito. A meno che non si voglia [importare/esportare documenti JSON come così sono](#importexport-json-documents), quindi, la procedura consigliata è quella di specificare la struttura di dati nella sezione **struttura**.

1. Se si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, il servizio Data Factory considera la struttura come schema. In questo caso, se una riga non contiene un valore per una colonna, verrà inserito un valore null.
2. Se non si specifica la struttura dei dati usando la proprietà **structure** nella definizione del set di dati, il servizio Data Factory deduce lo schema usando la prima riga di dati. In questo caso, se la prima riga non contiene lo schema completo, alcune colonne non saranno presenti nel risultato dell'operazione di copia.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure Cosmos DB.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB come origine

Per copiare dati da Azure Cosmos DB, impostare il tipo di origine nell'attività di copia su **DocumentDbCollectionSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **DocumentDbCollectionSource** |Sì |
| query |Specificare la query Cosmos DB per leggere i dati.<br/><br/>Esempio: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Se non specificato, l'istruzione SQL eseguita: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Carattere speciale per indicare che il documento è nidificato e come rendere flat il set di risultati.<br/><br/>Se, ad esempio, una query Cosmos DB restituisce un risultato nidificato `"Name": {"First": "John"}`, l'attività di copia identificherà il nome di colonna come "Name.First" con valore "John" se nestedSeparator è un punto. |No (il valore predefinito è il punto `.`) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB come sink

Per copiare dati da Azure Cosmos DB, impostare il tipo di sink nell'attività di copia su **DocumentDbCollectionSink**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **DocumentDbCollectionSink** |Sì |
| nestingSeparator |È necessario un carattere speciale nel nome della colonna di origine per indicare tale documento nidificato. <br/><br/>Se nestedSeparator è un punto, `Name.First` nella struttura del set di dati di output genera, ad esempio, la struttura JSON seguente nel documento Cosmos DB: `"Name": {"First": "[value maps to this column from source]"}`. |No (il valore predefinito è il punto `.`) |
| writeBatchTimeout |Tempo di attesa per il completamento dell’operazione prima del timeout.<br/><br/>I valori consentiti sono: intervallo di tempo. Ad esempio: "00:30:00" (30 minuti). |No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importare/esportare documenti JSON

Usando questo connettore Cosmos DB, è possibile:

* Importare in Cosmos DB documenti JSON da varie origini, tra cui BLOB di Azure, Azure Data Lake Store e altri archivi basati su file supportati da Azure Data Factory.
* Esportare i documenti JSON da raccolte Cosmos DB in diversi archivi basati su file.
* Copiare i documenti tra due raccolte Cosmos DB così come sono.

Per ottenere la copia senza schema:

- Nei set di dati Cosmos DB non specificare la sezione "struttura"; nell'origine/sink Cosmos DB dell'attività di copia non specificare la proprietà "nestingSeparator" .
- Quando si importa/esporta da/in file JSON, nel set di dati dell'archivio file corrispondente specificare il tipo di formato come "JsonFormat" e configurare "filePattern" correttamente (vedere la sezione [formato JSON](supported-file-formats-and-compression-codecs.md#json-format) per informazioni dettagliate), non specificare la sezione "struttura" e ignorare le altre impostazioni sul formato.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).