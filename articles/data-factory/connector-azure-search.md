---
title: Copiare dati nell'indice di Ricerca usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come eseguire il push o la copia di dati in un indice di Ricerca di Azure usando l'attività di copia in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: d8db545fc58f3cc2e18bbc9a732ed16ef8b563c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Copiare dati in un indice di Ricerca di Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-azure-search-connector.md)
> * [Versione 2 - Anteprima](connector-azure-search.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati nell'indice di Ricerca di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Ricerca di Azure nella versione 1](v1/data-factory-azure-search-connector.md).

## <a name="supported-scenarios"></a>Scenari supportati

È possibile copiare dati da qualsiasi archivio dati di origine supportato nell'indice di Ricerca di Azure. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>introduttiva
È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano le informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Ricerca di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Ricerca di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureSearch** | Sì |
| URL | URL del servizio Ricerca di Azure. | sì |
| key | Chiave amministratore del servizio Ricerca di Azure. Contrassegnare questo campo come SecureString. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

> [!IMPORTANT]
> Quando si copiano dati da un archivio dati cloud nell'indice di Ricerca di Azure, nel servizio collegato di Ricerca di Azure è necessario fare riferimento a un runtime di integrazione di Azure con area esplicita in connactVia. Impostare l'area in base a quella in cui si trova Ricerca di Azure. Altre informazioni sono disponibili in [Runtime di integrazione di Azure] (concepts-integration-runtime.md#azure-integration-runtime).

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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Ricerca di Azure.

Per copiare dati in Ricerca di Azure, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzureSearchIndex** | Sì |
| indexName | Nome dell'indice di Ricerca di Azure. Il servizio Data Factory non crea l'indice. L'indice deve essere presente in Ricerca di Azure. | Sì |

**Esempio:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Ricerca di Azure.

### <a name="azure-search-as-sink"></a>Ricerca di Azure come sink

Per copiare dati in Ricerca di Azure, impostare il tipo di origine nell'attività di copia su **AzureSearchIndexSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureSearchIndexSink** | Sì |
| writebehavior | Specifica se eseguire un'unione o una sostituzione quando nell'indice esiste già un documento. Vedere la [proprietà WriteBehavior](#writebehavior-property).<br/><br/>I valori consentiti sono: **Merge** (predefinito) e **Carica**. | No |
| writeBatchSize | Consente di caricare dati nell'indice di Ricerca di Azure quando le dimensioni del buffer raggiungono il valore indicato da writeBatchSize. Per informazioni dettagliate, vedere la [proprietà WriteBatchSize](#writebatchsize-property).<br/><br/>I valori consentiti sono: integer da 1 a 1.000; il valore predefinito è 1000. | No |

### <a name="writebehavior-property"></a>Proprietà WriteBehavior

Durante la scrittura di dati, AzureSearchSink esegue operazioni di upsert. In altre parole, quando si scrive un documento il servizio Ricerca di Azure aggiorna il documento esistente anziché generare un'eccezione di conflitto se la chiave relativa esiste già nell'indice di Ricerca di Azure.

Le operazioni di upsert eseguite da AzureSearchSink sono le seguenti (con AzureSearch SDK):

- **Merge**: le colonne del nuovo documento vengono unite con quelle del documento esistente. Per le colonne del nuovo documento con valore Null, viene mantenuto il valore del documento esistente.
- **Upload**: il nuovo documento sostituisce quello esistente. Per le colonne del nuovo documento non specificate, il valore è impostato su Null indipendentemente dalla presenza o meno di un valore diverso da Null nel documento esistente.

L'operazione predefinita è **Merge**.

### <a name="writebatchsize-property"></a>Proprietà WriteBatchSize

Il servizio Ricerca di Azure supporta la scrittura di documenti come batch. Un batch può contenere da 1 a 1000 azioni e un'azione gestisce un documento per eseguire l'operazione di caricamento/unione.

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
                "referenceName": "<Azure Search output dataset name>",
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

### <a name="data-type-support"></a>Supporto dei tipi di dati

La tabella seguente indica se un tipo di dati di Ricerca di Azure è supportato o meno.

| Tipo di dati di Ricerca di Azure | Supportato nel sink di Ricerca di Azure |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).