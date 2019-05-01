---
title: Copiare dati da Netezza usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Netezza in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 9bf90c9d3ce593ba5bf6339cd9cec31bb49f14f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399928"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiare dati da Netezza usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Netezza. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Netezza a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornisce un driver predefinito per abilitare la connettività. Non è pertanto necessario installare manualmente un driver per usare questo connettore.

## <a name="get-started"></a>Attività iniziali

È possibile creare una pipeline che usa un'attività di copia tramite .NET SDK, Python SDK, Azure PowerShell, l'API REST o un modello di Azure Resource Manager. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md).

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per il connettore Netezza.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Netezza sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **Netezza**. | Sì |
| connectionString | Stringa di connessione ODBC per la connessione a Netezza. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `pwd` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile scegliere un runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No  |

Una stringa di connessione tipica è `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. La tabella seguente descrive altre proprietà che è possibile impostare:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| SecurityLevel | Il livello di sicurezza (SSL/TLS) che usa il driver per la connessione all'archivio dati. Esempio: `SecurityLevel=preferredSecured`. I valori supportati sono:<br/>- **Solo senza protezione** (**onlyUnSecured**): Il driver non utilizza SSL.<br/>- **Preferiti senza protezione (preferredUnSecured) (impostazione predefinita)**: Se il server fornisce una scelta, il driver non utilizza SSL. <br/>- **Preferiti con protezione (preferredSecured)**: Se il server fornisce una scelta, il driver utilizza SSL. <br/>- **Solo con protezione (onlySecured)**: Il driver non si connette a meno che non sia disponibile una connessione SSL. | No  |
| CaCertFile | Il percorso completo per il certificato SSL usato dal server. Esempio: `CaCertFile=<cert path>;`| Sì, se SSL è abilitato |

**Esempio**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
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

Questa sezione presenta un elenco delle proprietà supportate dal set di dati Netezza.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md).

Per copiare dati da Netezza, impostare la proprietà **type** del set di dati su **NetezzaTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **NetezzaTable** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine Netezza.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza come origine

Per copiare dati da Netezza, impostare il tipo di **origine** nell'attività di copia su **NetezzaSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **NetezzaSource**. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Esempio: `"SELECT * FROM MyTable"` | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
