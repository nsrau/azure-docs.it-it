---
title: Copiare dati da Teradata Vantage usando Azure Data Factory
description: Il connettore Teradata del servizio Data Factory consente di copiare i dati da un Teradata Vantage agli archivi dati supportati da Data Factory come sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 4074c50aa17bf804696060134e37055a18bd0137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680098"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copiare dati da Teradata Vantage usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione del servizio di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versione corrente](connector-teradata.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Teradata Vantage. Si basa sulla [Panoramica dell'attività di copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Teradata è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Teradata Vantage a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

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

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **Teradata**. | Sì |
| connectionString | Specifica le informazioni necessarie per la connessione all'istanza Teradata. Vedere gli esempi seguenti.<br/>È anche possibile inserire una password in Azure Key Vault ed estrarre la configurazione `password` dalla stringa di connessione. Per informazioni dettagliate, vedere [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) . | Sì |
| Nome utente | Specificare un nome utente per la connessione a Teradata. Si applica quando si utilizza l'autenticazione di Windows. | No |
| password | Specificare una password per l'account utente specificato per il nome utente. È anche possibile scegliere di [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). <br>Si applica quando si usa l'autenticazione di Windows o si fa riferimento a una password in Key Vault per l'autenticazione di base. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |Sì |

Ulteriori proprietà di connessione che è possibile impostare nella stringa di connessione in base al caso:

| Proprietà | Descrizione | Valore predefinito |
|:--- |:--- |:--- |
| CharacterSet | Set di caratteri da utilizzare per la sessione. Ad esempio, `CharacterSet=UTF16`.<br><br/>Questo valore può essere un set di caratteri definito dall'utente o uno dei set di caratteri predefiniti seguenti: <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>-Shift-JIS (Windows, compatibile con DOS, KANJISJIS_0S)<br/>-EUC (compatibile con UNIX, KANJIEC_0U)<br/>-Mainframe IBM (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Il valore predefinito è `ASCII`. |
| MaxRespSize |Dimensioni massime del buffer di risposta per le richieste SQL, in kilobyte (KB). Ad esempio, `MaxRespSize=‭10485760‬`.<br/><br/>Per il database Teradata versione 16,00 o successiva, il valore massimo è 7361536. Per le connessioni che usano versioni precedenti, il valore massimo è 1048576. | Il valore predefinito è `65536`. |

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

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su `TeradataTable`. | Sì |
| database | Nome dell'istanza di Teradata. | No (se nell'origine dell'attività è specificato "query") |
| table | Nome della tabella nell'istanza di Teradata. | No (se nell'origine dell'attività è specificato "query") |

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
> il set di dati di tipo `RelationalTable` è ancora supportato. Tuttavia, è consigliabile usare il nuovo set di dati.

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
>Per caricare i dati da Teradata in modo efficiente usando il partizionamento dei dati, vedere la sezione relativa alla [copia parallela da Teradata](#parallel-copy-from-teradata) .

Per copiare dati da Teradata, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su `TeradataSource`. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`.<br>Quando si Abilita il caricamento partizionato, è necessario associare tutti i parametri di partizione predefiniti corrispondenti nella query. Per esempi, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No (se è specificata una query nel set di dati) |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare dati da Teradata. <br>Consenti valori: **None** (impostazione predefinita), **hash** e **DynamicRange**.<br>Quando è abilitata un'opzione di partizione (ovvero non `None`), il grado di parallelismo per il caricamento simultaneo di dati da Teradata è controllato dall'impostazione della [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. | No |
| partitionSettings | Consente di specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition non è `None`. | No |
| partitionColumnName | Specificare il nome della colonna di origine che verrà utilizzata dalla partizione di intervallo o dalla partizione hash per la copia parallela. Se non specificato, l'indice primario della tabella viene rilevato automaticamente e utilizzato come colonna di partizione. <br>Applicare quando l'opzione partition è `Hash` o `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare `?AdfHashPartitionCondition` o `?AdfRangePartitionColumnName` nella clausola WHERE. Vedere l'esempio nella sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione in cui copiare i dati. <br>Applica quando l'opzione di partizione è `DynamicRange`. Se si utilizza query per recuperare i dati di origine, associare `?AdfRangePartitionUpbound` nella clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare `?AdfRangePartitionLowbound` nella clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Teradata](#parallel-copy-from-teradata) . | No |

> [!NOTE]
>
> `RelationalSource` tipo di origine della copia è ancora supportato, ma non supporta il nuovo carico parallelo incorporato da Teradata (opzioni di partizione). Tuttavia, è consigliabile usare il nuovo set di dati.

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

Quando si Abilita la copia partizionata, Data Factory esegue query parallele sull'origine Teradata per caricare i dati in base alle partizioni. Il grado parallelo è controllato dall'impostazione del [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, Data Factory genera ed esegue quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati da Teradata.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati da Teradata. Di seguito sono elencate le configurazioni consigliate per diversi scenari. Quando si copiano dati in un archivio dati basato su file, viene riordinata la scrittura in una cartella come più file (specifica solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da una tabella di grandi dimensioni.                                   | **Opzione partition**: hash. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente la colonna PK, applica un hash a tale colonna e copia i dati in base alle partizioni. |
| Caricare grandi quantità di dati tramite una query personalizzata.                 | **Opzione partition**: hash.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Colonna partizione**: specificare la colonna utilizzata per applica partizione hash. Se non specificato, Data Factory rileva automaticamente la colonna PK della tabella specificata nel set di dati Teradata.<br><br>Durante l'esecuzione, Data Factory sostituisce `?AdfHashPartitionCondition` con la logica della partizione hash e invia a Teradata. |
| Caricare grandi quantità di dati tramite una query personalizzata, con una colonna di tipo integer con un valore distribuito uniformemente per il partizionamento dell'intervallo. | **Opzioni di partizione**: partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonna partizione**: specificare la colonna utilizzata per partizionare i dati. È possibile partizionare la colonna con il tipo di dati Integer.<br>Limite **superiore partizione** e **limite inferiore partizione**: specificare se si desidera filtrare in base alla colonna partizione per recuperare i dati solo tra l'intervallo inferiore e quello superiore.<br><br>Durante l'esecuzione, Data Factory sostituisce `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`e `?AdfRangePartitionLowbound` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e invia a Teradata. <br>Ad esempio, se la colonna di partizione "ID" è impostata con il limite inferiore come 1 e il limite superiore come 80, con la copia parallela impostata su 4, Data Factory recupera i dati di 4 partizioni. I rispettivi ID sono rispettivamente compresi tra [1, 20], [21, 40], [41, 60] e [61, 80]. |

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
| BLOB |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Data |DateTime |
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
| Intervallo - da ora a secondo |Non supportati. Applica cast esplicito nella query di origine. |
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


## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
