---
title: Copiare dati da Teradata usando Azure Data Factory | Microsoft Docs
description: Informazioni sul connettore Teradata del servizio Data Factory, che consente di copiare dati da un database Teradata in archivi dati supportati da Data Factory come sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561438"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copiare dati da Teradata usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versione corrente](connector-teradata.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Teradata. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Teradata in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Teradata supporta:

- Teradata **versione 14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- La copia di dati usando l'autenticazione **Di base** o **Windows**.
- Copia parallela dall'origine Teradata. Visualizzare [copia da Teradata parallela](#parallel-copy-from-teradata) sezione con i dettagli.

> [!NOTE]
>
> Azure Data Factory aggiornato il connettore Teradata poiché v3.18 Runtime di integrazione Self-Hosted, che ha il compito da un driver ODBC integrato e offre opzioni flessibili di connessione, nonché out-of-box copia parallela per migliorare le prestazioni. Qualsiasi carico di lavoro esistente usando il connettore Teradata precedente attivate dal Provider di dati .NET per Teradata è ancora supportato come-è, anche se verrà consigliato di usare quello nuovo in futuro. Si noti che il nuovo percorso richiede un set diverso di origine collegato servizio/set di dati/copia. Nei dettagli della configurazione, vedere la sezione corrispondente.

## <a name="prerequisites"></a>Prerequisiti

Se non accessibile pubblicamente di Teradata, è necessario configurare un Runtime di integrazione Self-Hosted. Per altre informazioni sul runtime di integrazione, vedere [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md). Il Runtime di integrazione offre un driver Teradata integrato a partire dalla versione 3.18, pertanto non è necessario installare manualmente alcun driver. Il driver richiede "Visual C++ Redistributable 2012 Update 4" nel computer del runtime di integrazione Self-Hosted, scaricare dal [qui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) se non si ha ancora installato.

Per la versione di runtime di integrazione Self-Hosted è inferiore a 3.18, è necessario installare il [Provider di dati .NET per Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) versione 14 o successiva nel computer del Runtime di integrazione. 

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Teradata.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Teradata sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Teradata** | Yes |
| connectionString | Specifica le informazioni necessarie per connettersi all'istanza del Database di Teradata. Vedere gli esempi seguenti.<br/>È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `password` all'esterno della stringa di connessione. Fare riferimento a [Store le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) articolo con altri dettagli. | Yes |
| username | Specificare il nome utente per la connessione al database Teradata. Si applica quando si usa l'autenticazione di Windows. | No |
| password | Specificare la password per l'account utente specificato per il nome utente. È anche possibile effettuare [fanno riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). <br>Si applica quando si usa l'autenticazione di Windows, o la password in Azure Key Vault per l'autenticazione di base di riferimento. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Yes |

**Esempio: uso dell'autenticazione di base**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: uso dell'autenticazione di Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Se si usa con il seguente payload Teradata servizio collegato abilitato dal Provider di dati .NET per Teradata, è ancora supportata come-è, anche se verrà consigliato di usare quello nuovo in futuro.

**Payload precedente:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Teradata.

Per copiare dati da Teradata, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **TeradataTable** | Yes |
| database | Nome del database di Teradata. | No (se nell'origine dell'attività è specificato "query") |
| table | Nome della tabella nel database Teradata. | No (se nell'origine dell'attività è specificato "query") |

**Esempio:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Se si usa set di dati di tipo "RelationalTable" come illustrato di seguito, è ancora supportata come-è, anche se verrà consigliato di usare quello nuovo in futuro.

**Payload precedente:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Teradata.

### <a name="teradata-as-source"></a>Teradata come origine

> [!TIP]
>
> Altre informazioni, vedere [copia da Teradata parallela](#parallel-copy-from-teradata) sezione su come caricare i dati da Teradata in modo efficiente utilizzando il partizionamento dei dati.

Per copiare dati da Teradata, sono supportate le proprietà seguenti nell'attività di copia **origine** sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **TeradataSource** | Yes |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`.<br>Quando si abilita caricamento partizionata, devi associare parametri predefiniti partizione corrispondente nella query. Vedere gli esempi nella [copia da Teradata parallela](#parallel-copy-from-teradata) sezione. | No (se è specificata una query nel set di dati) |
| partitionOptions | Specifica le opzioni utilizzate per caricare dati da Teradata di partizionamento dei dati. <br>Consenti i valori sono: **None** (impostazione predefinita), **Hash** e **DynamicRange**.<br>Quando è abilitato opzione di partizionamento (non ' None'), anche configurare **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** impostazione per attività di copia, ad esempio come 4, che determina il grado di caricare simultaneamente i dati da Teradata parallel database. | No |
| partitionSettings | Specificare il gruppo delle impostazioni per il partizionamento dei dati. <br>Si applicano quando l'opzione di partizione non `None`. | No |
| partitionColumnName | Specificare il nome della colonna di origine **nel tipo integer** che verrà usato per il partizionamento per intervalli per la copia parallela. Se non specificato, la chiave primaria della tabella verrà automaticamente rilevato e utilizzato come colonna di partizione. <br>Applicato quando l'opzione di partizione è `Hash` o `DynamicRange`. Se si usa una query per recuperare i dati di origine, hook `?AdfHashPartitionCondition` o `?AdfRangePartitionColumnName` nella clausola WHERE. Vedere l'esempio nella [copia da Teradata parallela](#parallel-copy-from-teradata) sezione. | No |
| partitionUpperBound | Valore massimo della colonna di partizione e copiare i dati. <br>Applicato quando l'opzione di partizionamento è `DynamicRange`. Se si usa una query per recuperare i dati di origine, associare `?AdfRangePartitionUpbound` nella clausola WHERE. Vedere l'esempio nella [copia da Teradata parallela](#parallel-copy-from-teradata) sezione. | No |
| PartitionLowerBound | Valore minimo della colonna di partizione e copiare i dati. <br>Applicato quando l'opzione di partizionamento è `DynamicRange`. Se si usa una query per recuperare i dati di origine, associare `?AdfRangePartitionLowbound` nella clausola WHERE. Vedere l'esempio nella [copia da Teradata parallela](#parallel-copy-from-teradata) sezione. | No |

> [!NOTE]
>
> Se si usa copia origine di tipo "RelationalSource", è ancora supportata come-è, ma non supporta il nuovo caricamento parallelo incorporato da Teradata (opzioni di partizione). Consigliabile per usare uno nuovo in futuro.

**Esempio: copiare i dati usando query di base senza partizione**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Copia parallela da Teradata

Connettore Teradata di data factory offre il partizionamento per copiare dati da Teradata in parallelo con prestazioni elevate di dati incorporato. È possibile trovare le opzioni di partizionamento dei dati sull'attività di copia -> origine Teradata:

![Opzioni di partizione](./media/connector-teradata/connector-teradata-partition-options.png)

Quando si abilita copia partizionata, data factory esegue query in parallelo sull'origine Teradata per caricare i dati dalle partizioni. Viene configurato e controllato tramite il grado parallelo **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** impostazione sull'attività di copia. Ad esempio, se si imposta `parallelCopies` come quattro, data factory genera contemporaneamente ed esegue quattro query in base l'opzione di partizione specificato e le impostazioni, ogni parte durante il recupero dei dati dal database Teradata.

È consigliato di abilitare la copia parallela con soprattutto quando si caricano grandi quantità di dati da Teradata database di partizionamento dei dati. Di seguito sono le configurazioni consigliate per scenari diversi:

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo dalla tabella di grandi dimensioni                                   | **Opzione di partizione**: Hash. <br><br/>Durante l'esecuzione, data Factory automaticamente rileva colonna chiave primaria, applica hash su di esso e copia i dati dalle partizioni. |
| Caricare grandi quantità di dati tramite query personalizzata                 | **Opzione di partizione**: Hash.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Colonna di partizione**: Specificare la colonna utilizzata per la partizione hash applica. Se omesso, Azure Data factory rileva automaticamente la colonna chiave primaria della tabella specificato nel set di dati Teradata.<br><br>Durante l'esecuzione, data factory replace `?AdfHashPartitionCondition` con la logica di partizione di hash e trasmissione per Teradata. |
| Caricare grandi quantità di dati tramite query personalizzata, con una colonna di tipo integer con valore distribuite in modo uniforme per il partizionamento per intervalli | **Opzioni partizioni**: Partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonna di partizione**: Specificare la colonna utilizzata per partizionare i dati. È possibile partizionare eseguita sulla colonna con tipo di dati integer.<br>**Limite superiore di partizione** e **limite inferiore di partizione**: Specificare se si desidera filtrare i dati di colonna di partizione per recuperare solo i dati tra l'intervallo inferiore e superiore.<br><br>Durante l'esecuzione, data factory replace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` con il nome di colonna effettivi e il valore degli intervalli per ogni partizione e inviare a Teradata. <br>Ad esempio, se la colonna di partizione "ID" impostato con limite inferiore come 1 e il limite superiore come 80, con il set di copie parallele come 4, Azure Data factory recuperare dati da 4 partizioni con ID compreso tra [1,20], [21, 40,] [41, 60] e [61, 80]. |

**Esempio: query alla partizione hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Esempio: query alla partizione dell'intervallo dinamico**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapping dei tipi di dati per Teradata

Quando si copiano dati da Teradata, vengono usati i mapping seguenti tra i tipi di dati di Teradata e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Teradata | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Non supportati. Applicare un cast esplicito nella query di origine. |
| Integer |Int32 |
| Interval Day |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Day To Hour |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Day To Minute |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Day To Second |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Hour |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Hour To Minute |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Hour To Second |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Minute |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Minute To Second |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Month |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Second |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Year |Non supportati. Applicare un cast esplicito nella query di origine. |
| Interval Year To Month |Non supportati. Applicare un cast esplicito nella query di origine. |
| Number |Double |
| Periodo (Date) |Non supportati. Applicare un cast esplicito nella query di origine. |
| Periodo) |Non supportati. Applicare un cast esplicito nella query di origine. |
| Periodo (ora con fuso orario) |Non supportati. Applicare un cast esplicito nella query di origine. |
| Period (Timestamp) |Non supportati. Applicare un cast esplicito nella query di origine. |
| Periodo (Timestamp con fuso orario) |Non supportati. Applicare un cast esplicito nella query di origine. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Non supportati. Applicare un cast esplicito nella query di origine. |
| Xml |Non supportati. Applicare un cast esplicito nella query di origine. |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
