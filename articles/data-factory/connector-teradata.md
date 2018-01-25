---
title: Copiare dati da Teradata usando Azure Data Factory | Microsoft Docs
description: Informazioni sul connettore Teradata del servizio Data Factory, che consente di copiare dati da un database Teradata in archivi dati supportati da Data Factory come sink.
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 905a2bf1b42819a531bc4b16dd1e6f5539e80068
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copiare dati da Teradata usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-onprem-teradata-connector.md)
> * [Versione 2 - Anteprima](connector-teradata.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Teradata. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Teradata nella versione 1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Teradata in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Teradata supporta:

- Teradata **versione 12 e successive**.
- La copia di dati usando l'autenticazione **Di base** o **Windows**.

## <a name="prerequisites"></a>prerequisiti

Per usare questo connettore Teradata, è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare il [provider di dati .NET per Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) versione 14 o successiva nel computer del runtime di integrazione.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Teradata.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Teradata sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Teradata** | Sì |
| server | Nome del server Teradata. | Sì |
| authenticationType | Tipo di autenticazione usato per connettersi al database Teradata.<br/>I valori consentiti sono: **Di base** e **Windows**. | Sì |
| username | Specificare il nome utente per la connessione al database Teradata. | Sì |
| password | Specificare la password per l'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Teradata.

Per copiare dati da Teradata, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **RelationalTable** | Sì |
| tableName | Nome della tabella nel database Teradata. | No (se nell'origine dell'attività è specificato "query") |

**Esempio:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Teradata.

### <a name="teradata-as-source"></a>Teradata come origine

Per copiare dati da Teradata, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **RelationalSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Mapping dei tipi di dati per Teradata

Quando si copiano dati da Teradata, vengono usati i mapping seguenti tra i tipi di dati di Teradata e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Teradata | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| BLOB |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |string |
| Clob |string |
| Data |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |string |
| Integer |Int32 |
| Interval Day |Intervallo di tempo |
| Interval Day To Hour |Intervallo di tempo |
| Interval Day To Minute |Intervallo di tempo |
| Interval Day To Second |Intervallo di tempo |
| Interval Hour |Intervallo di tempo |
| Interval Hour To Minute |Intervallo di tempo |
| Intervallo - da ora a secondo |Intervallo di tempo |
| Interval Minute |Intervallo di tempo |
| Interval Minute To Second |Intervallo di tempo |
| Interval Month |string |
| Interval Second |Intervallo di tempo |
| Interval Year |string |
| Interval Year To Month |string |
| Number |Double |
| Period(Date) |string |
| Period(Time) |string |
| Period(Time With Time Zone) |string |
| Period(Timestamp) |string |
| Period(Timestamp With Time Zone) |string |
| SmallInt |Int16 |
| Tempo |Intervallo di tempo |
| Time With Time Zone |string |
| Timestamp |Datetime |
| Timestamp With Time Zone |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |string |
| VarGraphic |string |
| xml |string |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).