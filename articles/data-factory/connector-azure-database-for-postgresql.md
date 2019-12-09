---
title: Copiare dati da e verso il database di Azure per PostgreSQL
description: Informazioni su come copiare dati da e verso il database di Azure per PostgreSQL usando un'attività di copia in una pipeline di Azure Data Factory.
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
ms.openlocfilehash: 3729d43716a0f4e133fb175da2546aac560b0525
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931624"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiare dati da e verso il database di Azure per PostgreSQL usando Azure Data Factory

Questo articolo descrive come usare la funzionalità di copia dell'attività in Azure Data Factory per copiare dati da database di Azure per PostgreSQL. Si basa sull' [attività di copia in Azure Data Factory](copy-activity-overview.md) articolo, che presenta una panoramica generale dell'attività di copia.

Questo connettore è specializzato per il [servizio database di Azure per PostgreSQL](../postgresql/overview.md). Per copiare dati da un database PostgreSQL generico situato in locale o nel cloud, usare il [connettore PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database di Azure per PostgreSQL è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con una [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare i dati dal Database di Azure per PostgreSQL a un qualsiasi archivio dati sink supportato. In alternativa, è possibile copiare dati da qualsiasi archivio dati di origine supportato a database di Azure per PostgreSQL. Per un elenco di archivi dati supportati dall'attività di copia come origini e sink, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory fornisce un driver predefinito per abilitare la connettività. Non è quindi necessario installare manualmente alcun driver per usare questo connettore.

## <a name="getting-started"></a>Inizia ora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche del connettore database di Azure per PostgreSQL.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato database di Azure per PostgreSQL sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su: **AzurePostgreSql**. | SÌ |
| connectionString | Stringa di connessione ODBC per la connessione al Database di Azure per PostgreSQL.<br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire una password in Azure Key Vault ed estrarre la configurazione `password` dalla stringa di connessione. Per ulteriori informazioni, vedere gli esempi seguenti e [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) . | SÌ |
| connectVia | Questa proprietà rappresenta il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non diversamente specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Una stringa di connessione tipica è `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Di seguito sono riportate altre proprietà che è possibile impostare in base al caso:

| Proprietà | Description | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Il metodo usato dal driver per crittografare i dati inviati tra il driver e il server di database. Ad esempio, `EncryptionMethod=<0/1/6>;`| 0 (Nessuna crittografia) **(impostazione predefinita)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Determina se il driver convalida il certificato inviato dal server di database quando è abilitata la crittografia SSL (metodo di crittografia = 1). Ad esempio, `ValidateServerCertificate=<0/1>;`| 0 (disabilitato) **(impostazione predefinita)** / 1 (abilitato) | No |

**Esempio**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**Esempio**:

***Archivia la password in Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [DataSets in Azure Data Factory](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate da database di Azure per PostgreSQL nei set di dati.

Per copiare dati dal Database di Azure per PostgreSQL, impostare la proprietà type del set di dati su **AzurePostgreSqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **AzurePostgreSqlTable** | SÌ |
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate da un'origine database di Azure per PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Database di Azure per PostgreSQL come origine

Per copiare i dati da un Database di Azure per PostgreSQL, impostare il tipo di origine nell'attività di copia su **AzurePostgreSqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AzurePostgreSqlSource** | SÌ |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"` | No (se è specificata la proprietà TableName nel set di dati) |

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

### <a name="azure-database-for-postgresql-as-sink"></a>Database di Azure per PostgreSQL come sink

Per copiare dati in database di Azure per PostgreSQL, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **AzurePostgreSQLSink**. | SÌ |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database di Azure per PostgreSQL a ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |
| writeBatchSize | Inserisce i dati nella tabella di database di Azure per PostgreSQL quando la dimensione del buffer raggiunge writeBatchSize.<br>Il valore consentito è un numero intero che rappresenta il numero di righe. | No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br>I valori consentiti sono stringhe TimeSpan. Ad esempio "00:30:00" (30 minuti). | No (il valore predefinito è 00:00:30) |

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

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per ulteriori informazioni sulle proprietà, vedere [attività Lookup in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
