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
ms.date: 05/16/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3815ce9879379c07204ce1294dc0fe55bd9ede53
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Spostare dati da SAP Business Warehouse usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-sap-business-warehouse-connector.md)
> * [Versione 2 - Anteprima](../connector-sap-business-warehouse.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni sul [connettore SAP Business Warehouse nella versione 2](../connector-sap-business-warehouse.md).


Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un database SAP Business Warehouse (BW) locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un archivio dati SAP Business Warehouse locale a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento di dati da SAP Business Warehouse ad altri archivi dati, non da altri archivi dati a un SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Versioni supportate e installazione
Questo connettore supporta SAP Business Warehouse versione 7.x. Supporta anche la copia di dati da Infocube e QueryCubes (incluse query BEx) tramite query MDX.

Per abilitare la connettività all'istanza di SAP BW, installare i componenti seguenti:
- **Gateway di gestione dati**: il servizio Data Factory supporta la connessione ad archivi dati locali (incluso SAP Business Warehouse) usando un componente denominato Gateway di gestione dati. Per informazioni su Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway, vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md). Il gateway è necessario anche se il database SAP Business Warehouse è ospitato in una macchina virtuale IaaS di Azure. È possibile installare il gateway nella stessa VM dell'archivio dati o in una macchina virtuale diversa, purché il gateway possa connettersi al database.
- **Libreria SAP NetWeaver** nel computer del gateway. È possibile ottenere la libreria SAP Netweaver dal proprio amministratore SAP o direttamente dall'[area per il download di software SAP](https://support.sap.com/swdc). Per ottenere il percorso di download della versione più recente, cercare **SAP Note #1025361**. Assicurarsi che l'architettura della libreria SAP NetWeaver (32 bit o 64 bit) corrisponda al tipo di installazione del gateway. Installare quindi tutti i file inclusi in SAP NetWeaver RFC SDK in base alla nota SAP. La libreria SAP NetWeaver è inclusa anche nell'installazione degli strumenti client SAP.

> [!TIP]
> Inserire le DLL estratte dall'SDK di NetWeaver RFC nella cartella system32.

## <a name="getting-started"></a>introduttiva
È possibile creare una pipeline con l'attività di copia che sposta i dati da un archivio dati Cassandra usando diversi strumenti/API. 

- Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati. 
- È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory.  Per un esempio con le definizioni JSON per le entità di Data Factory usate per copiare dati da un SAP Business Warehouse locale, vedere la sezione [Esempio JSON: Copiare dati da SAP Business Warehouse a BLOB di Azure](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) di questo articolo. 

Nelle sezioni seguenti sono disponibili le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità della Data Factory specifiche di un archivio dati SAP BW:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
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

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. Il set di dati SAP BW di tipo **RelationalTable** non supporta alcuna proprietà specifica del tipo. 


## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se l'origine nell'attività di copia è di tipo **RelationalSource** (che include SAP BW), nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query | Specifica la query MDX che consente di leggere i dati dall'istanza di SAP BW. | Query MDX. | Sì |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Esempio JSON: Copiare dati da SAP Business Warehouse a BLOB di Azure
L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Questo esempio illustra come copiare dati da un database SAP Business Warehouse locale a un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

> [!IMPORTANT]
> Questo esempio fornisce frammenti di codice JSON. Non include istruzioni dettagliate per la creazione della data factory. Le istruzioni dettagliate sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) .

L'esempio include le entità di Data factory seguenti:

1. Un servizio collegato di tipo [SapBw](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

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



### <a name="type-mapping-for-sap-bw"></a>Mapping dei tipi per SAP BW
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da SAP BW, vengono usati i mapping seguenti tra i tipi SAP BW e i tipi .NET.

Tipo di dati nel dizionario ABAP | Tipo di dati .Net
-------------------------------- | --------------
ACCP |  int
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

> [!NOTE]
> Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

