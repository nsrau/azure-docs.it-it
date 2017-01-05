---
title: Spostare i dati da DB2 | Microsoft Docs
description: Informazioni su come spostare i dati dal database di DB2 mediante Data factory di Azure
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: e4c1ef1d52a3e0618bed572695b7a9f2b4b46e88


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>Spostare i dati da DB2 mediante Data factory di Azure
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da DB2 a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data factory supporta la connessione a origini DB2 locali tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway.

> [!NOTE]
> Usare il gateway per connettersi a DB2 anche se è ospitato in una VM IaaS di Azure. Se si sta cercando di connettersi a un'istanza di DB2 ospitata nel cloud è anche possibile installare l'istanza del gateway nella VM IaaS.
>
>

Data factory supporta attualmente solo lo spostamento di dati da DB2 ad altri archivi dati, non da altri archivi dati a DB2.

## <a name="supported-versions"></a>Versioni supportate
Il connettore DB2 supporta le piattaforme e le versioni di IBM DB2 seguenti con DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versione 9, 10 e 11:

* IBM DB2 per z/OS 11.1
* IBM DB2 per z/OS 10.1
* IBM DB2 per i 7.2
* IBM DB2 per i 7.1
* IBM DB2 per LUW 11
* IBM DB2 per LUW 10.5
* IBM DB2 per LUW 10.1

Il Gateway di gestione dati offre un driver DB2 integrato, perciò non è necessario installare manualmente eventuali driver quando si copiano dati da DB2.

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .
>
>

## <a name="copy-data-wizard"></a>Copia dati guidata
Il modo più semplice per creare una pipeline che copia i dati da un database DB2 consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un database DB2 in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Esempio: Copiare i dati da DB2 a BLOB di Azure
Questo esempio illustra come copiare dati da un database DB2 locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

L'esempio include le entità di Data factory seguenti:

1. Un servizio collegato di tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati da un risultato della query in un database DB2 a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Innanzitutto, installare e configurare un gateway di gestione dati. Le istruzioni si trovano nell'articolo [Spostare dati tra percorsi locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato DB2:**

```JSON
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Set di dati di input DB2:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in DB2 e che contenga una colonna denominata "timestamp" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno. Si noti che il valore **type** è impostato su **RelationalTable**.

```JSON
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** seleziona i dati dalla tabella Orders.

```JSON
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="db2-linked-service-properties"></a>Proprietà del servizio collegato DB2
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato DB2.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà del tipo deve essere impostata su: **OnPremisesDB2** |Sì |
| server |Nome del server DB2. |Sì |
| database |Nome del database DB2. |Sì |
| schema |Nome dello schema nel database. Il nome dello schema fa distinzione tra maiuscole e minuscole. |No |
| authenticationType |Tipo di autenticazione usato per connettersi al database DB2. I valori possibili sono: anonima, di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base o Windows. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database DB2 locale. |Sì |

Vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati DB2 locale. 

## <a name="db2-dataset-type-properties"></a>Proprietà del tipo di set di dati DB2
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo RelationalTable (che comprende il set di dati DB2) presenta le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database DB2 a cui fa riferimento il servizio collegato. La proprietà tableName fa distinzione tra maiuscole e minuscole. |No (se la **query** di **RelationalSource** è specificata) |

## <a name="db2-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia DB2
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Per attività di copia con origine di tipo **RelationalSource** (che comprende DB2), sono disponibili le proprietà seguenti nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `"query": "select * from "MySchema"."MyTable""`. |No (se **tableName** di **set di dati** è specificato) |

> [!NOTE]
> I nomi di schemi e tabelle fanno distinzione tra maiuscole e minuscole. Racchiudere i nomi tra "" (virgolette doppie) nella query.  
>
>

**Esempio:**

```JSON
"query": "select * from "DB2ADMIN"."Customers""
```


[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Mapping dei tipi per DB2
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati in DB2 vengono usati i mapping seguenti dal tipo DB2 al tipo .NET.

| Tipo di database DB2 | Tipo di .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| BLOB |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).



<!--HONumber=Nov16_HO3-->


