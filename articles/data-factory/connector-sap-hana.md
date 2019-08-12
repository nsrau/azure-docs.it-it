---
title: Copiare dati da SAP HANA usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da SAP HANA in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: e9b024fc3c07670201cf72cf80c0b69bf68f1cc8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725991"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiare dati da SAP HANA usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sap-hana-connector.md)
> * [Versione corrente](connector-sap-hana.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SAP HANA. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database SAP HANA in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP HANA supporta:

- La copia di dati da qualsiasi versione del database SAP HANA.
- Copia di dati da **modelli di informazioni Hana** (ad esempio, viste analitiche e di calcolo) e **tabelle righe/colonne**.
- La copia di dati usando l'autenticazione **Di base** o **Windows**.

> [!TIP]
> Per copiare dati **in** un archivio dati SAP HANA, usare il connettore ODBC generico. Per i dettagli, vedere [Sink SAP HANA](connector-odbc.md#sap-hana-sink). Si noti che i servizi collegati per i connettori SAP HANA e ODBC sono associati a tipi diversi e pertanto non possono essere riusati.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore SAP HANA, è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare il driver ODBC di SAP HANA nel computer del runtime di integrazione. È possibile scaricare il driver ODBC di SAP HANA dall'[area per il download di software SAP](https://support.sap.com/swdc). Per cercare il driver, usare la parola chiave **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP HANA.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP HANA sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapHana** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi al SAP HANA usando l' **autenticazione di base** o **l'autenticazione di Windows**. Vedere gli esempi seguenti.<br>In stringa di connessione, il server/porta è obbligatorio (la porta predefinita è 30015) e il nome utente e la password sono obbligatori quando si usa l'autenticazione di base. Per altre impostazioni avanzate, vedere [SAP Hana proprietà di connessione ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>È anche possibile inserire la password in Azure Key Vault ed estrarre la configurazione della password dalla stringa di connessione. Per informazioni dettagliate, vedere [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) articolo. | Sì |
| userName | Specificare il nome utente quando si utilizza l'autenticazione di Windows. Esempio: `user@domain.com` | No |
| password | Specifica la password per l'account utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Yes |

**Esempio: uso dell'autenticazione di base**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: uso dell'autenticazione di Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Se si usa SAP HANA servizio collegato con il payload seguente, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

**Esempio:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP HANA.

Per copiare dati da SAP HANA, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **SapHanaTable** | Sì |
| schema | Nome dello schema nel database SAP HANA. | No (se nell'origine dell'attività è specificato "query") |
| table | Nome della tabella nel database SAP HANA. | No (se nell'origine dell'attività è specificato "query") |

**Esempio:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se si usa `RelationalTable` un set di dati tipizzato, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA come origine

Per copiare dati da SAP HANA, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **SapHanaSource** | Sì |
| query | Specifica la query SQL che consente di leggere i dati dall'istanza di SAP HANA. | Sì |
| packetSize | Specifica le dimensioni del pacchetto di rete (in kilobyte) per suddividere i dati in più blocchi. Se è presente una grande quantità di dati da copiare, l'aumento delle dimensioni del pacchetto può aumentare la velocità di lettura da SAP HANA nella maggior parte dei casi. Il test delle prestazioni è consigliato per la regolazione delle dimensioni del pacchetto. | No.<br>Il valore predefinito è 2048 (2MB). |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se si usa `RelationalSource` l'origine di copia tipizzata, questo è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="data-type-mapping-for-sap-hana"></a>Mapping dei tipi di dati per SAP HANA

Quando si copiano dati da SAP HANA, vengono usati i mapping seguenti tra i tipi di dati di SAP HANA e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SAP HANA | Tipo di dati provvisori di Data Factory |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
