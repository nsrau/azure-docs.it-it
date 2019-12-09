---
title: Copiare dati da ServiceNow
description: Informazioni su come copiare dati da ServiceNow in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926161"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiare dati da ServiceNow usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in ServiceNow. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore ServiceNow è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da ServiceNow a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Inizia ora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore ServiceNow.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ServiceNow sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **ServiceNow**. | SÌ |
| endpoint | Endpoint del server ServiceNow (`http://<instance>.service-now.com`).  | SÌ |
| authenticationType | Tipo di autenticazione da usare. <br/>I valori consentiti sono **Basic**, **OAuth2** | SÌ |
| Nome utente | Nome utente usato per la connessione al server di ServiceNow per l'autenticazione di base e OAuth2.  | SÌ |
| password | Password corrispondente al nome utente per l'autenticazione di base e OAuth2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | SÌ |
| clientId | ID client per l'autenticazione OAuth2.  | No |
| clientSecret | Segreto client per l'autenticazione OAuth2. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No |
| useHostVerification | Specifica se è necessario che il nome host nel certificato del server corrisponda al nome host del server per la connessione tramite SSL. Il valore predefinito è true.  | No |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite SSL. Il valore predefinito è true.  | No |

**Esempio:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di ServiceNow.

Per copiare dati da ServiceNow, impostare la proprietà type del set di dati su **ServiceNowObject**. Sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **ServiceNowObject** | SÌ |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow come origine

Per copiare dati da ServiceNow, impostare il tipo di origine nell'attività di copia su **ServiceNowSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **ServiceNowSource** | SÌ |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Actual.alm_asset"`. | No (se nel set di dati è specificato "tableName") |

Tenere presente quanto segue quando si specifica lo schema e la colonna per ServiceNow nella query, e **fare riferimento ai [suggerimenti sulle prestazioni](#performance-tips) per le implicazioni della copia per le prestazioni**.

- **Schema:** specificare lo schema come `Actual` o `Display` nella query ServiceNow, che è possibile individuare come parametro di `sysparm_display_value` come true o false quando si chiamano le [API RESTful di ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Colonna:** il nome di colonna per il valore effettivo nello schema `Actual` è `[column name]_value`, mentre il valore visualizzato nello schema `Display` è `[column name]_display_value`. Si noti il nome della colonna deve essere mappato allo schema usato nella query.

**Query di esempio:** 
`SELECT col_value FROM Actual.alm_asset` O 
`SELECT col_display_value FROM Display.alm_asset`

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Suggerimenti per incrementare le prestazioni

### <a name="schema-to-use"></a>Schema da usare

ServiceNow ha 2 schemi diversi, ovvero **"Actual"** , che restituisce i dati effettivi, e **"Display"** che restituisce i valori visualizzati dei dati. 

Se la query include un filtro, usare lo schema "Actual" che offre prestazioni di copia migliori. Quando si eseguono query sullo schema "Actual", ServiceNow supporta in modo nativo un filtro durante il recupero di dati per restituire solo il set di risultati filtrato, mentre quando si esegue una query sullo schema "Display", Azure Data Factory recupera tutti i dati e applica il filtro internamente.

### <a name="index"></a>Indice

L'indice di tabella di ServiceNow può essere utile per migliorare le prestazioni delle query. Vedere [Create a table index](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html) (Creare un indice di tabella).

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
