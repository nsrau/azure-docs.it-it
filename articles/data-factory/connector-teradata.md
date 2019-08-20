---
title: Copiare dati da Teradata usando Azure Data Factory | Microsoft Docs
description: Il connettore Teradata del servizio Data Factory consente di copiare i dati da un database Teradata agli archivi dati supportati da Data Factory come sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 134302bffdadc27cf202a43e7dc4cc94704bb5b3
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557876"
---
# <a name="copy-data-from-teradata-by-using-azure-data-factory"></a>Copiare dati da Teradata usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versione corrente](connector-teradata.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Teradata. Si basa sulla [Panoramica dell'attività di copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Teradata in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Teradata supporta:

- Teradata **versione 14,10, 15,0, 15,10, 16,0, 16,10 e 16,20**.
- Copia dei dati tramite l'autenticazione di **base** o di **Windows** .
- Copia parallela da un'origine Teradata. Per informazioni dettagliate, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) .

> [!NOTE]
>
> Dopo il rilascio del runtime di integrazione self-hosted v 3.18, Azure Data Factory aggiornato il connettore Teradata. Tutti i carichi di lavoro esistenti che usano il connettore Teradata precedente sono ancora supportati. Per i nuovi carichi di lavoro, tuttavia, è consigliabile usare quello nuovo. Si noti che il nuovo percorso richiede un diverso set di servizi collegati, set di dati e origine della copia. Per informazioni dettagliate sulla configurazione, vedere le rispettive sezioni che seguono.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Il runtime di integrazione fornisce un driver Teradata incorporato, a partire dalla versione 3,18. Non è necessario installare manualmente alcun driver. Il driver richiede "Visual C++ redistributable 2012 Update 4" nel computer del runtime di integrazione self-hosted. Se non è ancora installato, scaricarlo da [qui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

Per qualsiasi versione del runtime di integrazione self-hosted precedente alla 3,18, installare [.net provider di dati per Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), versione 14 o successiva, nel computer del runtime di integrazione. 

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche del connettore Teradata.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il servizio collegato Teradata supporta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **Teradata**. | Sì |
| connectionString | Specifica le informazioni necessarie per la connessione all'istanza del database Teradata. Vedere gli esempi seguenti.<br/>È anche possibile inserire una password in Azure Key Vault ed estrarre la `password` configurazione dalla stringa di connessione. Per informazioni dettagliate, vedere [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) . | Yes |
| userName | Specificare un nome utente per la connessione al database Teradata. Si applica quando si utilizza l'autenticazione di Windows. | No |
| password | Specificare una password per l'account utente specificato per il nome utente. È anche possibile scegliere di [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). <br>Si applica quando si usa l'autenticazione di Windows o si fa riferimento a una password in Key Vault per l'autenticazione di base. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |Sì |

**Esempio di utilizzo dell'autenticazione di base**

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

**Esempio di utilizzo dell'autenticazione di Windows**

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
> Il payload seguente è ancora supportato. In futuro, tuttavia, è consigliabile usare quello nuovo.

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

Questa sezione presenta un elenco delle proprietà supportate dal set di dati Teradata. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [Datasets](concepts-datasets-linked-services.md).

Per copiare dati da Teradata, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata `TeradataTable`su. | Yes |
| database | Nome del database Teradata. | No (se nell'origine dell'attività è specificato "query") |
| table | Nome della tabella nel database Teradata. | No (se nell'origine dell'attività è specificato "query") |

**Esempio:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`il set di dati di tipo è ancora supportato. Tuttavia, è consigliabile usare il nuovo set di dati.

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

Questa sezione presenta un elenco delle proprietà supportate dall'origine Teradata. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata come origine

>[!TIP]
>Per caricare i dati da Teradata in modo efficiente usando il partizionamento dei dati, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) .

Per copiare dati da Teradata, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata `TeradataSource`su. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`.<br>Quando si Abilita il caricamento partizionato, è necessario associare tutti i parametri di partizione predefiniti corrispondenti nella query. Per esempi, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No (se è specificata una tabella nel set di dati) |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare dati da Teradata. <br>Consenti valori: **Nessuna** (impostazione predefinita), **hash** e **DynamicRange**.<br>Quando è abilitata un'opzione di partizione, ovvero non `None`, configurare anche l' [`parallelCopies`](copy-activity-performance.md#parallel-copy) impostazione per l'attività di copia. Questo determina il grado di parallelismo di caricare simultaneamente i dati da un database Teradata. Ad esempio, è possibile impostare questa impostazione su 4. | No |
| partitionSettings | Consente di specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition `None`non è. | No |
| partitionColumnName | Specificare il nome della colonna di origine **nel tipo Integer** che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non è specificato, la chiave primaria della tabella viene rilevata automaticamente e utilizzata come colonna della partizione. <br>Applicare quando l'opzione di partizione `Hash` è `DynamicRange`o. Se si utilizza una query per recuperare i dati di origine, `?AdfHashPartitionCondition` hook `?AdfRangePartitionColumnName` o nella clausola WHERE. Vedere l'esempio nella sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione in cui copiare i dati. <br>Applica quando l'opzione di `DynamicRange`partizione è. Se si utilizza query per recuperare i dati di origine `?AdfRangePartitionUpbound` , associare la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione `DynamicRange`è. Se si utilizza una query per recuperare i dati di origine, `?AdfRangePartitionLowbound` associare la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No |

> [!NOTE]
>
> `RelationalSource`il tipo di origine della copia è ancora supportato, ma non supporta il nuovo carico parallelo incorporato da Teradata (opzioni di partizione). Tuttavia, è consigliabile usare il nuovo set di dati.

**Esempio: copiare i dati usando una query di base senza partizione**

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

Il connettore Data Factory Teradata fornisce il partizionamento dei dati predefinito per la copia di dati da Teradata in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella tabella di **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-teradata/connector-teradata-partition-options.png)

Quando si Abilita la copia partizionata, Data Factory esegue query parallele sull'origine Teradata per caricare i dati in base alle partizioni. Il grado parallelo è controllato dall' [`parallelCopies`](copy-activity-performance.md#parallel-copy) impostazione dell'attività di copia. Se, ad esempio, si `parallelCopies` imposta su quattro, data factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione e alle impostazioni specificate. Ogni query recupera una porzione di dati dal database Teradata.

È consigliabile abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati dal database Teradata. Di seguito sono elencate le configurazioni consigliate per diversi scenari:

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da una tabella di grandi dimensioni.                                   | **Opzione partizione**: Hash. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente la colonna PK, applica un hash a tale colonna e copia i dati in base alle partizioni. |
| Caricare grandi quantità di dati tramite una query personalizzata.                 | **Opzione partizione**: Hash.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Colonna partizione**: Specificare la colonna utilizzata per applica partizione hash. Se non specificato, Data Factory rileva automaticamente la colonna PK della tabella specificata nel set di dati Teradata.<br><br>Durante l'esecuzione, data factory `?AdfHashPartitionCondition` sostituisce con la logica della partizione hash e invia a Teradata. |
| Caricare grandi quantità di dati tramite una query personalizzata, con una colonna di tipo integer con un valore distribuito uniformemente per il partizionamento dell'intervallo. | **Opzioni partizione**: Partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. È possibile partizionare la colonna con il tipo di dati Integer.<br>Limite **superiore della partizione** e **limite inferiore della partizione**: Specificare se si desidera filtrare in base alla colonna di partizione per recuperare i dati solo tra l'intervallo inferiore e quello superiore.<br><br>Durante l'esecuzione, data factory `?AdfRangePartitionColumnName`sostituisce `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e invia a Teradata. <br>Ad esempio, se la colonna di partizione "ID" è impostata con il limite inferiore come 1 e il limite superiore come 80, con la copia parallela impostata su 4, Data Factory recupera i dati di 4 partizioni. I rispettivi ID sono rispettivamente compresi tra [1, 20], [21, 40], [41, 60] e [61, 80]. |

**Esempio: query con partizione hash**

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

**Esempio: query con partizione a intervalli dinamici**

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

Quando si copiano dati da Teradata, vengono applicati i mapping seguenti. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati di Teradata | Tipo di dati provvisorio di Data Factory |
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
| Graphic |Non supportati. Applica cast esplicito nella query di origine. |
| Integer |Int32 |
| Interval Day |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Day To Hour |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Day To Minute |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Day To Second |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Hour |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Hour To Minute |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Hour To Second |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Minute |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Minute To Second |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Month |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Second |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Year |Non supportati. Applica cast esplicito nella query di origine. |
| Interval Year To Month |Non supportati. Applica cast esplicito nella query di origine. |
| Number |Double |
| Periodo (Data) |Non supportati. Applica cast esplicito nella query di origine. |
| Periodo (ora) |Non supportati. Applica cast esplicito nella query di origine. |
| Periodo (ora con fuso orario) |Non supportati. Applica cast esplicito nella query di origine. |
| Periodo (timestamp) |Non supportati. Applica cast esplicito nella query di origine. |
| Periodo (timestamp con fuso orario) |Non supportati. Applica cast esplicito nella query di origine. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Non supportati. Applica cast esplicito nella query di origine. |
| Xml |Non supportati. Applica cast esplicito nella query di origine. |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
