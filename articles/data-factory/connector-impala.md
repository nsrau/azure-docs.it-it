---
title: Copiare dati da Impala tramite Azure Data Factory (anteprima) | Microsoft Docs
description: Informazioni su come copiare dati da Impala in archivi dati di sink supportati usando un'attività di copia in una pipeline di data factory.
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
ms.openlocfilehash: 4bebdbda8fbba10b3e8817d3958e75d39522538a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092045"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-preview"></a>Copiare dati da Impala tramite Azure Data Factory (anteprima)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Impala. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in anteprima. È possibile provarlo e inviare commenti e suggerimenti. Se si vuole accettare una dipendenza dai connettori in versione di anteprima nella propria soluzione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Impala è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Impala in qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Data Factory fornisce un driver predefinito per abilitare la connettività. Non è pertanto necessario installare manualmente un driver per usare questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore per Impala.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Impala sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Impala**. | Sì |
| host | Indirizzo IP o nome host del server Impala (ovvero, 192.168.222.160).  | Yes |
| port | Porta TCP che il server Impala usa per l'ascolto delle connessioni client. Il valore predefinito è 21050.  | No |
| authenticationType | Tipo di autenticazione da usare. <br/>I valori consentiti sono **Anonymous**, **SASLUsername** e **UsernameAndPassword**. | Yes |
| userName | Nome utente usato per accedere al server Impala. Quando si usa SASLUsername, il valore predefinito è Anonymous.  | No |
| password | La password che corrisponde al nome utente quando si usa UsernameAndPassword. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Specifica se le connessioni al server sono crittografate tramite SSL. Il valore predefinito è **false**.  | No |
| trustedCertPath | Percorso completo del file PEM contenente i certificati CA attendibili usati per verificare il server in caso di connessione tramite SSL. Questa proprietà può essere impostata solo quando si usa SSL nel runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato della CA dall'archivio di scopi consentiti o da un file .pem specificato. Il valore predefinito è **false**.  | No |
| allowHostNameCNMismatch | Specifica se è necessario che il nome del certificato SSL rilasciato dall'Autorità di certificazione corrisponda al nome host del server per la connessione tramite SSL. Il valore predefinito è **false**.  | No |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è **false**.  | No |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Impala.

Per copiare dati da Impala, impostare la proprietà type del set di dati su **ImpalaObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **ImpalaObject** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| table | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Usare `schema` e`table` per il nuovo carico di lavoro. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dal tipo di origine Impala.

### <a name="impala-as-a-source-type"></a>Impala come tipo di origine

Per copiare dati da Impala, impostare il tipo di origine nell'attività di copia su **ImpalaSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **ImpalaSource**. | Yes |
| query | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
