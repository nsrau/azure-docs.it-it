---
title: Copiare dati da/in SAP Cloud for Customer usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da SAP Cloud for Customer in archivi dati sink supportati o da archivi dati di origine supportati in SAP Cloud for Customer usando Data Factory.
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: e580c3f36ce19679d3edcf7a8861e4e492dfa9c5
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copiare dati da SAP Cloud for Customer (C4C) usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da/in SAP Cloud for Customer (C4C). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da SAP Cloud for Customer in qualsiasi archivio dati sink supportato o da qualsiasi archivio dati di origine supportato in SAP Cloud for Customer. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore consente ad Azure Data Factory di copiare dati da/in SAP Cloud for Customer incluse le soluzioni SAP Cloud for Sales, SAP Cloud for Service e SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP Cloud for Customer sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapCloudForCustomer**. | Sì |
| url | URL del servizio SAP C4C OData. | Sì |
| username | Specificare il nome utente per la connessione a SAP C4C. | Sì |
| password | Specificare la password dell'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink |

>[!IMPORTANT]
>Per copiare i dati in SAP Cloud for Customer, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con una posizione prossima a SAP Cloud for Customer e associarlo al servizio collegato come illustrato nell'esempio seguente.

**Esempio:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP Cloud for Customer.

Per copiare dati da SAP Cloud for Customer, impostare la proprietà type del set di dati su **SapCloudForCustomerResource**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **SapCloudForCustomerResource**. |Sì |
| path | Specificare il percorso dell'entità SAP C4C OData. |Sì |

**Esempio:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C come origine

Per copiare dati da SAP Cloud for Customer, impostare il tipo di origine nell'attività di copia su **SapCloudForCustomerSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapCloudForCustomerSource**.  | Sì |
| query | Specificare la query OData personalizzata per leggere i dati. | No |

Query di esempio per ottenere dati relativi a un giorno specifico:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SAPC4CSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C come sink

Per copiare dati in SAP Cloud for Customer, impostare il tipo di sink nell'attività di copia su **SapCloudForCustomerSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapCloudForCustomerSink**.  | Sì |
| writebehavior | Comportamento dell'azione di scrittura dell'operazione. Può essere "Insert", "Update". | No. Il valore predefinito è "Insert". |
| writeBatchSize | Dimensioni batch dell'operazione di scrittura. Le dimensioni batch per ottenere prestazioni ottimali possono essere diverse per tabelle o server differenti. | No. Il valore predefinito è 10. |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapping del tipo di dati per SAP Cloud for Customer

Quando si copiano dati da SAP Cloud for Customer, vengono usati i mapping seguenti tra i tipi di dati di SAP Cloud for Customer e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SAP C4C OData | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
