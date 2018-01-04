---
title: Copiare i dati dal/al Cloud di SAP per cliente tramite Data Factory di Azure | Documenti Microsoft
description: Informazioni su come copiare i dati dal Cloud di SAP per cliente per gli archivi dati sink supportati (o) dagli archivi dati di origine supportata a SAP Cloud per il cliente con Data Factory.
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
ms.openlocfilehash: ad3bc7ba38f5ea20586031bdcc3ae44f03f9da0b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copia i dati da SAP Cloud per cliente (C4C) con Data Factory di Azure

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare dati da/a Cloud SAP per cliente (C4C). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati dal Cloud SAP per cliente per qualsiasi archivio dati sink supportati o copiare i dati da qualsiasi archivio dati di origine supportata a Cloud SAP per cliente. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore consente Data Factory di Azure copiare i dati da/a Cloud SAP per cliente inclusi Cloud SAP per Sales, Cloud SAP per il servizio e il Cloud SAP per le soluzioni di Engagement sociale.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità di Data Factory specifica cloud SAP per il connettore di cliente.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per SAP Cloud al servizio clienti collegato:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **SapCloudForCustomer**. | Sì |
| URL | L'URL del servizio OData C4C SAP. | Sì |
| username | Specificare il nome utente a cui connettersi C4C di SAP. | Sì |
| password | Specificare la password per l'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink |

>[!IMPORTANT]
>Per copiare i dati nel Cloud di SAP per cliente, in modo esplicito [creare un IR Azure](create-azure-integration-runtime.md#create-azure-ir) con una posizione accanto al Cloud di SAP per il cliente e associare nel servizio collegato come nell'esempio seguente:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal Cloud SAP per set di dati cliente.

Per copiare dati dal Cloud SAP per cliente, impostare la proprietà del tipo di set di dati da **SapCloudForCustomerResource**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo del set di dati deve essere impostata su: **SapCloudForCustomerResource** |Sì |
| path | Il percorso dell'entità OData C4C SAP. |Sì |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco delle proprietà supportate dal Cloud SAP per l'origine di cliente.

### <a name="sap-c4c-as-source"></a>C4C SAP come origine

Per copiare dati dal Cloud SAP per cliente, impostare il tipo di origine in attività di copia per **SapCloudForCustomerSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **SapCloudForCustomerSource**  | Sì |
| query | Specificare la query OData personalizzata per leggere i dati. | No  |

Esempio di query per ottenere dati per un giorno specifico:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

Per copiare dati Cloud SAP per cliente, impostare l'attività di copia per il tipo di sink **SapCloudForCustomerSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **SapCloudForCustomerSink**  | Sì |
| writebehavior | Comportamento dell'azione di scrittura dell'operazione. Potrebbe essere "Insert", "Update". | di serie Valore predefinito "Insert". |
| writeBatchSize | Le dimensioni del batch dell'operazione di scrittura. Le dimensioni di batch per ottenere prestazioni ottimali potrebbero essere diversa per una tabella diversa o server. | di serie Valore predefinito 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapping dei tipi di dati per il cliente per SAP Cloud

Quando si copiano dati da SAP Cloud per cliente, i mapping seguenti vengono utilizzati per i tipi di dati dal Cloud di SAP a tipi di dati provvisori Data Factory di Azure. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati OData C4C SAP | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleano |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | Intervallo di tempo |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
