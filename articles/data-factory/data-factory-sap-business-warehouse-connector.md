---
title: Spostare dati da SAP Business Warehouse usando Azure Data Factory | Microsoft Docs
description: Informazioni su come spostare dati da SAP Business Warehouse usando Azure Data Factory.
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
ms.openlocfilehash: 6a14e5e0cab25b26782ebd45b52aa7542b7e24d5
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Spostare dati da SAP Business Warehouse usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una pipeline di Azure Data Factory per spostare dati da SAP Business Warehouse a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate. Data Factory supporta attualmente solo lo spostamento di dati da SAP Business Warehouse ad altri archivi dati, non da altri archivi dati a SAP Business Warehouse.


## <a name="supported-versions-and-installation"></a>Versioni supportate e installazione
Questo connettore supporta SAP Business Warehouse versione 7.x. Supporta anche la copia di dati da Infocube e QueryCubes (incluse query BEx) tramite query MDX.

Per abilitare la connettività all'istanza di SAP BW, installare i componenti seguenti:
- **Gateway di gestione dati**: il servizio Data Factory supporta la connessione ad archivi dati locali (incluso SAP Business Warehouse) usando un componente denominato Gateway di gestione dati. Per informazioni su Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway, vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md). Il gateway è necessario anche se il database SAP Business Warehouse è ospitato in una macchina virtuale IaaS di Azure. È possibile installare il gateway nella stessa VM dell'archivio dati o in una macchina virtuale diversa, purché il gateway possa connettersi al database.
- **Libreria SAP NetWeaver** nel computer del gateway. È possibile ottenere la libreria SAP Netweaver dal proprio amministratore SAP o direttamente dall'[area per il download di software SAP](https://support.sap.com/swdc). Per ottenere il percorso di download della versione più recente, cercare **SAP Note #1025361**. Assicurarsi che l'architettura della libreria SAP NetWeaver (32 bit o 64 bit) corrisponda al tipo di installazione del gateway. Installare quindi tutti i file inclusi in SAP NetWeaver RFC SDK in base alla nota SAP. La libreria SAP NetWeaver è inclusa anche nell'installazione degli strumenti client SAP.

## <a name="supported-sinks"></a>Sink supportati
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella [Archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). È possibile spostare dati da SAP Business Warehouse a qualsiasi archivio dati sink supportato. 

## <a name="copy-data-wizard"></a>Copia di dati guidata
Il modo più semplice per creare una pipeline che consenta di copiare dati da SAP Business Warehouse a uno degli archivi dati sink supportati è ricorrere alla Copia di dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Questo esempio illustra come copiare dati da un database SAP Business Warehouse locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

> [!IMPORTANT]
> Questo esempio fornisce frammenti di codice JSON. Non include istruzioni dettagliate per la creazione della data factory. Le istruzioni dettagliate sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="sample-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Esempio: Copiare dati da SAP Business Warehouse a un archivio BLOB di Azure
L'esempio include le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [SapBw](#sap-bw-linked-service).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#sap-bw-dataset).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#sap-bw-source-in-copy-activity) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio i dati vengono copiati da un'istanza di SAP Business Warehouse a un BLOB di Azure a intervalli orari. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il Gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Servizio collegato SAP Business Warehouse
Questo servizio collegato collega l'istanza di SAP BW alla data factory. La proprietà type è impostata su **SapBw**. La sezione typeProperties fornisce informazioni di connessione per l'istanza di SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>Set di dati di input SAP BW
Questo set di dati definisce il set di dati SAP Business Warehouse. Per il set di dati Data Factory il tipo deve essere impostato su **RelationalTable**, mentre per il set di dati SAP BW non è attualmente necessario specificare alcuna proprietà relativa al tipo. La query presente nella definizione dell'attività di copia specifica i dati da leggere dall'istanza di SAP BW. 

Impostando la proprietà esterna su true si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

Le proprietà di frequenza e intervallo definiscono la pianificazione. In questo caso, i dati vengono letti dall'istanza di SAP BW ogni ora. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Questo set di dati definisce il set di dati di output di BLOB di Azure. La proprietà type è impostata su AzureBlob. La sezione typeProperties specifica dove devono essere archiviati i dati copiati dall'istanza di SAP BW. I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **RelationalSource** (per l'origine SAP BW) e il tipo di **sink** è impostato su **BlobSink**. La query specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-bw-linked-service"></a>Servizio collegato SAP BW
La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato SAP Business Warehouse (BW).

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
server | Nome del server in cui si trova l'istanza di SAP BW. | string | Sì
systemNumber | Numero del sistema SAP BW. | Numero decimale a due cifre rappresentato come stringa. | Sì
clientId | ID del client nel sistema SAP BW. | Numero decimale a tre cifre rappresentato come stringa. | Sì
username | Nome dell'utente che ha accesso al server SAP | string | Sì
password | Password per l'utente. | string | Sì
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi all'istanza di SAP BW locale. | string | Sì
encryptedCredential | Stringa di credenziali crittografata. | string | No

## <a name="sap-bw-dataset"></a>Set di dati SAP BW
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. Il set di dati SAP BW di tipo **RelationalTable** non supporta alcuna proprietà specifica del tipo. 


## <a name="sap-bw-source-in-copy-activity"></a>Origine SAP BW nell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se l'origine nell'attività di copia è di tipo **RelationalSource** (che include SAP BW), nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query | Specifica la query MDX che consente di leggere i dati dall'istanza di SAP BW. | Query MDX. | Sì |

### <a name="type-mapping-for-sap-bw"></a>Mapping dei tipi per SAP BW
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da SAP BW, vengono usati i mapping seguenti tra i tipi SAP BW e i tipi .NET.

Tipo di dati nel dizionario ABAP | Tipo di dati .Net
-------------------------------- | --------------
ACCP |    int
CHAR | String
CLNT | String
CURR | Decimale
CUKY | String
DEC | Decimale
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimale
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNITÀ | String
DATS | String
NUMC | String
TIMS | String

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

