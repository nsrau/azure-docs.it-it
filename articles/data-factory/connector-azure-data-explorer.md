---
title: Copiare dati da o verso Azure Esplora dati
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
ms.date: 11/26/2019
ms.openlocfilehash: 4cc315b91b5dbedcb22091149ca37061ff956efa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913418"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Copiare dati da o verso Azure Esplora dati usando Azure Data Factory

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da o verso [Esplora dati di Azure](../data-explorer/data-explorer-overview.md). Si basa sull'articolo [Panoramica dell'attività di copia](copy-activity-overview.md) , che offre una panoramica generale dell'attività di copia.

>[!TIP]
>Per Azure Data Factory e l'integrazione con Esplora dati di Azure in generale, Scopri di più su come [integrare azure Esplora dati con Azure Data Factory](../data-explorer/data-factory-integration.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Azure Esplora dati è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Esplora dati di Azure. È anche possibile copiare dati da Esplora dati di Azure in un qualsiasi archivio dati sink supportato. Per un elenco di archivi dati supportati dall'attività di copia come origini o sink, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) .

>[!NOTE]
>La copia dei dati in o da Esplora dati di Azure tramite un archivio dati locale tramite il runtime di integrazione self-hosted è supportata nella versione 3,14 e successive.

Con il connettore Azure Esplora dati è possibile eseguire le operazioni seguenti:

* Copia di dati tramite autenticazione token dell'applicazione Azure Active Directory (Azure AD) con **entità servizio**.
* Come origine, recupero di dati tramite una query KQL (Kusto).
* Come sink, accodamento di dati a una tabella di destinazione.

## <a name="getting-started"></a>Inizia ora

>[!TIP]
>Per una procedura dettagliata di Azure Esplora dati Connector, vedere Copiare dati da e verso [azure Esplora dati usando Azure Data Factory](../data-explorer/data-factory-load-data.md) e [la copia bulk da un database in Azure Esplora dati](../data-explorer/data-factory-template.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore di Esplora dati di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Esplora dati usa l'autenticazione basata su entità servizio. Per ottenere un'entità servizio e concedere le autorizzazioni, attenersi alla procedura seguente:

1. Registrare un'entità applicazione in Azure Active Directory attenendosi alla procedura descritta in [registrare l'applicazione con un tenant di Azure ad](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio le autorizzazioni corrette in Azure Esplora dati. Per informazioni dettagliate sui ruoli e sulle autorizzazioni e sulla gestione delle autorizzazioni, vedere gestire le autorizzazioni del [database di Azure Esplora dati](../data-explorer/manage-database-permissions.md) . In generale, è necessario:

    - **Come origine**, concedere almeno il ruolo **Visualizzatore database** al database
    - **Come sink**, concedere almeno il ruolo di inserimento del **database** al database

>[!NOTE]
>Quando si usa l'interfaccia utente di Data Factory per creare, l'account utente di accesso viene usato per elencare i cluster, i database e le tabelle di Azure Esplora dati. Immettere manualmente il nome se non si dispone dell'autorizzazione per queste operazioni.

Per il servizio collegato di Azure Esplora dati sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AzureDataExplorer**. | SÌ |
| endpoint | URL dell'endpoint del cluster di Esplora dati di Azure con il formato `https://<clusterName>.<regionName>.kusto.windows.net`. | SÌ |
| database | Nome del database. | SÌ |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Questa operazione è nota come "ID autorità" nella [stringa di connessione kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recuperarlo posizionando il puntatore del mouse nell'angolo superiore destro del portale di Azure. | SÌ |
| servicePrincipalId | Specificare l'ID client dell'applicazione. Questa operazione è nota come "ID client dell'applicazione AAD" nella [stringa di connessione kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | SÌ |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Questa operazione è nota come "chiave dell'applicazione AAD" nella [stringa di connessione kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in data factory o [fare riferimento a dati protetti archiviati nel Azure Key Vault](store-credentials-in-key-vault.md). | SÌ |

**Esempio di proprietà del servizio collegato:**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [DataSets in Azure Data Factory](concepts-datasets-linked-services.md). Questa sezione elenca le proprietà supportate dal set di dati di Azure Esplora dati.

Per copiare dati in Esplora dati di Azure, impostare la proprietà type del set di dati su **AzureDataExplorerTable**.

Sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** deve essere impostata su **AzureDataExplorerTable**. | SÌ |
| table | Nome della tabella a cui fa riferimento il servizio collegato. | Sì per il sink, no per l'origine |

**Esempio di proprietà del set di dati:**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate da Azure Esplora dati origini e sink.

### <a name="azure-data-explorer-as-source"></a>Esplora dati di Azure come origine

Per copiare dati da Esplora dati di Azure, impostare la proprietà **type** nell'origine dell'attività di copia su **AzureDataExplorerSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su: **AzureDataExplorerSource** | SÌ |
| query | Richiesta di sola lettura in [formato KQL](/azure/kusto/query/). Usare la query KQL personalizzata come riferimento. | SÌ |
| queryTimeout | Tempo di attesa prima del timeout della richiesta di query. Il valore predefinito è 10 min (00:10:00); il valore massimo consentito è 1 ora (01:00:00). | No |
| notroncamento | Indica se troncare il set di risultati restituito. Per impostazione predefinita, il risultato viene troncato dopo 500.000 record o 64 megabyte (MB). Il troncamento è fortemente consigliato per garantire il comportamento corretto dell'attività. |No |

>[!NOTE]
>Per impostazione predefinita, Azure Esplora dati Source ha un limite di dimensioni pari a 500.000 record o 64 MB. Per recuperare tutti i record senza troncamento, è possibile specificare `set notruncation;` all'inizio della query. Per altre informazioni, vedere [limiti delle query](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

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

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del sink dell'attività di copia deve essere impostata su: **AzureDataExplorerSink**. | SÌ |
| ingestionMappingName | Nome di un [mapping](/azure/kusto/management/mappings#csv-mapping) creato in precedenza in una tabella kusto. Per eseguire il mapping delle colonne dall'origine ad Azure Esplora dati (che si applica a [tutti i formati e archivi di origine supportati](copy-activity-overview.md#supported-data-stores-and-formats), inclusi i formati CSV/JSON/Avro), è possibile usare il [mapping delle colonne](copy-activity-schema-and-type-mapping.md) dell'attività di copia (in modo implicito in base al nome o in modo esplicito come configurato) e/o i mapping di Esplora dati di Azure. | No |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per ulteriori informazioni sulle proprietà, vedere [attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco di archivi dati supportati dall'attività di copia in Azure Data Factory come origini e sink, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

* Altre informazioni su come [copiare dati da Azure Data Factory in Esplora dati di Azure](/azure/data-explorer/data-factory-load-data).
