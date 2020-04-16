---
title: Copiare dati da Teradata Vantage tramite Azure Data FactoryCopy data from Teradata Vantage by using Azure Data Factory
description: Il connettore Teradata del servizio Data Factory consente di copiare i dati da un Teradata Vantage agli archivi dati supportati da Data Factory come sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 4eed79210e3e39f82b892ac0681e161ebb59597e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418032"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copiare dati da Teradata Vantage tramite Azure Data FactoryCopy data from Teradata Vantage by using Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versione corrente](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare i dati da Teradata Vantage.This article outlines how to use the copy activity in Azure Data Factory to copy data from Teradata Vantage. Si basa sulla [panoramica dell'attività](copy-activity-overview.md)di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Teradata è supportato per le attività seguenti:This Teradata connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare i dati da Teradata Vantage in qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Teradata supporta:

- Teradata **versione 14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- Copia dei dati tramite l'autenticazione **di base** o **di Windows.**
- Copia parallela da un'origine Teradata. Vedere la sezione [Copia parallela da Teradata](#parallel-copy-from-teradata) per i dettagli.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Se si utilizza Self-hosted Integration Runtime, si noti che fornisce un driver Teradata incorporato a partire dalla versione 3.18.If you use Self-hosted Integration Runtime, note it provides a built-in Teradata driver starting from version 3.18. Non è necessario installare manualmente alcun driver. Il driver richiede "Visual C, Redistributable 2012 Update 4" nel computer di runtime di integrazione self-hosted. Se non lo hai ancora installato, scaricalo da [qui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà utilizzate per definire le entità di Data Factory specifiche del connettore Teradata.The following sections provide details about properties that are used to define data factory entities specific to the Teradata connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il servizio collegato Teradata supporta le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Teradata**. | Sì |
| connectionString | Specifica le informazioni necessarie per connettersi all'istanza Teradata. Vedere gli esempi seguenti.<br/>È anche possibile inserire una password nell'insieme di credenziali delle chiavi di Azure ed estrarre la `password` configurazione dalla stringa di connessione. Fare riferimento a [Archiviare le credenziali nell'insieme di credenziali delle chiavi](store-credentials-in-key-vault.md) di Azure con altri dettagli. | Sì |
| username | Specificare un nome utente per la connessione a Teradata. Si applica quando si utilizza l'autenticazione di Windows. | No |
| password | Specificare una password per l'account utente specificato per il nome utente. È anche possibile scegliere di fare riferimento a [un segreto archiviato in Archiviazione chiave](store-credentials-in-key-vault.md)di Azure . <br>Si applica quando si utilizza l'autenticazione di Windows o si fa riferimento a una password nell'insieme di credenziali delle chiavi per l'autenticazione di base. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Altre proprietà di connessione che è possibile impostare nella stringa di connessione in base al caso:More connection properties you can set in connection string per your case:

| Proprietà | Descrizione | Valore predefinito |
|:--- |:--- |:--- |
| CharacterSet | Set di caratteri da utilizzare per la sessione. Ad esempio, `CharacterSet=UTF16`.<br><br/>Questo valore può essere un set di caratteri definito dall'utente o uno dei seguenti set di caratteri predefiniti: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS compatibile, KANJISJIS_0S)<br/>- EUC (Unix compatibile, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Il valore predefinito è `ASCII`. |
| MaxRespSize (dimensioni massime) |Dimensione massima del buffer delle risposte per le richieste SQL, in kilobyte (KB). Ad esempio, `MaxRespSize=‭10485760‬`.<br/><br/>Per Teradata Database versione 16.00 o successiva, il valore massimo è 7361536.For Teradata Database version 16.00 or later, the maximum value is 7361536. Per le connessioni che utilizzano versioni precedenti, il valore massimo è 1048576. | Il valore predefinito è `65536`. |

**Esempio di utilizzo dell'autenticazione di baseExample using basic authentication**

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

**Esempio di utilizzo dell'autenticazione di WindowsExample using Windows authentication**

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
> Il payload seguente è ancora supportato. Andando avanti, tuttavia, è necessario utilizzare quello nuovo.

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

In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati Teradata. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei dataset, vedere [Dataset](concepts-datasets-linked-services.md).

Per copiare i dati da Teradata, sono supportate le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del dataset `TeradataTable`deve essere impostata su . | Sì |
| database | Nome dell'istanza Teradata. | No (se nell'origine dell'attività è specificato "query") |
| tabella | Nome della tabella nell'istanza Teradata. | No (se nell'origine dell'attività è specificato "query") |

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
> `RelationalTable`dataset type è ancora supportato. Tuttavia, è consigliabile usare il nuovo set di dati.

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
>Per caricare i dati da Teradata in modo efficiente tramite il partizionamento dei dati, vedere la sezione [Copia parallela da Teradata.](#parallel-copy-from-teradata)

Per copiare i dati da Teradata, nella sezione **dell'origine dell'attività** di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su `TeradataSource`. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`.<br>Quando si abilita il carico partizionato, è necessario associare tutti i parametri di partizione incorporati corrispondenti nella query. Per esempi, vedere la sezione [Copia parallela da Teradata.For](#parallel-copy-from-teradata) examples, see the Parallel copy from Teradata section. | No (se è specificata la tabella nel set di dati) |
| partizioniOpzioni | Specifica le opzioni di partizionamento dei dati utilizzate per caricare i dati da Teradata. <br>I valori consentiti sono: **None (impostazione** predefinita), **Hash** e **DynamicRange**.<br>Quando un'opzione di partizione `None`è abilitata (ovvero no ), il grado di parallelismo [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) per il caricamento simultaneo dei dati da Teradata è controllato dall'impostazione dell'attività di copia. | No |
| partitionImpostazioni | Specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione `None`partizione non è . | No |
| partitionColumnName (nome di colonna) | Specificare il nome della colonna di origine che verrà utilizzata dalla partizione dell'intervallo o dalla partizione hash per la copia parallela. Se non specificato, l'indice primario della tabella viene rilevato automaticamente e utilizzato come colonna di partizione. <br>Applicare quando l'opzione di partizione è `Hash` o `DynamicRange`. Se si utilizza una query per `?AdfHashPartitionCondition` recuperare `?AdfRangePartitionColumnName` i dati di origine, hook o nella clausola WHERE. Vedere l'esempio nella sezione [Copia parallela da Teradata.See](#parallel-copy-from-teradata) example in Parallel copy from Teradata section. | No |
| partitionUpperBound | Valore massimo della colonna della partizione in cui copiare i dati. <br>Applicare quando l'opzione partizione è `DynamicRange`. Se si utilizza la query `?AdfRangePartitionUpbound` per recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Teradata.For](#parallel-copy-from-teradata) an example, see the Parallel copy from Teradata section. | No |
| partizioneLowerBound | Valore minimo della colonna della partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per `?AdfRangePartitionLowbound` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Teradata.For](#parallel-copy-from-teradata) an example, see the Parallel copy from Teradata section. | No |

> [!NOTE]
>
> `RelationalSource`l'origine della copia del tipo è ancora supportata, ma non supporta il nuovo carico parallelo incorporato da Teradata (opzioni di partizione). Tuttavia, è consigliabile usare il nuovo set di dati.

**Esempio: copiare i dati utilizzando una query di base senza partizioneExample: copy data by using a basic query without partition**

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

Il connettore Teradata di Data Factory fornisce il partizionamento dei dati incorporato per copiare i dati da Teradata in parallelo. Le opzioni di partizionamento dei dati sono disponibili nella tabella **Origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-teradata/connector-teradata-partition-options.png)

Quando si abilita la copia partizionata, Data Factory esegue query parallele sull'origine Teradata per caricare i dati in base alle partizioni. Il grado parallelo è [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controllato dall'impostazione dell'attività di copia. Ad esempio, se `parallelCopies` si imposta su quattro, Data Factory genera ed esegue contemporaneamente quattro query in base all'opzione e alle impostazioni della partizione specificata e ogni query recupera una parte dei dati dai dati Teradata.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, soprattutto quando si carica grandi quantità di dati dai Teradata. Di seguito sono riportate le configurazioni consigliate per scenari diversi. Quando si copiano dati nell'archivio dati basato su file, viene comandato di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni suggerite                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pieno carico da tavolo di grandi dimensioni.                                   | **Opzione partizione**: Hash. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente la colonna dell'indice primario, applica un hash su di essa e copia i dati in base alle partizioni. |
| Caricare grandi quantità di dati utilizzando una query personalizzata.                 | **Opzione partizione**: Hash.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`: .<br>**Colonna partizione**: Specificare la colonna utilizzata per applicare la partizione hash. Se non specificato, Data Factory rileva automaticamente la colonna PK della tabella specificata nel set di dati Teradata.<br><br>Durante l'esecuzione, `?AdfHashPartitionCondition` Data Factory viene sostituito con la logica della partizione hash e viene inviato a Teradata. |
| Caricare grandi quantità di dati utilizzando una query personalizzata, con una colonna intera con un valore distribuito uniformemente per il partizionamento dell'intervallo. | **Opzioni di partizione**: Partizione a intervallo dinamico.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. È possibile partizionare in base alla colonna con tipo di dati integer.<br>**Partizione limite superiore** e **limite inferiore della partizione:** specificare se si desidera filtrare in base alla colonna della partizione per recuperare i dati solo tra l'intervallo inferiore e superiore.<br><br>Durante l'esecuzione, `?AdfRangePartitionColumnName`Data `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` sostituisce , e con il nome di colonna effettivo e gli intervalli di valori per ogni partizione e invia a Teradata. <br>Ad esempio, se la colonna della partizione "ID" è impostata con il limite inferiore come 1 e il limite superiore come 80, con copia parallela impostata come 4, Data Factory recupera i dati da 4 partizioni. I loro ID sono compresi tra [1,20], [21, 40], [41, 60] e [61, 80], rispettivamente. |

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

**Esempio: query con partizione di intervallo dinamicoExample: query with dynamic range partition**

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

Quando si copiano dati da Teradata, vengono applicate le seguenti mappature. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati Teradata | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| BLOB |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |string |
| Clob |string |
| Data |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Non supportato. Applicare il cast esplicito nella query di origine. |
| Integer |Int32 |
| Interval Day |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Day To Hour |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Day To Minute |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Day To Second |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Hour |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Hour To Minute |Non supportato. Applicare il cast esplicito nella query di origine. |
| Intervallo - da ora a secondo |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Minute |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Minute To Second |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Month |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Second |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Year |Non supportato. Applicare il cast esplicito nella query di origine. |
| Interval Year To Month |Non supportato. Applicare il cast esplicito nella query di origine. |
| Number |Double |
| Periodo (Data) |Non supportato. Applicare il cast esplicito nella query di origine. |
| Periodo (ora) |Non supportato. Applicare il cast esplicito nella query di origine. |
| Periodo (tempo con fuso orario) |Non supportato. Applicare il cast esplicito nella query di origine. |
| Period (Timestamp) |Non supportato. Applicare il cast esplicito nella query di origine. |
| Periodo (timestamp con fuso orario) |Non supportato. Applicare il cast esplicito nella query di origine. |
| SmallInt |Int16 |
| Tempo |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |Datetime |
| Timestamp With Time Zone |Datetime |
| VarByte |Byte[] |
| VarChar |string |
| VarGraphic |Non supportato. Applicare il cast esplicito nella query di origine. |
| Xml |Non supportato. Applicare il cast esplicito nella query di origine. |


## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
