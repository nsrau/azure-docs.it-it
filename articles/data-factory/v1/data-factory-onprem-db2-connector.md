---
title: Spostare dati da DB2 mediante Azure Data Factory | Microsoft Docs
description: "Informazioni su come spostare dati da un database DB2 locale mediante l'attività di copia di Azure Data Factory"
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
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 17ffd0de41964736d2f59b0cf891d0c6b2e7d16b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Spostare dati da DB2 mediante l'attività di copia di Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-onprem-db2-connector.md)
> * [Versione 2 - Anteprima](../connector-db2.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni sul [connettore DB2 nella versione 2](../connector-db2.md).


Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da un database DB2 locale a un altro archivio dati. È possibile copiare dati in qualsiasi archivio che sia elencato come un sink supportato nell'articolo sulle [attività di spostamento dati di Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Questo argomento si basa sull'articolo relativo a Data Factory, che offre una panoramica dello spostamento dei dati mediante l'attività di copia ed elenca le combinazioni di archivi dati supportati. 

Data Factory consente attualmente solo lo spostamento dei dati da un database DB2 a un [archivio dati sink supportato](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Lo spostamento dei dati da altri archivi dati in un database DB2 non è consentito.

## <a name="prerequisites"></a>prerequisiti
Data Factory supporta la connessione a database DB2 locali mediante il [gateway di gestione dati](data-factory-data-management-gateway.md). Per istruzioni passo per passo su come configurare il gateway di una pipeline di dati per spostare i dati, vedere [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

Un gateway è necessario anche se il DB2 è ospitato in una VM IaaS di Azure. È possibile installare il gateway nella stessa VM IaaS dell'archivio dati. Se il gateway può connettersi al database, è possibile installare il gateway su una VM diversa.

Il gateway di gestione dati offre un driver DB2 integrato, perciò non è necessario installare manualmente un driver per copiare dati da DB2.

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere l'articolo [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).


## <a name="supported-versions"></a>Versioni supportate
Il connettore Data Factory DB2 supporta le piattaforme e le versioni di IBM DB2 seguenti con DRDA (Distributed Relational Database Architecture) SQL Access Manager versione 9, 10 e 11:

* IBM DB2 per z/OS versione 11.1
* IBM DB2 per z/OS versione 10.1
* IBM DB2 per i (AS400) versione 7.2
* IBM DB2 per i (AS400) versione 7.1
* IBM DB2 per Linux, UNIX e Windows (LUW) versione 11
* IBM DB2 per LUW versione 10.5
* IBM DB2 per LUW versione 10.1

> [!TIP]
> Se si riceve il messaggio d'errore "Non è stato trovato il pacchetto corrispondente a una richiesta di esecuzione dell'istruzione SQL. SQLSTATE=51002 SQLCODE=-805", il motivo è che non viene creato un pacchetto necessario per un utente normale nel sistema operativo. Per risolvere il problema seguire queste istruzioni per il tipo di server DB2 in uso:
> - DB2 per i (AS400): consente all'utente esperto di creare una raccolta per l'utente normale prima di eseguire l'attività di copia. Per creare la raccolta usare il comando: `create collection <username>`
> - DB2 per z/OS o LUW: usare un account con privilegi elevati, ovvero utente esperto o amministratore con autorità di pacchetto e autorizzazioni BIND, BINDADD, GRANT EXECUTE TO PUBLIC, per eseguire l'attività di copia una volta. Il pacchetto necessario viene creato automaticamente durante la copia. In seguito è possibile tornare a essere un utente normale per le successive esecuzioni delle operazioni di copia.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con un'attività di copia per spostare i dati da un archivio dati DB2 usando diversi strumenti e API: 

- Il modo più semplice per creare una pipeline è usare la Copia guidata di Azure Data Factory. Per una rapida procedura dettagliata di creazione di una pipeline mediante la copia guidata dei dati, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md). 
- Per creare una pipeline è possibile anche usare strumenti come il portale di Azure, Visual Studio, Azure PowerShell, un modello di Azure Resource Manager, l'API .NET e l'API REST. Per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i servizi collegati per collegare gli archivi dati di input e output alla data factory.
2. Creare i set di dati per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una pipeline con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la Copia guidata, le definizioni JSON per i servizi collegati, i set di dati e le entità pipeline di data factory vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione dell'API .NET, usare il formato JSON per definire le entità di Data Factory. La sezione [Esempio JSON: Copiare dati da DB2 a BLOB di Azure](#json-example-copy-data-from-db2-to-azure-blob) mostra le definizioni JSON per le entità di Data Factory utilizzate per copiare dati da un archivio dati DB2 locale.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di Data Factory specifiche di un archivio dati DB2.

## <a name="db2-linked-service-properties"></a>Proprietà del servizio collegato DB2
La tabella seguente elenca le proprietà JSON che sono specifiche di un servizio collegato DB2.

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| **type** |Questa proprietà deve essere impostata su **OnPremisesDb2**. |Sì |
| **server** |Il nome del server DB2. |Sì |
| **database** |Il nome del database DB2. |Sì |
| **schema** |Il nome dello schema nel database DB2. Questa proprietà fa distinzione tra maiuscole e minuscole. |No  |
| **authenticationType** |Il tipo di autenticazione utilizzato per connettersi al database DB2. I valori possibili sono: anonima, di base e Windows. |Sì |
| **username** |Il nome dell'account utente, se si usa l'autenticazione di base o di Windows. |No  |
| **password** |La password per l'account utente. |No  |
| **gatewayName** |Il nome del gateway che il servizio Data factory deve usare per connettersi al database DB2 locale. |Sì |

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md). Le sezioni come **struttura**, **disponibilità** e **policy** di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, archiviazione BLOB di Azure, archiviazione tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **RelationalTable**, che comprende il set di dati DB2, presenta la proprietà seguente:

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| **tableName** |Il nome della tabella nell'istanza del database DB2 a cui il servizio collegato fa riferimento. Questa proprietà fa distinzione tra maiuscole e minuscole. |No (se è specificata la proprietà **query** di un'attività di copia di tipo **RelationalSource**) |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco delle sezioni e delle proprietà disponibili per la definizione delle attività di copia, vedere l'articolo relativo alla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili le proprietà dell'attività di copia, come **name**, **description**, tabella **inputs**, tabella **outputs** e **policy**. Le proprietà disponibili nella sezione **typeProperties** dell'attività variano per ogni tipo di attività. Per l'attività di copia, le proprietà variano in base ai tipi di origini dati e sink.

Per le attività di copia con origine di tipo **RelationalSource** (che comprende DB2), sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| **query** |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `"query": "select * from "MySchema"."MyTable""` |No (se è specificata la proprietà **tableName** di un set di dati) |

> [!NOTE]
> I nomi di schemi e tabelle fanno distinzione tra maiuscole e minuscole. Nell'istruzione della query racchiudere i nomi di proprietà fra virgolette doppie ("").

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Esempio JSON: Copiare dati da DB2 a BLOB di Azure
Questo esempio fornisce le definizioni JSON di esempio da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). L'esempio illustra come copiare dati da un database DB2 nell'archivio BLOB. Tuttavia, è possibile copiare i dati in [qualsiasi tipo di sink di archivio dati supportato](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia di Azure Data Factory.

L'esempio include le entità della data factory seguenti:

- Un servizio collegato DB2 di tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Un servizio collegato di archiviazione BLOB di Azure di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa le proprietà [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

L'esempio copia i dati da un risultato della query in un database DB2 a un BLOB di Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritte nelle sezioni riportate dopo le definizioni di entità.

Innanzitutto installare e configurare un gateway dati. Le istruzioni si trovano nell'articolo [Spostare dati tra percorsi locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato DB2**

```json
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

**Servizio collegato di archiviazione BLOB di Azure**

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

**Set di dati di input DB2**

L'esempio presuppone che sia stata creata una tabella in DB2 denominata "MyTable" contenente una colonna denominata "timestamp" per i dati di una serie temporale.

La proprietà **external** è impostata su "true". Questa impostazione comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è generato da un'attività al suo interno. Si noti che il valore della proprietà **type** è impostato su **RelationalTable**.


```json
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

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora impostando la proprietà **frequency** su "Hour" e **interval** su 1. La proprietà **folderPath** per il BLOB viene valutata dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
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

**Pipeline con l'attività di copia**

La pipeline contiene un'attività di copia configurata per usare set di dati di input e output specificati e programmata per essere eseguita ogni ora. Nella definizione JSON per la pipeline il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** seleziona i dati dalla tabella "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
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

## <a name="type-mapping-for-db2"></a>Mapping dei tipi per DB2
Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipo automatiche dai tipi di origine ai tipi di sink usando l'approccio in due passaggi seguente:

1. Conversione dal tipo di origine nativo al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando l'attività di copia converte i dati da un tipo DB2 in un tipo .NET, vengono utilizzati i mapping seguenti:

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
| Data |Datetime |
| Tempo |Intervallo di tempo |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |string |
| VarChar |string |
| LongVarChar |string |
| DB2DynArray |string |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |string |
| VarGraphic |string |
| LongVarGraphic |string |
| Clob |string |
| BLOB |Byte[] |
| DbClob |string |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Data |Datetime |
| Tempo |Intervallo di tempo |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |string |

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni su come eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Letture ripetibili da origini relazionali
Quando si copiano dati da un archivio dati relazionale è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare la proprietà **policy** di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione e dal modo in cui la sezione viene rieseguita. Per altre informazioni, vedere [Letture ripetibili da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Per informazioni sui fattori chiave che influiscono sulle prestazioni dell'attività di copia e sui modi per ottimizzarle, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
