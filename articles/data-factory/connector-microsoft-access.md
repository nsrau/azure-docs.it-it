---
title: Copiare dati da origini di Microsoft Access
description: Informazioni su come copiare i dati dalle origini di Microsoft Access agli archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.Learn how to copy data from Microsoft Access sources to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: d6110065e28b8f179cd1d113107fb3508e1c3e44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892537"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Copiare dati da e negli archivi dati di Microsoft Access usando Azure Data FactoryCopy data from and to Microsoft Access data stores using Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un archivio dati di Microsoft Access.This article outlines how to use the Copy Activity in Azure Data Factory to copy data from a Microsoft Access data store. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Microsoft Access è supportato per le seguenti attività:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare i dati dall'origine di Microsoft Access in qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare questo connettore di Microsoft Access, è necessario:

- Configurare un runtime di integrazione self-hosted. Per informazioni dettagliate, vedere l'articolo [Self-hosted Integration Runtime.See Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article for details.
- Installare il driver ODBC di Microsoft Access per l'archivio dati nel computer Integration Runtime.

>[!NOTE]
>Microsoft Access 2016 versione del driver ODBC non funziona con questo connettore. Utilizzare invece la versione del driver 2013 o 2010.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà utilizzate per definire le entità di Data Factory specifiche del connettore di Microsoft Access.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Microsoft Access sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **MicrosoftAccess** | Sì |
| connectionString | Stringa di connessione ODBC esclusa la parte relativa alle credenziali. È possibile specificare la stringa di connessione o usare il DSN di sistema (Nome origine dati) impostato nel computer del runtime di integrazione (è comunque necessario specificare la parte delle credenziali nel servizio collegato di conseguenza).<br> È anche possibile inserire una password nell'insieme di credenziali delle chiavi di Azure ed estrarre la `password` configurazione dalla stringa di connessione.Fare riferimento a [Archiviare le credenziali nell'insieme di credenziali delle chiavi](store-credentials-in-key-vault.md) di Azure con altri dettagli.| Sì |
| authenticationType | Tipo di autenticazione utilizzata per connettersi all'archivio dati di Microsoft Access.<br/>I valori consentiti sono **Base** e **Anonimo**. | Sì |
| userName | Specificare il nome utente se si usa l'autenticazione di base. | No |
| password | Specificare la password per l'account utente specificato per userName. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| credenziali | La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver. Contrassegnare questo campo come SecureString. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati di Microsoft Access.

Per copiare dati da Microsoft Access, sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del dataset deve essere impostata su: **MicrosoftAccessTable** | Sì |
| tableName | Nome della tabella in Microsoft Access. | No per l'origine (se nell'origine dell'attività è specificato "query");<br/>sì per il sink |

**Esempio**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine di Microsoft Access.

### <a name="microsoft-access-as-source"></a>Microsoft Access come origine

Per copiare dati dall'archivio dati compatibile con Microsoft Access, nella sezione **dell'origine dell'attività** di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **MicrosoftAccessSource** | Sì |
| query | Usare la query personalizzata per leggere i dati. Ad esempio `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
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
