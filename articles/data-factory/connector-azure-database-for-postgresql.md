---
title: Copiare dati da e verso il database di Azure per PostgreSQLCopy data to and from Azure Database for PostgreSQL
description: Informazioni su come copiare dati da e verso il database di Azure per PostgreSQL usando un'attività di copia in una pipeline di Azure Data Factory.Learn how to and from Azure Database for PostgreSQL by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410472"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiare dati da e verso il database di Azure per PostgreSQL usando Azure Data FactoryCopy data to and from Azure Database for PostgreSQL by using Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come usare la funzionalità di attività di copia in Azure Data Factory per copiare i dati dal database di Azure per PostgreSQL.This article describes how to use the copy activity feature in Azure Data Factory to copy data from Azure Database for PostgreSQL. Si basa [sull'articolo Attività di copia in Azure Data Factory,](copy-activity-overview.md) che presenta una panoramica generale dell'attività di copia.

Questo connettore è specializzato per il database di Azure per il [servizio PostgreSQL](../postgresql/overview.md). Per copiare dati da un database PostgreSQL generico che si trova in locale o nel cloud, utilizzare il [connettore PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Database di Azure per PostgreSQL è supportato per le attività seguenti:This Azure Database for PostgreSQL connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con una [matrice di origine/sink supportataCopy activity](copy-activity-overview.md) with a supported source/sink matrix
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare i dati dal Database di Azure per PostgreSQL a un qualsiasi archivio dati sink supportato. In alternativa, è possibile copiare i dati da qualsiasi archivio dati di origine supportato in Database di Azure per PostgreSQL.Or, you can copy data from any supported source data store to Azure Database for PostgreSQL. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere la tabella [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory fornisce un driver predefinito per abilitare la connettività. Pertanto, non è necessario installare manualmente alcun driver per utilizzare questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono dettagli sulle proprietà usate per definire le entità di Data Factory specifiche del database di Azure per il connettore PostgreSQL.The following sections offer details about properties that are used to define Data Factory entities specific to Azure Database for PostgreSQL connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per il servizio collegato Database di Azure per PostgreSQL:The following properties are supported for the Azure Database for PostgreSQL linked service:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzurePostgreSql**. | Sì |
| connectionString | Stringa di connessione ODBC per la connessione al Database di Azure per PostgreSQL.<br/>È anche possibile inserire una password nell'insieme di credenziali delle chiavi di Azure ed estrarre la `password` configurazione dalla stringa di connessione. Per altre informazioni, vedere gli esempi seguenti e [Archiviare le credenziali in Archiviazione archivio chiavi di Azure.See](store-credentials-in-key-vault.md) the following samples and Store credentials in Azure Key Vault for more details. | Sì |
| connectVia | Questa proprietà rappresenta il runtime di [integrazione](concepts-integration-runtime.md) da utilizzare per connettersi all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Una stringa di connessione tipica è `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Ecco altre proprietà che puoi impostare in base al tuo caso:

| Proprietà | Descrizione | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Il metodo usato dal driver per crittografare i dati inviati tra il driver e il server di database. Ad esempio, `EncryptionMethod=<0/1/6>;`| 0 (Nessuna crittografia) **(impostazione predefinita)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Determina se il driver convalida il certificato inviato dal server di database quando è attivata la crittografia SSL (Metodo di crittografia 1). Ad esempio, `ValidateServerCertificate=<0/1>;`| 0 (disabilitato) **(impostazione predefinita)** / 1 (abilitato) | No |

**Esempio**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Esempio**:

***Archiviare la password nell'insieme di credenziali delle chiavi di AzureStore password in Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere Set di dati [in Azure Data Factory.](concepts-datasets-linked-services.md) Questa sezione fornisce un elenco di proprietà supportate dal database di Azure per PostgreSQL nei set di dati.

Per copiare dati dal Database di Azure per PostgreSQL, impostare la proprietà type del set di dati su **AzurePostgreSqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzurePostgreSqlTable** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline e attività in Azure Data Factory.](concepts-pipelines-activities.md) Questa sezione fornisce un elenco delle proprietà supportate da un database di Azure per l'origine PostgreSQL.This section provides a list of properties supported by an Azure Database for PostgreSQL source.

### <a name="azure-database-for-postgresql-as-source"></a>Database di Azure per PostgreSQL come origine

Per copiare i dati da un Database di Azure per PostgreSQL, impostare il tipo di origine nell'attività di copia su **AzurePostgreSqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **AzurePostgreSqlSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"` | No (se è specificata la proprietà tableName nel dataset) |

**Esempio**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Database di Azure per PostgreSQL come sinkAzure Database for PostgreSQL as sink

Per copiare i dati nel database di Azure per PostgreSQL, nella sezione relativa al sink dell'attività di copia sono supportate le proprietà seguenti:To copy data to Azure Database for PostgreSQL, the following properties are supported in the copy activity **sink** section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **AzurePostgreSQLSink**. | Sì |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database di Azure per PostgreSQL in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |
| writeBatchSize | Inserisce dati nella tabella Database di Azure per PostgreSQL quando la dimensione del buffer raggiunge writeBatchSize.<br>Valore consentito è un numero intero che rappresenta il numero di righe. | No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br>I valori consentiti sono stringhe Timespan.Allowed values are Timespan strings. Ad esempio "00:30:00" (30 minuti). | No (il valore predefinito è 00:00:30) |

**Esempio**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per altre informazioni sulle proprietà, vedere Attività di ricerca in Azure Data Factory.For more information about the properties, [see Lookup activity in Azure Data Factory.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)
