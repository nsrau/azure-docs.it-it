---
title: Copiare dati da e in IBM Informix usando Azure Data Factory
description: Informazioni su come copiare dati da e in IBM Informix usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: jingwang
ms.openlocfilehash: 93f484bd30de1ba0ca0f7aa5db263243bebc5b09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85508810"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Copiare dati da e in IBM Informix usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un archivio dati IBM Informix. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Informix è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare i dati dall'origine Informix in qualsiasi archivio dati di sink supportato o da qualsiasi archivio dati di origine supportato a sink Informix. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore Informix, è necessario:

- Configurare un runtime di integrazione self-hosted. Per informazioni dettagliate, vedere l'articolo relativo alla [Integration Runtime self-hosted](create-self-hosted-integration-runtime.md) .
- Installare il driver ODBC Informix per l'archivio dati nel computer Integration Runtime. È ad esempio possibile utilizzare il driver "IBM INFORMIX Informix DRIVER (64-bit)".

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche per il connettore Informix.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Informix sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su: **Informix** | Sì |
| connectionString | Stringa di connessione ODBC che esclude la parte delle credenziali. È possibile specificare la stringa di connessione o usare il DSN di sistema (nome dell'origine dati) configurato nel computer Integration Runtime (è necessario comunque specificare la parte delle credenziali nel servizio collegato di conseguenza). <br> È anche possibile inserire una password in Azure Key Vault ed estrarre la  `password`   configurazione dalla stringa di connessione. Per informazioni dettagliate, vedere [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md)   .| Sì |
| authenticationType | Tipo di autenticazione usato per la connessione all'archivio dati Informix.<br/>I valori consentiti sono **Base** e **Anonimo**. | Sì |
| userName | Specificare il nome utente se si usa l'autenticazione di base. | No |
| password | Specificare la password per l'account utente specificato per userName. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| credenziali | La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver. Contrassegnare questo campo come SecureString. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Informix.

Per copiare dati da Informix, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **InformixTable** | Sì |
| tableName | Nome della tabella in Informix. | No per l'origine (se nell'origine dell'attività è specificato "query");<br/>sì per il sink |

**Esempio**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Informix.

### <a name="informix-as-source"></a>Informix come origine

Per copiare dati da Informix, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su: **InformixSource** | Sì |
| query | Usare la query personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix come sink

Per copiare dati in Informix, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su: **InformixSink** | Sì |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono: intervallo di tempo.  Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize.<br/>I valori consentiti sono integer, ovvero il numero di righe. |No (l'impostazione predefinita è 0 - rilevamento automatico) |
| preCopyScript |Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nell'archivio dati in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. |No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
