---
title: Copiare dati da e verso il database di Azure per MySQL
description: Informazioni su come copiare dati da e verso il database di Azure per MySQL usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: cd3a069eb24b6390629cfaf9a4f19f47e8978d8a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931653"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Copiare dati da e verso il database di Azure per MySQL usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati dal database di Azure per MySQL. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

Questo connettore è specializzato per il [servizio database di Azure per MySQL](../mysql/overview.md). Per copiare dati da un database MySQL generico situato in locale o nel cloud, usare il [connettore MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database di Azure per MySQL è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare i dati dal database di Azure per MySQL a un qualsiasi archivio dati sink supportato. In alternativa, è possibile copiare dati da qualsiasi archivio dati di origine supportato a database di Azure per MySQL. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Inizia ora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di data factory specifiche in un connettore del database Azure per MySQL.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato del database di Azure per MySQL sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureMySql** | SÌ |
| connectionString | Specifica le informazioni necessarie per connettersi all'istanza del database di Azure per MySQL. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `password` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | SÌ |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non diversamente specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Una stringa di connessione tipica è `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Altre proprietà che è possibile impostare per il case:

| Proprietà | Description | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |
| SSLMode | Questa opzione specifica se il driver usa la crittografia SSL e verifica la connessione a MySQL. Ad esempio, `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(impostazione predefinita)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Questa opzione specifica se usare o meno un certificato CA dall'archivio di attendibilità di sistema o da un file PEM specificato. Ad esempio, `UseSystemTrustStore=<0/1>;`| Abilitato (1) / Disabilitato (0) **(impostazione predefinita)** | No |

**Esempio:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la password in Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati del database di Azure per MySQL.

Per copiare dati dal database di Azure per MySQL, impostare la proprietà type del set di dati su **AzureMySqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureMySqlTable** | SÌ |
| tableName | Nome della tabella nel database MySQL. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database di Azure per MySQL.

### <a name="azure-database-for-mysql-as-source"></a>Database di Azure per MySQL come origine

Per copiare dati da database di Azure per MySQL, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureMySqlSource** | SÌ |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |
| queryCommandTimeout | Tempo di attesa prima del timeout della richiesta di query. Il valore predefinito è 120 minuti (02:00:00) | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Database di Azure per MySQL come sink

Per copiare dati in database di Azure per MySQL, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su: **AzureMySqlSink** | SÌ |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database di Azure per MySQL in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |
| writeBatchSize | Inserisce i dati nella tabella di database di Azure per MySQL quando la dimensione del buffer raggiunge writeBatchSize.<br>Il valore consentito è Integer che rappresenta il numero di righe. | No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br>I valori consentiti sono un intervallo di tempo. Ad esempio "00:30:00" (30 minuti). | No (il valore predefinito è 00:00:30) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapping dei tipi di dati per il database di Azure per MySQL

Quando si copiano dati dal database di Azure per MySQL, vengono usati i mapping seguenti tra i tipi di dati MySQL e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati del database di Azure per MySQL | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
