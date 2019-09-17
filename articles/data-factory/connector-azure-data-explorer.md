---
title: Copiare dati in o da Esplora dati di Azure usando Azure Data Factory
description: Informazioni su come copiare dati in o da Esplora dati di Azure usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010260"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copiare dati in o da Esplora dati di Azure usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati in o da [Esplora dati di Azure](../data-explorer/data-explorer-overview.md). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Azure Esplora dati è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da qualsiasi archivio dati di origine supportato in Esplora dati di Azure. È anche possibile copiare dati da Esplora dati di Azure in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md).

>[!NOTE]
>La copia dei dati da e verso l'archivio dati di Azure Esplora dati da/a quello locale usando la Integration Runtime self-hosted è supportata a partire dalla versione 3,14.

Il connettore di Esplora dati di Azure consente di eseguire le operazioni seguenti:

* Copia di dati tramite autenticazione token dell'applicazione Azure Active Directory (Azure AD) con **entità servizio**.
* Come origine, recupero di dati tramite una query KQL (Kusto).
* Come sink, accodamento di dati a una tabella di destinazione.

## <a name="getting-started"></a>Introduzione

>[!TIP]
>Per una procedura dettagliata sull'uso di Azure Esplora dati Connector, vedere [copiare dati da e verso azure Esplora dati con Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore di Esplora dati di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore Azure Esplora dati usa l'autenticazione basata su entità servizio. Per ottenere un'entità servizio e concedere le autorizzazioni, attenersi alla procedura seguente:

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo le indicazioni fornite in [Registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio l'autorizzazione appropriata in Esplora dati di Azure. Per informazioni dettagliate sui ruoli e sulle autorizzazioni, vedere gestire le autorizzazioni del [database di Azure Esplora dati](../data-explorer/manage-database-permissions.md) , oltre a esaminare la gestione delle autorizzazioni. In generale, è necessario

    - **Come origine**, concedere almeno il ruolo **Visualizzatore database** al database.
    - **Come sink**, concedere almeno il ruolo di inserimento del **database** al database.

>[!NOTE]
>Quando si usa l'interfaccia utente di ADF per creare, l'account utente di accesso viene usato per elencare i cluster, i database e le tabelle di Azure Esplora dati. Immettere manualmente il nome se non si è autorizzati a eseguire tale operazione.

Per il servizio collegato Esplora dati di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureDataExplorer** | Sì |
| endpoint | URL dell'endpoint del cluster di Esplora dati di Azure con il formato `https://<clusterName>.<regionName>.kusto.windows.net`. | Sì |
| database | Nome del database. | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Questo è ciò che normalmente si conosce come "**ID autorità**" nella [stringa di connessione kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recuperarle passando il puntatore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. Questo è ciò che normalmente si conosce come "**ID client dell'applicazione AAD**" nella [stringa di connessione kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Questo è ciò che normalmente si conosce come "**chiave dell'applicazione AAD**" nella [stringa di connessione kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Esplora dati di Azure.

Per copiare dati in Esplora dati di Azure, impostare la proprietà type del set di dati su **AzureDataExplorerTable**.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureDataExplorerTable** | Sì |
| table | Nome della tabella a cui fa riferimento il servizio collegato. | Sì per il sink, no per l'origine |

**Esempio di proprietà del set di dati**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Esplora dati di Azure.

### <a name="azure-data-explorer-as-source"></a>Esplora dati di Azure come origine

Per copiare dati da Esplora dati di Azure, impostare la proprietà **type** nell'origine dell'attività di copia su **AzureDataExplorerSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su: **AzureDataExplorerSource** | Sì |
| query | Richiesta di sola lettura in [formato KQL](/azure/kusto/query/). Usare la query KQL personalizzata come riferimento. | Sì |
| queryTimeout | Il tempo di attesa prima del timeout della richiesta di query. Il valore predefinito è 10 minuti (00:10:00); il valore massimo consentito è 1 ora (01:00:00). | No |
| notroncamento | Indica se troncare il set di risultati restituito. Per impostazione predefinita, il risultato viene troncato dopo 500.000 record o 64 MB. Il troncamento è fortemente consigliato per un comportamento appropriato dell'attività. |No |

>[!NOTE]
>Per impostazione predefinita, Azure Esplora dati Source ha un limite di dimensioni pari a 500.000 record o 64 MB. Per recuperare tutti i record senza troncamento, è possibile specificare `set notruncation;` all'inizio della query. Per ulteriori informazioni, fare riferimento ai [limiti delle query](https://docs.microsoft.com/azure/kusto/concepts/querylimits) .

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
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su: **AzureDataExplorerSink** | Sì |
| ingestionMappingName | Nome di un **[mapping](/azure/kusto/management/mappings#csv-mapping)** creato in precedenza in una tabella kusto. Per eseguire il mapping delle colonne dall'origine al Esplora dati di Azure, che si applica a **[tutti i formati/archivi di origine supportati](copy-activity-overview.md#supported-data-stores-and-formats)** , inclusi i formati CSV/JSON/Avro e così via, è possibile usare il [mapping](copy-activity-schema-and-type-mapping.md) delle colonne dell'attività di copia (in modo implicito per nome o in modo esplicito come configurato) e o mapping del Esplora dati di Azure. | No |

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

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

* Altre informazioni sulla [copia di dati da Azure Data Factory ad Azure Esplora dati](/azure/data-explorer/data-factory-load-data).