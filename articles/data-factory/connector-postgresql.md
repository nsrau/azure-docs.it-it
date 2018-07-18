---
title: Copiare dati da PostgreSQL usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da PostgreSQL in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
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
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: 6279e088b8abd574bbd8ef6488d986d42c91123c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046055"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Copiare i dati da PostgreSQL mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Versione corrente](connector-postgresql.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database PostgreSQL. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database PostgreSQL in un qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore PostgreSQL supporta la **versione 7.4 e le versioni successive** di PostgreSQL.

## <a name="prerequisites"></a>prerequisiti

Se il database PostgreSQL non è accessibile pubblicamente, è necessario configurare un runtime di integrazione self-hosted. Per altre informazioni sui runtime di integrazione self-hosted, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md). Il runtime di integrazione offre un driver PostgreSQL predefinito a partire dalla versione 3.7 e non è quindi necessario installare manualmente alcun driver.

Per una versione del runtime di integrazione self-hosted precedente alla 3.7, è necessario installare il [provider di dati Ngpsql per PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) con una versione compresa tra la 2.0.12 e la 3.1.9 nel computer del runtime di integrazione.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore PostgreSQL.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di PostgreSQL sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **PostgreSql** | Sì |
| connectionString | Stringa di connessione ODBC per la connessione al Database di Azure per PostgreSQL. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

Una stringa di connessione tipica è `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Altre proprietà che è possibile impostare per il case:

| Proprietà | DESCRIZIONE | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Il metodo usato dal driver per crittografare i dati inviati tra il driver e il server di database. ad esempio `ValidateServerCertificate=<0/1/6>;`| 0 (Nessuna crittografia) **(impostazione predefinita)** / 1 (SSL) / 6 (RequestSSL) | No  |
| ValidateServerCertificate (VSC) | Determina se il driver convalida il certificato inviato dal server di database quando è abilitata la crittografia SSL (metodo di crittografia = 1). ad esempio `ValidateServerCertificate=<0/1>;`| 0 (disabilitato) **(impostazione predefinita)** / 1 (abilitato) | No  |

**Esempio:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Il servizio collegato PostgreSQL con il payload seguente è ancora supportato senza modifiche, ma è consigliato l'uso del nuovo per il futuro.

**Payload precedente:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati di PostgreSQL.

Per copiare dati da PostgreSQL, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **RelationalTable** | Sì |
| tableName | Nome della tabella nel database PostgreSQL. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL come origine

Per copiare dati da PostgreSQL, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **RelationalSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | No (se nel set di dati è specificato "tableName") |

> [!NOTE]
> I nomi di schemi e tabelle fanno distinzione tra maiuscole e minuscole. Racchiudere i nomi tra `""` (virgolette doppie) nella query.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-postgresql"></a>Mapping dei tipi di dati per PostgreSQL

Quando si copiano dati da PostgreSQL, vengono usati i mapping seguenti tra i tipi di dati PostgreSQL e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di PostgreSQL | Alias PostgresSQL | Tipo di dati provvisori di Data Factory |
|:--- |:--- |:--- |
| `abstime` |&nbsp; |`String` |
| `bigint` | `int8` | `Int64` |
| `bigserial` | `serial8` | `Int64` |
| `bit [1]` |&nbsp; | `Boolean` |
| `bit [(n)], n>1` |&nbsp; | `Byte[]` |
| `bit varying [(n)]` | `varbit` |`Byte[]` |
| `boolean` | `bool` | `Boolean` |
| `box` |&nbsp; | `String` |
| `bytea` |&nbsp; | `Byte[], String` |
| `character [(n)]` | `char [(n)]` | `String` |
| `character varying [(n)]` | `varchar [(n)]` | `String` |
| `cid` |&nbsp; | `Int32` |
| `cidr` |&nbsp; | `String` |
| `circle` |&nbsp; |` String` |
| `date` |&nbsp; |`Datetime` |
| `daterange` |&nbsp; |`String` |
| `double precision` |`float8` |`Double` |
| `inet` |&nbsp; |`String` |
| `intarray` |&nbsp; |`String` |
| `int4range` |&nbsp; |`String` |
| `int8range` |&nbsp; |`String` |
| `integer` | `int, int4` |`Int32` |
| `interval [fields] [(p)]` | | `String` |
| `json` |&nbsp; | `String` |
| `jsonb` |&nbsp; | `Byte[]` |
| `line` |&nbsp; | `Byte[], String` |
| `lseg` |&nbsp; | `String` |
| `macaddr` |&nbsp; | `String` |
| `money` |&nbsp; | `String` |
| `numeric [(p, s)]`|`decimal [(p, s)]` |`String` |
| `numrange` |&nbsp; |`String` |
| `oid` |&nbsp; |`Int32` |
| `path` |&nbsp; |`String` |
| `pg_lsn` |&nbsp; |`Int64` |
| `point` |&nbsp; |`String` |
| `polygon` |&nbsp; |`String` |
| `real` |`float4` |`Single` |
| `smallint` |`int2` |`Int16` |
| `smallserial` |`serial2` |`Int16` |
| `serial` |`serial4` |`Int32` |
| `text` |&nbsp; |`String` |
| `timewithtimezone` |&nbsp; |`String` |
| `timewithouttimezone` |&nbsp; |`String` |
| `timestampwithtimezone` |&nbsp; |`String` |
| `xid` |&nbsp; |`Int32` |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
