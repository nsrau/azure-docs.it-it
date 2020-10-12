---
title: Copiare i dati da DB2 mediante Azure Data Factory
description: Informazioni su come copiare dati da DB2 in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 3c65ed7e5fa6bb1652791eee75d4caa4c9c5f1ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83873644"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiare dati da DB2 usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-onprem-db2-connector.md)
> * [Versione corrente](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database DB2. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database DB2 è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da un database DB2 in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore DB2 supporta le piattaforme e le versioni di IBM DB2 seguenti con DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versione 9, 10 e 11.  Usa il protocollo DDM/DRDA.

* IBM DB2 per z/OS 12.1
* IBM DB2 per z/OS 11.1
* IBM DB2 per z/OS 10.1
* IBM DB2 per i 7.3
* IBM DB2 per i 7.2
* IBM DB2 per i 7.1
* IBM DB2 per LUW 11
* IBM DB2 per LUW 10.5
* IBM DB2 per LUW 10.1

>[!TIP]
>Il connettore DB2 si basa sul provider Microsoft OLE DB per DB2. Per risolvere i problemi relativi agli errori del connettore DB2, vedere [Codici errore del provider di dati](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Il runtime di integrazione offre un driver per DB2 integrato e non è quindi necessario installare manualmente alcun driver quando si copiano dati da/in DB2.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore DB2.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di DB2 sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Db2** | Sì |
| connectionString | Specificare le informazioni necessarie per la connessione all'istanza di DB2.<br/> È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `password` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Proprietà tipiche all'interno della stringa di connessione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| server |Nome del server DB2. È possibile specificare il numero di porta dopo il nome del server delimitato da due punti, ad esempio `server:port`.<br>Il connettore DB2 usa il protocollo DDM/DRDA e, per impostazione predefinita, usa la porta 50000, se non diversamente specificato. La porta usata dal database DB2 specifico potrebbe essere diversa in base alla versione e alle impostazioni, ad esempio per DB2 LUW la porta predefinita è 50000, per AS400 la porta predefinita è 446 o 448 con TLS abilitato. Per il modo in cui la porta è configurata in genere, fare riferimento ai seguenti documenti DB2: [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm) e [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Sì |
| database |Nome del database DB2. |Sì |
| authenticationType |Tipo di autenticazione usato per connettersi al database DB2.<br/>Il valore consentito è: **Basic**. |Sì |
| username |Specificare il nome utente per la connessione al database DB2. |Sì |
| password |Specificare la password per l'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| packageCollection | Specificare dove Azure Data Factory debba creare automaticamente i pacchetti necessari quando si eseguono query sul database. Se questo parametro non è impostato, Data Factory usa {username} come valore predefinito. | No |
| certificateCommonName | Quando si usa la crittografia SSL (Secure Sockets Layer) o TLS (Transport Layer Security), è necessario immettere un valore per il nome comune del certificato. | No |

> [!TIP]
> Se viene visualizzato un messaggio di errore che indica `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`, il motivo è che non è stato creato un pacchetto necessario per l'utente. Per impostazione predefinita, Azure Data Factory tenterà di creare un pacchetto in una raccolta denominata come l'utente usato per la connessione a DB2. Specificare la proprietà della raccolta pacchetti per indicare dove si desidera che Azure Data Factory crei i pacchetti necessari durante l'esecuzione di query sul database.

**Esempio:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
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
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
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

Il servizio collegato DB2 con il payload seguente è ancora supportato senza modifiche, ma è consigliato l'uso del nuovo per il futuro.

**Payload precedente:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati DB2.

Per copiare dati da DB2, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **Db2Table** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| tabella | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Il set di dati tipizzato `RelationalTable` è ancora supportato senza modifiche, ma è consigliato l'uso del nuovo per il futuro.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine DB2.

### <a name="db2-as-source"></a>DB2 come origine

Per copiare i dati da DB2, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **Db2Source** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

L'origine tipizzata `RelationalSource` è ancora supportata senza modifiche, ma è consigliato l'uso della nuova per il futuro.

## <a name="data-type-mapping-for-db2"></a>Mapping dei tipi di dati per DB2

Quando si copiano dati da DB2, vengono usati i mapping seguenti tra i tipi di dati di DB2 e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di database DB2 | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| BLOB |Byte[] |
| Char |string |
| Clob |string |
| Data |Datetime |
| DB2DynArray |string |
| DbClob |string |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |string |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |string |
| LongVarGraphic |string |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Tempo |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |string |
| VarGraphic |string |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
