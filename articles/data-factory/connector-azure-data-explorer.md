---
title: Copiare dati in o da Azure Data Explorer
description: Informazioni su come copiare dati in o da Esplora dati di Azure usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 12bf807f5866567508b644105f377cfde1494250
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410769"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Copiare dati in o da Azure Data Explorer tramite Azure Data FactoryCopy data to or from Azure Data Explorer by using Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati in o da [Azure Data Explorer.](/azure/data-explorer/data-explorer-overview) Si basa sull'articolo [panoramica dell'attività](copy-activity-overview.md) di copia, che offre una panoramica generale dell'attività di copia.

>[!TIP]
>Per l'integrazione generale di Azure Data Factory e Azure Data Explorer, vedere [Integrare Azure Data Explorer con Azure Data Factory.](/azure/data-explorer/data-factory-integration)

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Azure Data Explorer è supportato per le attività seguenti:This Azure Data Explorer connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Esplora dati di Azure. È anche possibile copiare dati da Esplora dati di Azure in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini o sink, vedere la tabella [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>La copia di dati da o verso Azure Data Explorer tramite un archivio dati locale tramite il runtime di integrazione self-hosted è supportata nella versione 3.14 e successive.

Con il connettore Azure Data Explorer è possibile eseguire le operazioni seguenti:With the Azure Data Explorer connector, you can do the following:

* Copia di dati tramite autenticazione token dell'applicazione Azure Active Directory (Azure AD) con **entità servizio**.
* Come origine, recupero di dati tramite una query KQL (Kusto).
* Come sink, accodamento di dati a una tabella di destinazione.

## <a name="getting-started"></a>Introduzione

>[!TIP]
>Per una procedura dettagliata del connettore di Azure Data Explorer, vedere [Copiare dati in/da Azure Data Explorer usando Azure Data Factory](/azure/data-explorer/data-factory-load-data) e Copia bulk da un database ad Azure Data Explorer.For a walkthrough of Azure Data Explorer connector, see Copy data to/from Azure Data Explorer using Azure Data Factory and [Bulk copy from a database to Azure Data Explorer.](/azure/data-explorer/data-factory-template)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore di Esplora dati di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore di Azure Data Explorer usa l'autenticazione dell'entità servizio. Seguire questi passaggi per ottenere un'entità servizio e concedere autorizzazioni:Follow these steps to get a service principal and to grant permissions:

1. Registrare un'entità dell'applicazione in Azure Active Directory seguendo la procedura descritta in [Registrare l'applicazione con un tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)di Azure AD. Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio le autorizzazioni corrette in Esplora dati di Azure.Grant the service principal the correct permissions in Azure Data Explorer. Per informazioni dettagliate su ruoli e autorizzazioni e sulla gestione delle autorizzazioni, vedere Gestire le autorizzazioni del database di [Azure Data Explorer.See Manage Azure Data Explorer database permissions](/azure/data-explorer/manage-database-permissions) for detailed information about roles and permissions and about managing permissions. In generale, è necessario:

    - **Come origine**, concedere almeno il ruolo **visualizzatore database** al database
    - **As sink**, concedere almeno il ruolo **Database in** gestore di database

>[!NOTE]
>Quando si usa l'interfaccia utente di Data Factory per l'autore, l'account utente di accesso viene usato per elencare cluster, database e tabelle di Azure Data Explorer.When you use the Data Factory UI to author, your login user account is used to list Azure Data Explorer clusters, databases, and tables. Immettere manualmente il nome se non si dispone dell'autorizzazione per queste operazioni.

Le proprietà seguenti sono supportate per il servizio collegato Azure Data Explorer:The following properties are supported for the Azure Data Explorer linked service:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureDataExplorer**. | Sì |
| endpoint | URL dell'endpoint del cluster di Esplora dati di Azure con il formato `https://<clusterName>.<regionName>.kusto.windows.net`. | Sì |
| database | Nome del database. | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Questa operazione è nota come "ID autorità" nella stringa di [connessione Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recuperarlo passando il puntatore del mouse nell'angolo superiore destro del portale di Azure.Retrieve it by hovering the mouse pointer in the upper-right corner of the Azure portal. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. Questo è noto come "ID client dell'applicazione AAD" nella stringa di [connessione Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Questa operazione è nota come "chiave dell'applicazione AAD" nella stringa di [connessione Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento ai dati protetti archiviati in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

**Esempio di proprietà del servizio collegato:Linked service properties example:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere Set di dati [in Azure Data Factory.](concepts-datasets-linked-services.md) Questa sezione elenca le proprietà supportate dal set di dati di Azure Data Explorer.This section lists properties that the Azure Data Explorer dataset supports.

Per copiare dati in Esplora dati di Azure, impostare la proprietà type del set di dati su **AzureDataExplorerTable**.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureDataExplorerTable**. | Sì |
| tabella | Nome della tabella a cui fa riferimento il servizio collegato. | Sì per il sink, no per l'origine |

**Esempio di proprietà del set di dati:Dataset properties example:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline e attività in Azure Data Factory.](concepts-pipelines-activities.md) Questa sezione fornisce un elenco di proprietà supportate dalle origini e nei sink di Azure Data Explorer.This section provides a list of properties that Azure Data Explorer sources and sinks support.

### <a name="azure-data-explorer-as-source"></a>Esplora dati di Azure come origine

Per copiare dati da Esplora dati di Azure, impostare la proprietà **type** nell'origine dell'attività di copia su **AzureDataExplorerSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su: **AzureDataExplorerSourceThe** type property of the copy activity source must be set to: AzureDataExplorerSource | Sì |
| query | Richiesta di sola lettura in [formato KQL](/azure/kusto/query/). Usare la query KQL personalizzata come riferimento. | Sì |
| queryTimeout | Tempo di attesa prima del timeout della richiesta di query. Il valore predefinito è 10 min (00:10:00); il valore massimo consentito è 1 ora (01:00:00). | No |
| noTruncation (noTruncation) | Indica se troncare il set di risultati restituito. Per impostazione predefinita, il risultato viene troncato dopo 500.000 record o 64 megabyte (MB). Il troncamento è fortemente consigliato per garantire il corretto comportamento dell'attività. |No |

>[!NOTE]
>Per impostazione predefinita, l'origine Di Azure Data Explorer ha un limite di dimensioni di 500.000 record o 64 MB. Per recuperare tutti i record senza troncamento, è possibile specificare `set notruncation;` all'inizio della query. Per ulteriori informazioni, vedere [Limiti delle query](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Esplora dati di Azure come sink

Per copiare dati in Esplora dati di Azure, impostare la proprietà type nel sink dell'attività di copia su **AzureDataExplorerSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su: **AzureDataExplorerSink**. | Sì |
| ingestionMappingName | Nome di una [mappatura](/azure/kusto/management/mappings#csv-mapping) creata in precedena in una tabella Kusto. Per eseguire il mapping delle colonne dall'origine ad Azure Data Explorer (che si applica a tutti gli archivi e formati di [origine supportati,](copy-activity-overview.md#supported-data-stores-and-formats)inclusi i formati CSV/JSON/Avro), è possibile usare il mapping delle [colonne](copy-activity-schema-and-type-mapping.md) dell'attività di copia (in modo implicito per nome o in modo esplicito come configurato) e/o mapping di Azure Data Explorer. | No |
| additionalProperties (proprietà aggiuntive) | Contenitore delle proprietà che può essere usato per specificare una delle proprietà di inserimento che non vengono già impostate dal sink di Azure Data Explorer.A property bag which can be used for specifying any of the ingestion properties which aren't being already set by the Azure Data Explorer Sink. In particolare, può essere utile per specificare i tag di inserimento. Per altre [informazioni, vedere Documento sull'inserimento dei dati di Azure Data Explore data ingestion](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html). | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per ulteriori informazioni sulle proprietà, vedere [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco degli archivi dati supportati dall'attività di copia in Azure Data Factory come origini e sink, vedere [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

* Altre informazioni su come [copiare dati da Azure Data Factory ad Azure Data Explorer.](/azure/data-explorer/data-factory-load-data)
