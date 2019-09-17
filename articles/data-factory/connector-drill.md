---
title: Copiare dati da Drill tramite Azure Data Factory (anteprima) | Microsoft Docs
description: Informazioni su come copiare dati da Drill in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: b502823ea19cd8bbdab73c78fa3afdab2267940e
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009421"
---
# <a name="copy-data-from-drill-using-azure-data-factory-preview"></a>Copiare dati da Drill tramite Azure Data Factory (anteprima)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Drill. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in anteprima. È possibile provarlo e inviare commenti e suggerimenti. Se si vuole accettare una dipendenza dai connettori in versione di anteprima nella propria soluzione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di drill è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Drill a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Drill.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Drill sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Drill**. | Sì |
| connectionString | Stringa di connessione ODBC per la connessione a Drill. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `pwd` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "DrillLinkedService",
    "properties": {
        "type": "Drill",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "ConnectionType=Direct;Host=<host>;Port=<port>;AuthenticationType=Plain;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la password in Azure Key Vault**

```json
{
    "name": "DrillLinkedService",
    "properties": {
        "type": "Drill",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "ConnectionType=Direct;Host=<host>;Port=<port>;AuthenticationType=Plain;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Drill.

Per copiare dati da Drill, impostare la proprietà type del set di dati su **DrillTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **DrillTable** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| table | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Usare `schema` e`table` per il nuovo carico di lavoro. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "DrillDataset",
    "properties": {
        "type": "DrillTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Drill linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Drill.

### <a name="drillsource-as-source"></a>DrillSource come origine

Per copiare dati da Drill, impostare il tipo di origine nell'attività di copia su **DrillSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **DrillSource** | Yes |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDrill",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Drill input dataset name>",
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
                "type": "DrillSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
