---
title: Spostare dati da Teradata usando Azure Data Factory | Documentazione Microsoft
description: Informazioni su come il connettore Teradata per il servizio Data factory consente di spostare dati dal database Teradata
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 42012447f3e713510f5d11b4bb2478390e36cf09
ms.contentlocale: it-it
ms.lasthandoff: 03/29/2017

---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Spostare i dati da Teradata utilizzando Data factory di Azure
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un database Teradata locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un archivio dati Teradata locale a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento di dati da un archivio dati Teradata ad altri archivi dati, ma non da altri archivi dati a un archivio dati Teradata. 

## <a name="prerequisites"></a>Prerequisiti
Data factory supporta la connessione a origini Teradata locali tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway.

Il gateway è necessario anche se il database Teradata è ospitato in una macchina virtuale IaaS di Azure. È possibile installare il gateway nella stessa VM IaaS dell'archivio dati o in una macchina virtuale diversa, purché il gateway possa connettersi al database.

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Versioni supportate e installazione
Perché Gateway di gestione dati si connetta al database Teradata, è necessario installare il [provider di dati .NET per Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) versione 14 o successiva nello stesso sistema di Gateway di gestione dati. Sono supportate le versioni di Teradata a partire dalla 12.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da un archivio dati Cassandra usando diversi strumenti/API. 

- Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati. 
- È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di Data Factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory.  Per un esempio con le definizioni JSON per le entità di Data Factory usate per copiare dati da un archivio dati Teradata locale, vedere la sezione [Esempio JSON: Copiare dati da Teradata a BLOB di Azure](#json-example-copy-data-from-teradata-to-azure-blob) di questo articolo. 

Nelle sezioni seguenti sono disponibili le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità della Data Factory specifiche di un archivio dati Teradata:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Teradata.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà del tipo deve essere impostata su: **OnPremisesTeradata** |Sì |
| server |Nome del server Teradata. |Sì |
| authenticationType |Tipo di autenticazione usato per connettersi al database Teradata. I valori possibili sono: anonima, di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base o Windows. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database Teradata locale. |Sì |

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. Attualmente non sono disponibili proprietà di tipo supportate per il set di dati Teradata.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se l'origine è di tipo **RelationalSource** (che comprende Teradata), sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. |Sì |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Esempio JSON: Copiare dati da Teradata a BLOB di Azure
L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da Teradata in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.   

L'esempio include le entità di Data factory seguenti:

1. Un servizio collegato di tipo [OnPremisesTeradata](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati dai risultati della query nel database Teradata in un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il Gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato Teradata:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure:**

```json
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

**Set di dati di input Teradata:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Teradata e che contenga una colonna denominata "timestamp" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
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

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Mapping dei tipi per Teradata
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano i dati in Teradata vengono usati i mapping seguenti dal tipo Teradata al tipo .NET.

| Tipo di database Teradata | Tipo di .NET Framework |
| --- | --- |
| Char |String |
| Clob |String |
| Graphic |String |
| VarChar |String |
| VarGraphic |String |
| BLOB |Byte[] |
| Byte |Byte[] |
| VarByte |Byte[] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Decimal |Decimal |
| Double |Double |
| Integer |Int32 |
| Number |Double |
| SmallInt |Int16 |
| Date |DateTime |
| Time |TimeSpan |
| Time With Time Zone |String |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTimeOffset |
| Interval Day |TimeSpan |
| Interval Day To Hour |TimeSpan |
| Interval Day To Minute |TimeSpan |
| Interval Day To Second |TimeSpan |
| Interval Hour |TimeSpan |
| Interval Hour To Minute |TimeSpan |
| Intervallo - da ora a secondo |TimeSpan |
| Interval Minute |TimeSpan |
| Interval Minute To Second |TimeSpan |
| Interval Second |TimeSpan |
| Interval Year |String |
| Interval Year To Month |String |
| Interval Month |String |
| Period(Date) |String |
| Period(Time) |String |
| Period(Time With Time Zone) |String |
| Period(Timestamp) |String |
| Period(Timestamp With Time Zone) |String |
| Xml |String |

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

