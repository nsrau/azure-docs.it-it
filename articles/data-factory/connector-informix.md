---
title: Copiare dati da origini IBM Informix usando Azure Data FactoryCopy data from IBM Informix sources using Azure Data Factory
description: Informazioni su come copiare i dati dalle origini IBM Informix agli archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.Learn how to copy data from IBM Informix sources to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b4fb6662491443db5d10825635cad8496e56e7f3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414968"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>Copiare dati da e in gli archivi dati IBM Informix usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare i dati da un archivio dati IBM Informix.This article outlines how to use the Copy Activity in Azure Data Factory to copy data from an IBM Informix data store. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Informix è supportato per le seguenti attività:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare i dati dall'origine Informix a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare questo connettore Informix, è necessario:

- Configurare un runtime di integrazione self-hosted. Per informazioni dettagliate, vedere l'articolo [Self-hosted Integration Runtime.See Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article for details.
- Installare il driver ODBC Informix per l'archivio dati nel computer Integration Runtime. Ad esempio, è possibile utilizzare il driver "IBM INFORMIX Informix DRIVER (64-bit)".

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà utilizzate per definire le entità di Data Factory specifiche del connettore Informix.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Informix sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Informix** | Sì |
| connectionString | Stringa di connessione ODBC esclusa la parte relativa alle credenziali. È possibile specificare la stringa di connessione o usare il DSN di sistema (Nome origine dati) impostato nel computer del runtime di integrazione (è comunque necessario specificare la parte delle credenziali nel servizio collegato di conseguenza). <br> È anche possibile inserire una password nell'insieme di credenziali delle chiavi di Azure ed estrarre la `password` configurazione dalla stringa di connessione. Fare riferimento a [Archiviare le credenziali nell'insieme di credenziali delle chiavi](store-credentials-in-key-vault.md) di Azure con altri dettagli.| Sì |
| authenticationType | Tipo di autenticazione utilizzata per connettersi all'archivio dati Diformix.<br/>I valori consentiti sono **Base** e **Anonimo**. | Sì |
| userName | Specificare il nome utente se si usa l'autenticazione di base. | No |
| password | Specificare la password per l'account utente specificato per userName. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati Informix.This section provides a list of properties supported by Informix dataset.

Per copiare i dati da Informix, sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **InformixTable** | Sì |
| tableName | Nome della tabella nell'Informix. | No per l'origine (se nell'origine dell'attività è specificato "query");<br/>sì per il sink |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione fornisce un elenco delle proprietà supportate dall'origine Informix.

### <a name="informix-as-source"></a>Informix come fonte

Per copiare i dati da Informix, nella sezione **dell'origine** dell'attività di copia sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **InformixSource** | Sì |
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

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
