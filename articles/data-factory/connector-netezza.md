---
title: Copiare dati da Netezza usando Azure Data Factory
description: Informazioni su come copiare dati da Netezza in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 738c0cf8c9fea61bedb53aa5f6c9bde089bac5f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930060"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiare dati da Netezza usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Netezza. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

>[!TIP]
>Per lo scenario di migrazione dei dati da Netezza ad Azure, vedere l'articolo relativo [all'uso di Azure Data Factory per eseguire la migrazione dei dati dal server Netezza locale ad Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Netezza è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)


È possibile copiare dati da Netezza a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Netezza Connector supporta la copia parallela dall'origine. Per informazioni dettagliate, vedere la sezione [copia parallela da Netezza](#parallel-copy-from-netezza) .

Azure Data Factory fornisce un driver predefinito per abilitare la connettività. Non è pertanto necessario installare manualmente un driver per usare questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Inizia oggi stesso

È possibile creare una pipeline che usa un'attività di copia tramite .NET SDK, Python SDK, Azure PowerShell, l'API REST o un modello di Azure Resource Manager. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md).

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per il connettore Netezza.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Netezza sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **Netezza**. | SÌ |
| connectionString | Stringa di connessione ODBC per la connessione a Netezza. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `pwd` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | SÌ |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No |

Una stringa di connessione tipica è `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. La tabella seguente descrive altre proprietà che è possibile impostare:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| SecurityLevel | Il livello di sicurezza (SSL/TLS) che usa il driver per la connessione all'archivio dati. Esempio: `SecurityLevel=preferredSecured`. I valori supportati sono:<br/>- **Solo senza protezione** (**onlyUnSecured**): il driver non usa SSL.<br/>- **Preferibilmente senza protezione (preferredUnSecured) - Valore predefinito**: se il server consente di scegliere, il driver non usa SSL. <br/>- **Preferibilmente con protezione (preferredSecured)** : se il server consente di scegliere, il driver usa SSL. <br/>- **Solo con protezione (onlySecured)** : il driver non si connette a meno che non sia disponibile una connessione SSL. | No |
| CaCertFile | Il percorso completo per il certificato SSL usato dal server. Esempio: `CaCertFile=<cert path>;`| Sì, se SSL è abilitato |

**Esempio**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
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

Questa sezione presenta un elenco delle proprietà supportate dal set di dati Netezza.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md).

Per copiare dati da Netezza, impostare la proprietà **type** del set di dati su **NetezzaTable**. Sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **NetezzaTable** | SÌ |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| table | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Usare `schema` e `table` per un nuovo carico di lavoro. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine Netezza.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza come origine

>[!TIP]
>Per caricare i dati da Netezza in modo efficiente usando il partizionamento dei dati, vedere la sezione [copia parallela da Netezza](#parallel-copy-from-netezza) .

Per copiare dati da Netezza, impostare il tipo di **origine** nell'attività di copia su **NetezzaSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **NetezzaSource**. | SÌ |
| query | Usare la query SQL personalizzata per leggere i dati. Esempio: `"SELECT * FROM MyTable"` | No (se nel set di dati è specificato "tableName") |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare dati da Netezza. <br>Consenti valori: **None** (impostazione predefinita), **dataslice**e **DynamicRange**.<br>Quando è abilitata un'opzione di partizione (ovvero non `None`), il grado di parallelismo per caricare simultaneamente i dati da un database Netezza è controllato dall'impostazione [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. | No |
| partitionSettings | Consente di specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition non è `None`. | No |
| partitionColumnName | Specificare il nome della colonna di origine **nel tipo Integer** che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non è specificato, la chiave primaria della tabella viene rilevata automaticamente e utilizzata come colonna di partizione. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare `?AdfRangePartitionColumnName` nella clausola WHERE. Vedere l'esempio nella sezione [copia parallela da Netezza](#parallel-copy-from-netezza) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione in cui copiare i dati. <br>Applica quando l'opzione di partizione è `DynamicRange`. Se si utilizza query per recuperare i dati di origine, associare `?AdfRangePartitionUpbound` nella clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Netezza](#parallel-copy-from-netezza) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare `?AdfRangePartitionLowbound` nella clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Netezza](#parallel-copy-from-netezza) . | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Copia parallela da Netezza

Il connettore Data Factory Netezza fornisce il partizionamento dei dati predefinito per copiare dati da Netezza in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella tabella di **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-netezza/connector-netezza-partition-options.png)

Quando si Abilita la copia partizionata, Data Factory esegue query parallele sull'origine Netezza per caricare i dati in base alle partizioni. Il grado parallelo è controllato dall'impostazione del [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, Data Factory genera ed esegue quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati dal database Netezza.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati dal database di Netezza. Di seguito sono elencate le configurazioni consigliate per diversi scenari. Quando si copiano dati in un archivio dati basato su file, viene riordinata la scrittura in una cartella come più file (specifica solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da una tabella di grandi dimensioni.                                   | **Opzione partition**: data Slice. <br><br/>Durante l'esecuzione, Data Factory partiziona automaticamente i dati in base alle [sezioni di dati predefinite di Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia i dati in base alle partizioni. |
| Caricare grandi quantità di dati tramite una query personalizzata.                 | **Opzione partition**: data Slice.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante l'esecuzione, Data Factory sostituisce `?AdfPartitionCount` (con il numero di copia parallelo impostato sull'attività di copia) e `?AdfDataSliceCondition` con la logica della partizione della sezione di dati e invia a Netezza. |
| Caricare grandi quantità di dati tramite una query personalizzata, con una colonna di tipo integer con un valore distribuito uniformemente per il partizionamento dell'intervallo. | **Opzioni di partizione**: partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonna partizione**: specificare la colonna utilizzata per partizionare i dati. È possibile partizionare la colonna con il tipo di dati Integer.<br>Limite **superiore partizione** e **limite inferiore partizione**: specificare se si desidera filtrare in base alla colonna partizione per recuperare i dati solo tra l'intervallo inferiore e quello superiore.<br><br>Durante l'esecuzione, Data Factory sostituisce `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`e `?AdfRangePartitionLowbound` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e invia a Netezza. <br>Ad esempio, se la colonna di partizione "ID" è impostata con il limite inferiore come 1 e il limite superiore come 80, con la copia parallela impostata su 4, Data Factory recupera i dati di 4 partizioni. I rispettivi ID sono rispettivamente compresi tra [1, 20], [21, 40], [41, 60] e [61, 80]. |

**Esempio: query con partizione di sezione dati**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Esempio: query con partizione a intervalli dinamici**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
