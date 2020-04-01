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
ms.openlocfilehash: c7e17f7c4493560bd6118b8d4837fd795a6ab0c8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422852"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiare dati da Netezza usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Netezza. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

>[!TIP]
>Per lo scenario di migrazione dei dati da Netezza ad Azure, vedere Usare Azure Data Factory per eseguire la migrazione dei [dati dal server Netezza](data-migration-guidance-netezza-azure-sqldw.md)locale ad Azure.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Netezza è supportato per le seguenti attività:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)


È possibile copiare dati da Netezza a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Il connettore Netezza supporta la copia parallela dall'origine. Vedere la sezione [Copia parallela da Netezza](#parallel-copy-from-netezza) per i dettagli.

Azure Data Factory fornisce un driver predefinito per abilitare la connettività. Non è pertanto necessario installare manualmente un driver per usare questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

È possibile creare una pipeline che usa un'attività di copia tramite .NET SDK, Python SDK, Azure PowerShell, l'API REST o un modello di Azure Resource Manager. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md).

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per il connettore Netezza.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Netezza sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **Netezza**. | Sì |
| connectionString | Stringa di connessione ODBC per la connessione a Netezza. <br/>È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `pwd` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No |

Una stringa di connessione tipica è `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. La tabella seguente descrive altre proprietà che è possibile impostare:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| SecurityLevel | Il livello di sicurezza (SSL/TLS) che usa il driver per la connessione all'archivio dati. Esempio: `SecurityLevel=preferredSecured`. I valori supportati sono:<br/>- **Solo senza protezione** (**onlyUnSecured**): il driver non usa SSL.<br/>- **Preferibilmente senza protezione (preferredUnSecured) - Valore predefinito**: se il server consente di scegliere, il driver non usa SSL. <br/>- **Preferito protetto (preferredSecured):** Se il server fornisce una scelta, il driver utilizza SSL. <br/>- **Solo con protezione (onlySecured)**: il driver non si connette a meno che non sia disponibile una connessione SSL. | No |
| CaCertFile | Il percorso completo per il certificato SSL usato dal server. Esempio: `CaCertFile=<cert path>;`| Sì, se SSL è abilitato |

**Esempio**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
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

Per copiare i dati da Netezza, impostare la proprietà **type** del set di dati su **NetezzaTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **NetezzaTable** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| tabella | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Utilizzare `schema` `table` e per un nuovo carico di lavoro. | No (se nell'origine dell'attività è specificato "query") |

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
>Per caricare i dati da Netezza in modo efficiente utilizzando il partizionamento dei dati, vedere Copia [parallela da Netezza.](#parallel-copy-from-netezza)

Per copiare dati da Netezza, impostare il tipo di **origine** nell'attività di copia su **NetezzaSource**. Nella sezione Copia origine attività sono supportate le proprietà seguenti:The following properties are supported in the Copy Activity **source** section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **NetezzaSource**. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Esempio: `"SELECT * FROM MyTable"` | No (se nel set di dati è specificato "tableName") |
| partizioniOpzioni | Specifica le opzioni di partizionamento dei dati utilizzate per caricare i dati da Netezza. <br>I valori consentiti sono: **None (impostazione** predefinita), **DataSlice**e **DynamicRange**.<br>Quando è abilitata un'opzione `None`di partizione, ovvero non ), il grado di parallelismo [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) per il caricamento simultaneo dei dati da un database Netezza viene controllato impostando l'attività di copia. | No |
| partitionImpostazioni | Specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione `None`partizione non è . | No |
| partitionColumnName (nome di colonna) | Specificare il nome della colonna di origine **in tipo Integer** che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non specificato, la chiave primaria della tabella viene rilevata automaticamente e utilizzata come colonna di partizione. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per `?AdfRangePartitionColumnName` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Vedere l'esempio nella sezione [Copia parallela da Netezza.See](#parallel-copy-from-netezza) example in Parallel copy from Netezza section. | No |
| partitionUpperBound | Valore massimo della colonna della partizione in cui copiare i dati. <br>Applicare quando l'opzione partizione è `DynamicRange`. Se si utilizza la query `?AdfRangePartitionUpbound` per recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Netezza.For](#parallel-copy-from-netezza) an example, see the Parallel copy from Netezza section. | No |
| partizioneLowerBound | Valore minimo della colonna della partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per `?AdfRangePartitionLowbound` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Netezza.For](#parallel-copy-from-netezza) an example, see the Parallel copy from Netezza section. | No |

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

Il connettore Data Factory Netezza fornisce il partizionamento dei dati incorporato per copiare i dati da Netezza in parallelo. Le opzioni di partizionamento dei dati sono disponibili nella tabella **Origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-netezza/connector-netezza-partition-options.png)

Quando si abilita la copia partizionata, Data Factory esegue query parallele sull'origine Netezza per caricare i dati in base alle partizioni. Il grado parallelo è [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controllato dall'impostazione dell'attività di copia. Ad esempio, se `parallelCopies` si imposta su quattro, Data Factory genera ed esegue contemporaneamente quattro query in base all'opzione e alle impostazioni di partizione specificate e ogni query recupera una parte dei dati dal database Netezza.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, soprattutto quando si carica grandi quantità di dati dal database Netezza. Di seguito sono riportate le configurazioni consigliate per scenari diversi. Quando si copiano dati nell'archivio dati basato su file, viene comandato di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni suggerite                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pieno carico da tavolo di grandi dimensioni.                                   | **Opzione di partizione**: Sezione dati. <br><br/>Durante l'esecuzione, Data Factory partiziona automaticamente i dati in base alle sezioni di dati predefinite di [Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia i dati in base alle partizioni. |
| Caricare grandi quantità di dati utilizzando una query personalizzata.                 | **Opzione di partizione**: Sezione dati.<br>**Query** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`: .<br>Durante l'esecuzione, `?AdfPartitionCount` Data Factory sostituisce (con il `?AdfDataSliceCondition` numero di copia parallela impostato sull'attività di copia) e con la logica della partizione dell'sezione di dati e invia a Netezza. |
| Caricare grandi quantità di dati utilizzando una query personalizzata, con una colonna intera con un valore distribuito uniformemente per il partizionamento dell'intervallo. | **Opzioni di partizione**: Partizione a intervallo dinamico.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. È possibile partizionare in base alla colonna con tipo di dati integer.<br>**Partizione limite superiore** e **limite inferiore della partizione:** specificare se si desidera filtrare in base alla colonna della partizione per recuperare i dati solo tra l'intervallo inferiore e superiore.<br><br>Durante l'esecuzione, `?AdfRangePartitionColumnName`Data `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` sostituisce , e con il nome di colonna effettivo e gli intervalli di valori per ogni partizione e invia a Netezza. <br>Ad esempio, se la colonna della partizione "ID" è impostata con il limite inferiore come 1 e il limite superiore come 80, con copia parallela impostata come 4, Data Factory recupera i dati da 4 partizioni. I loro ID sono compresi tra [1,20], [21, 40], [41, 60] e [61, 80], rispettivamente. |

**Esempio: query con partizione di sezione datiExample: query with data slice partition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Esempio: query con partizione di intervallo dinamicoExample: query with dynamic range partition**

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

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
