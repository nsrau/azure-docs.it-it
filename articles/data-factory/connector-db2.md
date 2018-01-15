---
title: Copiare dati da DB2 usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da DB2 in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 23bc0ba87abbac0f83e3e5ac9d1049bbf42707c9
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiare dati da DB2 usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-onprem-db2-connector.md)
> * [Versione 2 - Anteprima](connector-db2.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database DB2. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore DB2 nella versione 1](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database DB2 in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore DB2 supporta le piattaforme e le versioni di IBM DB2 seguenti con DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versione 9, 10 e 11:

* IBM DB2 per z/OS 11.1
* IBM DB2 per z/OS 10.1
* IBM DB2 per i 7.2
* IBM DB2 per i 7.1
* IBM DB2 per LUW 11
* IBM DB2 per LUW 10.5
* IBM DB2 per LUW 10.1

> [!TIP]
> Se si riceve un messaggio d'errore che indica che "Non è stato trovato il pacchetto corrispondente a una richiesta di esecuzione dell'istruzione SQL. SQLSTATE=51002 SQLCODE=805", il motivo è che non viene creato un pacchetto necessario per un utente normale in tale sistema operativo. Seguire le istruzioni seguenti in base al tipo di server DB2 in uso:
> - DB2 per i (AS400): consente all'utente esperto di creare una raccolta per l'utente di accesso prima di usare l'attività di copia. Comando: `create collection <username>`
> - DB2 per z/OS o LUW: usare un account con privilegi elevati (utente esperto o amministratore con autorità di pacchetto e autorizzazioni BIND, BINDADD, GRANT EXECUTE TO PUBLIC), per eseguire una volta l'attività di copia. Il pacchetto necessario viene quindi creato automaticamente durante la copia. In seguito è possibile tornare a essere un utente normale per le successive esecuzioni delle operazioni di copia.

## <a name="prerequisites"></a>Prerequisiti

Per copiare dati da un database DB2 non accessibile pubblicamente, è necessario configurare un runtime di integrazione self-hosted. Per altre informazioni sui runtime di integrazione self-hosted, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md). Il runtime di integrazione offre un driver per DB2 integrato e non è quindi necessario installare manualmente alcun driver quando si copiano dati da/in DB2.

## <a name="getting-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore DB2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di DB2 sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Db2** | Sì |
| server |Nome del server DB2. |Sì |
| database |Nome del database DB2. |Sì |
| schema |Nome dello schema nel database. Il nome dello schema fa distinzione tra maiuscole e minuscole. |No |
| authenticationType |Tipo di autenticazione usato per connettersi al database DB2.<br/>Il valore consentito è: **Di base**. |Sì |
| nome utente |Specificare il nome utente per la connessione al database DB2. |Sì |
| password |Specificare la password per l'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

**Esempio:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati DB2.

Per copiare dati da DB2, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **RelationalTable** | Sì |
| tableName | Nome della tabella nel database DB2. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine DB2.

### <a name="db2-as-source"></a>DB2 come origine

Per copiare dati da DB2, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **RelationalSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Mapping dei tipi di dati per DB2

Quando si copiano dati da DB2, vengono usati i mapping seguenti tra i tipi di dati di DB2 e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di database DB2 | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binario |Byte[] |
| BLOB |Byte[] |
| Char |Stringa |
| Clob |Stringa |
| Data |DateTime |
| DB2DynArray |Stringa |
| DbClob |Stringa |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| A due righe |A due righe |
| Float |A due righe |
| Graphic |Stringa |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Stringa |
| LongVarGraphic |Stringa |
| Numeric |Decimal |
| Real |Singola |
| SmallInt |Int16 |
| Ora |Intervallo di tempo |
| Timestamp |Data/Ora |
| VarBinary |Byte[] |
| VarChar |Stringa |
| VarGraphic |Stringa |
| xml |Byte[] |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).