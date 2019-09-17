---
title: Copiare dati da e verso il database di Azure per PostgreSQL usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da e verso il database di Azure per PostgreSQL usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: d6b860f43abae2283b4889bff0913bac25c821f5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71017770"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-using-azure-data-factory"></a>Copiare dati da e verso il database di Azure per PostgreSQL usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati dal Database di Azure per PostgreSQL. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

Questo connettore è specializzato per il [servizio database di Azure per PostgreSQL](../postgresql/overview.md). Per copiare dati da un database PostgreSQL generico situato in locale o nel cloud, usare il [connettore PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database di Azure per PostgreSQL è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare i dati dal Database di Azure per PostgreSQL a un qualsiasi archivio dati sink supportato. In alternativa, è possibile copiare dati da qualsiasi archivio dati di origine supportato a database di Azure per PostgreSQL. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche in un connettore del Database di Azure per PostgreSQL.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Database di Azure per PostgreSQL sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzurePostgreSql** | Sì |
| connectionString | Stringa di connessione ODBC per la connessione al Database di Azure per PostgreSQL.<br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `password` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Una stringa di connessione tipica è `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Altre proprietà che è possibile impostare per il case:

| Proprietà | Descrizione | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Il metodo usato dal driver per crittografare i dati inviati tra il driver e il server di database. Ad esempio,`EncryptionMethod=<0/1/6>;`| 0 (Nessuna crittografia) **(impostazione predefinita)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Determina se il driver convalida il certificato inviato dal server di database quando è abilitata la crittografia SSL (metodo di crittografia = 1). Ad esempio,`ValidateServerCertificate=<0/1>;`| 0 (disabilitato) **(impostazione predefinita)** / 1 (abilitato) | No |

**Esempio:**

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

**Esempio: archiviare la password in Azure Key Vault**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati del Database di Azure per PostgreSQL.

Per copiare dati dal Database di Azure per PostgreSQL, impostare la proprietà type del set di dati su **AzurePostgreSqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AzurePostgreSqlTable** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine del Database di Azure per PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Database di Azure per PostgreSQL come origine

Per copiare i dati da un Database di Azure per PostgreSQL, impostare il tipo di origine nell'attività di copia su **AzurePostgreSqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **AzurePostgreSqlSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

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

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **AzurePostgreSQLSink** | Yes |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database di Azure per PostgreSQL a ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |
| writeBatchSize | Inserisce i dati nella tabella di database di Azure per PostgreSQL quando la dimensione del buffer raggiunge writeBatchSize.<br>Il valore consentito è Integer che rappresenta il numero di righe. | No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br>I valori consentiti sono un intervallo di tempo. Ad esempio "00:30:00" (30 minuti). | No (il valore predefinito è 00:00:30) |

**Esempio:**

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

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
