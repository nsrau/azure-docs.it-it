---
title: Copiare dati in o da Esplora dati di Azure usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati in o da Esplora dati di Azure usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: 8f2a7a953ce2964645c281d9454a73b0cf1a8ff6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747189"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copiare dati in o da Esplora dati di Azure usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati in o da [Esplora dati di Azure](../data-explorer/data-explorer-overview.md). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Esplora dati di Azure. È anche possibile copiare dati da Esplora dati di Azure in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md).

>[!NOTE]
>La copia di dati da/a Esplora dati di Azure a/da un archivio dati locale tramite il runtime di integrazione self-hosted non è ancora supportata.

Il connettore di Esplora dati di Azure consente di eseguire le operazioni seguenti:

* Copia di dati tramite autenticazione token dell'applicazione Azure Active Directory (Azure AD) con **entità servizio**.
* Come origine, recupero di dati tramite una query KQL (Kusto).
* Come sink, accodamento di dati a una tabella di destinazione.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore di Esplora dati di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Esplora dati di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureDataExplorer** | Sì |
| endpoint | URL dell'endpoint del cluster di Esplora dati di Azure con il formato `https://<clusterName>.<regionName>.kusto.windows.net `. | Sì |
| database | Nome del database. | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarle passando il puntatore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

**Esempio di proprietà del servizio collegato:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Esplora dati di Azure.

Per copiare dati in Esplora dati di Azure, impostare la proprietà type del set di dati su **AzureDataExplorerTable**.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureDataExplorerTable** | Sì |
| tabella | Nome della tabella a cui fa riferimento il servizio collegato. | Sì per il sink, no per l'origine |

**Esempio di proprietà del set di dati**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Esplora dati di Azure.

### <a name="azure-data-explorer-as-source"></a>Esplora dati di Azure come origine

Per copiare dati da Esplora dati di Azure, impostare la proprietà **type** nell'origine dell'attività di copia su **AzureDataExplorerSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su: **AzureDataExplorerSource** | Sì |
| query | Richiesta di sola lettura in [formato KQL](/azure/kusto/query/). Usare la query KQL personalizzata come riferimento. | Sì |
| queryTimeout | Il tempo di attesa prima del timeout della richiesta di query. Il valore predefinito è 10 minuti (00:10:00); il valore massimo consentito è 1 ora (01:00:00). | No  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Esplora dati di Azure come sink

Per copiare dati in Esplora dati di Azure, impostare la proprietà type nel sink dell'attività di copia su **AzureDataExplorerSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su: **AzureDataExplorerSink** | Sì |
| ingestionMappingName | Nome di un [mapping csv](/azure/kusto/management/mappings#csv-mapping) creato in precedenza in una tabella Kusto. Per mappare le colonne dall'origine a Esplora dati di Azure, è anche possibile usare il [mapping di colonne](copy-activity-schema-and-type-mapping.md) dell'attività di copia. | No  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).