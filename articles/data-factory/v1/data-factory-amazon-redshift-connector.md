---
title: Spostare i dati da Amazon Redshift usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come spostare i dati da Amazon Redshift usando l'attività di copia di Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d423304c84bd03477f5e9ee2edb4763e2ae8d5b5
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Spostare i dati da Amazon Redshift usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-amazon-redshift-connector.md)
> * [Versione 2 - Anteprima](../connector-amazon-redshift.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Data Factory, che è disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Connettore Amazon Redshift nella versione 2](../connector-amazon-redshift.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da Amazon Redshift. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia. 

Data Factory consente attualmente solo lo spostamento dei dati da Amazon Redshift a un [archivio dati sink supportato](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Lo spostamento dei dati da altri archivi dati ad Amazon Redshift non è consentito.

> [!TIP]
> Per ottenere prestazioni ottimali quando si copiano grandi quantità di dati da Amazon Redshift, è possibile usare il comando **UNLOAD** di Redshift predefinito tramite Amazon Simple Storage Service (S3). Per informazioni dettagliate, vedere [Usare UNLOAD per copiare i dati da Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Prerequisiti
* Se si spostano i dati in un archivio dati locale, installare il [gateway di gestione dati](data-factory-data-management-gateway.md) su un computer locale. Concedere l'accesso per un gateway al cluster Amazon Redshift usando l'indirizzo IP del computer locale. Per istruzioni, vedere [Autorizzare l'accesso al cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Per spostare i dati in un archivio dati di Azure, vedere gli [intervalli di indirizzi IP di calcolo e gli intervalli SQL usati dai data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>introduttiva
È possibile creare una pipeline con l'attività di copia per spostare i dati da un'origine Amazon Redshift usando diversi strumenti e API.

Il modo più semplice per creare una pipeline è usare la Copia guidata di Azure Data Factory. Per una rapida procedura dettagliata di creazione di una pipeline mediante la copia guidata dei dati, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).

È anche possibile creare una pipeline usando il portale di Azure, Visual Studio, Azure PowerShell o altri strumenti. Per creare la pipeline possono essere usati anche modelli di Azure Resource Manager, l'API .NET o l'API REST. Per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare i servizi collegati per collegare gli archivi dati di input e output alla data factory.
2. Creare i set di dati per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una pipeline con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la Copia guidata, le definizioni JSON per queste entità di Data Factory vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione dell'API .NET, usare il formato JSON per definire le entità di Data Factory. La sezione [Esempio JSON: Copiare dati da Amazon Redshift all'archivio BLOB di Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) mostra le definizioni JSON per le entità di Data Factory usate per copiare dati da un archivio dati di Amazon Redshift.

Le sezioni seguenti descrivono le proprietà JSON che vengono usate per definire entità di Data Factory per Amazon Redshift.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente include le descrizioni degli elementi JSON specifici di un servizio collegato Amazon Redshift.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| **type** |Questa proprietà deve essere impostata su **AmazonRedshift**. |Sì |
| **server** |Indirizzo IP o nome host del server Amazon Redshift. |Sì |
| **port** |Il numero della porta TCP che il server Amazon Redshift usa per ascoltare le connessioni client. |No (il valore predefinito è 5439) |
| **database** |Nome del database Amazon Redshift. |Sì |
| **username** |Nome dell'utente che ha accesso al database. |Sì |
| **password** |La password per l'account utente. |Sì |

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md). Le sezioni **structure**, **availability** e **policy** sono simili per tutti i tipi di set di dati. Tra gli esempi di tipi di set di dati sono inclusi gli archivi di Azure SQL, gli archivi BLOB di Azure e gli archivi tabelle di Azure.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio. La sezione **typeProperties** per un set di dati di tipo **RelationalTable**, che comprende il set di dati Amazon Redshift, presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| **tableName** |Nome della tabella nel database Amazon Redshift a cui fa riferimento il servizio collegato. |No (se è specificata la proprietà **query** di un'attività di copia di tipo **RelationalSource**) |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo [Creating Pipelines](data-factory-create-pipelines.md) (Creazione di pipeline). Per tutti i tipi di attività sono disponibili le proprietà **name**, **description**, tabella **inputs**, tabella **outputs** e **policy**. Le proprietà disponibili nella sezione **typeProperties** variano per ogni tipo di attività. Per l'attività di copia, le proprietà variano in base ai tipi di origini dati e sink.

Per un'attività di copia, quando l'origine è di tipo **AmazonRedshiftSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| **query** | Usare la query personalizzata per leggere i dati. |No (se è specificata la proprietà **tableName** di un set di dati) |
| **redshiftUnloadSettings** | Contiene il gruppo di proprietà quando si usa il comando **UNLOAD** di Redshift. | No |
| **s3LinkedServiceName** | Amazon S3 da usare come archivio provvisorio. Il servizio collegato viene specificato usando un nome di Azure Data Factory di tipo **AwsAccessKey**. | Obbligatoria quando si usa la proprietà **redshiftUnloadSettings** |
| **bucketName** | Indica il bucket Amazon S3 da usare per archiviare i dati provvisori. Se questa proprietà non viene specificata, l'attività di copia genera automaticamente un bucket. | Obbligatoria quando si usa la proprietà **redshiftUnloadSettings** |

In alternativa, è possibile usare il tipo **RelationalSource**, che include Amazon Redshift, con la proprietà seguente nella sezione **typeProperties**. Si noti che questo tipo di origine non supporta il comando **UNLOAD** di Redshift.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| **query** |Usare la query personalizzata per leggere i dati. | No (se è specificata la proprietà **tableName** di un set di dati) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usare UNLOAD per copiare i dati da Amazon Redshift

Il comando [**UNLOAD**](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) di Amazon Redshift scarica i risultati di una query in uno o più file su Amazon S3. Questo comando è consigliato da Amazon per la copia di set di dati di grandi dimensioni da Redshift.

**Esempio: Copiare dati da Amazon Redshift ad Azure SQL Data Warehouse**

Questo esempio copia i dati da Amazon Redshift ad Azure SQL Data Warehouse. L'esempio usa il comando **UNLOAD** di Redshift, i dati della copia di staging e Microsoft PolyBase.

Per questo caso d'uso di esempio, l'attività di copia prima scarica i dati da Amazon Redshift ad Amazon S3, in base alla configurazione dell'opzione **redshiftUnloadSettings**. I dati vengono quindi copiati da Amazon S3 all'archiviazione BLOB di Azure, come specificato nell'opzione **stagingSettings**. PolyBase carica infine i dati in SQL Data Warehouse. Tutti i formati provvisori vengono gestiti dall'attività di copia.

![Flusso di lavoro di copia da Amazon Redshift a SQL Data Warehouse](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Esempio JSON: Copiare dati da Amazon Redshift all'archiviazione BLOB di Azure
Questo esempio illustra come copiare dati da un database Amazon Redshift in Archiviazione BLOB di Azure. I dati possono essere copiati direttamente in un [sink supportato](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia.  

L'esempio include le entità di Data factory seguenti:

* Un servizio collegato di tipo [AmazonRedshift](#linked-service-properties)
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#dataset-properties)
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa le proprietà [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties)

L'esempio copia i dati dai risultati della query in Amazon Redshift in un BLOB di Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritte nelle sezioni riportate dopo le definizioni di entità.

**Servizio collegato Amazon Redshift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input Redshift Amazon**

La proprietà **external** viene impostata su "true" per comunicare al servizio Data Factory che il set di dati è esterno alla data factory. Questa impostazione della proprietà indica che il set di dati non viene generato da un'attività nella data factory. Impostare la proprietà su true in un set di dati di input non generato da un'attività nella pipeline.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora impostando la proprietà **frequency** su "Hour" e **interval** su 1. La proprietà **folderPath** del BLOB viene valutata in modo dinamico. Il valore della proprietà è basato sull'ora di inizio della sezione che viene elaborata. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Attività di copia in una pipeline con un'origine Amazon Redshift (di tipo RelationalSource) e un sink BLOB di Azure**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e di output. La pipeline è pianificata per essere eseguita ogni ora. Nella definizione JSON per la pipeline il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare dall'ultima ora.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Tipo di mappatura di Amazon Redshift
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni dei tipi automatiche dai tipi di origine ai tipi di sink. I tipi vengono convertiti usando un approccio in due passaggi:

1. Conversione dal tipo di origine nativo al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando l'attività di copia converte i dati da un tipo Amazon Redshift in un tipo .NET, vengono usati i mapping seguenti:

| Tipo Amazon Redshift | Tipo .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |DECIMAL |
| REAL |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni su come eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Letture ripetibili da origini relazionali
Quando si copiano dati da un archivio dati relazionale è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare la proprietà **policy** di ripetizione per un set di dati per poter rieseguire una sezione in caso di errore. Assicurarsi che vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Verificare anche che vengano letti gli stessi dati indipendentemente da come viene eseguita nuovamente la sezione. Per altre informazioni, vedere [Letture ripetibili da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Per informazioni sui fattori chiave che influiscono sulle prestazioni dell'attività di copia e sui modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate sulla creazione di una pipeline con l'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

