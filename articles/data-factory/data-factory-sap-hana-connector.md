---
title: Spostare dati da SAP HANA usando Azure Data Factory | Microsoft Docs
description: Informazioni su come spostare dati da SAP HANA usando Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 415e91ca2f1e47094fc8b64569b5cc29a706999a
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Spostare dati da SAP HANA usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una pipeline di Azure Data Factory per spostare dati da SAP HANA a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate. Data Factory supporta attualmente solo lo spostamento di dati da SAP HANA ad altri archivi dati, non da altri archivi dati a SAP HANA.

## <a name="supported-versions-and-installation"></a>Versioni supportate e installazione
Questo connettore supporta qualsiasi versione del database SAP HANA. Supporta anche la copia di dati da modelli di informazioni HANA (ad esempio, dalle viste di analisi e calcolo) e da tabelle Riga/Colonna tramite query SQL.

Per abilitare la connettività all'istanza di SAP HANA, installare i componenti seguenti:
- **Gateway di gestione dati**: il servizio Data Factory supporta la connessione ad archivi dati locali (incluso SAP HANA) usando un componente denominato Gateway di gestione dati. Per informazioni su Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway, vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md). Il gateway è necessario anche se il database SAP HANA è ospitato in una macchina virtuale IaaS di Azure. È possibile installare il gateway nella stessa VM dell'archivio dati o in una macchina virtuale diversa, purché il gateway possa connettersi al database.
- **Driver ODBC di SAP HANA** nel computer del gateway. È possibile scaricare il driver ODBC di SAP HANA dall'[area per il download di software SAP](https://support.sap.com/swdc). Per cercare il driver, usare la parola chiave **SAP HANA CLIENT for Windows**. 

## <a name="supported-sinks"></a>Sink supportati
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella [Archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). È possibile spostare dati da SAP HANA a qualsiasi archivio dati sink supportato. 

## <a name="copy-data-wizard"></a>Copia di dati guidata
Il modo più semplice per creare una pipeline che consenta di copiare dati da SAP HANA a uno degli archivi dati sink supportati è ricorrere alla Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline tramite il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Questo esempio illustra come copiare dati da un database SAP HANA locale a un archivio BLOB di Azure. I dati possono tuttavia essere copiati **direttamente** in qualsiasi sink elencato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

> [!IMPORTANT]
> Questo esempio fornisce frammenti di codice JSON. Non include istruzioni dettagliate per la creazione della data factory. Le istruzioni dettagliate sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="sample-copy-data-from-sap-hana-to-azure-blob"></a>Esempio: Copiare dati da SAP HANA a BLOB di Azure
L'esempio include le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [SapHana](#sap-hana-linked-service).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#sap-hana-dataset).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#sap-hana-source-in-copy-activity) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio i dati vengono copiati da un'istanza di SAP HANA a un BLOB di Azure a intervalli orari. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il Gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-hana-linked-service"></a>Servizio collegato SAP HANA
Questo servizio collegato collega l'istanza di SAP HANA alla data factory. La proprietà type è impostata su **SapHana**. La sezione typeProperties fornisce informazioni di connessione per l'istanza di SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Questo servizio collegato collega l'account di archiviazione di Azure alla data factory. La proprietà type è impostata su **AzureStorage**. La sezione typeProperties fornisce informazioni di connessione per l'account di archiviazione di Azure.

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

### <a name="sap-hana-input-dataset"></a>Set di dati input di SAP HANA

Questo set di dati definisce il set di dati di SAP HANA. Per il set di dati Data Factory il tipo deve essere impostato su **RelationalTable**, mentre per il set di dati SAP HANA non è attualmente necessario specificare alcuna proprietà relativa al tipo. La query presente nella definizione dell'attività di copia specifica i dati da leggere dall'istanza di SAP HANA. 

Impostando la proprietà esterna su true si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

Le proprietà di frequenza e intervallo definiscono la pianificazione. In questo caso, i dati vengono letti dall'istanza di SAP HANA ogni ora. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Set di dati di output del BLOB di Azure
Questo set di dati definisce il set di dati di output di BLOB di Azure. La proprietà type è impostata su AzureBlob. La sezione typeProperties specifica dove devono essere archiviati i dati copiati dall'istanza di SAP HANA. I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline con attività di copia

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** (per l'origine SAP HANA) e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-hana-linked-service"></a>Servizio collegato SAP HANA
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato SAP HANA.

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
server | Nome del server in cui si trova l'istanza di SAP HANA. Se il server usa una porta personalizzata, specificare `server:port`. | string | Sì
authenticationType | Tipo di autenticazione. | string. "Basic" o "Windows" | Sì 
username | Nome dell'utente che ha accesso al server SAP | string | Sì
password | Password per l'utente. | string | Sì
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi all'istanza di SAP HANA locale. | string | Sì
encryptedCredential | Stringa di credenziali crittografata. | string | No

## <a name="sap-hana-dataset"></a>Set di dati SAP HANA
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. Il set di dati SAP HANA di tipo **RelationalTable** non supporta alcuna proprietà specifica del tipo. 


## <a name="sap-hana-source-in-copy-activity"></a>Origine SAP HANA nell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se l'origine nell'attività di copia è di tipo **RelationalSource** (che include SAP HANA), nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query | Specifica la query SQL che consente di leggere i dati dall'istanza di SAP HANA. | Query SQL. | Sì |

### <a name="type-mapping-for-sap-hana"></a>Mapping dei tipi per SAP HANA
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da SAP HANA, vengono usati i mapping seguenti tra i tipi SAP HANA e i tipi .NET.

Tipo SAP HANA | Tipo basato su .Net
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Single
DOUBLE | Single
DECIMAL | Decimale
BOOLEAN | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TIME | Intervallo di tempo
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Limitazioni note
Quando si copiano dati da SAP HANA, è necessario tenere conto di alcune limitazioni:

- Le stringhe NVARCHAR vengono troncate alla lunghezza massima di 4000 caratteri Unicode
- SMALLDECIMAL non è supportato
- VARBINARY non è supportato
- Le date valide sono tra comprese tra il 30/12/1899 e il 31/12/9999

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

