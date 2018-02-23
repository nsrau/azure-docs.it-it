---
title: Copiare dati da/verso Oracle con Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da e verso database Oracle locali tramite Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82fe637b46decfc9c8d09b5c7e03f328a8636263
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Copiare dati da/verso un database Oracle locale con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-onprem-oracle-connector.md)
> * [Versione 2 - Anteprima](../connector-oracle.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni sul [connettore Oracle nella versione 2](../connector-oracle.md).


In questo articolo viene illustrato come usare l'attività di copia in Azure Data Factory per spostare i dati da e verso un database Oracle locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare i dati **da un database di Oracle** negli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **in un database di Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>prerequisiti
Data Factory supporta la connessione a origini Oracle locali mediante il gateway di gestione dati. Vedere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per informazioni sul Gateway di gestione dati e l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per istruzioni dettagliate su come configurare un gateway e una pipeline di dati per spostare i dati.

Il gateway è necessario anche se il database Oracle è ospitato in una macchina virtuale IaaS di Azure. È possibile installare il gateway nella stessa VM IaaS dell'archivio dati o in una macchina virtuale diversa, purché il gateway possa connettersi al database.

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Versioni supportate e installazione
Il connettore Oracle supporta due versioni di driver:

- **Driver Microsoft per Oracle (scelta consigliata)**: a partire dal Gateway di gestione dati versione 2.7, un driver Microsoft per Oracle viene installato automaticamente con il gateway, quindi non è necessario di gestire ulteriormente i driver per stabilire la connettività a Oracle ed è possibile inoltre ottenere migliori prestazioni di copia usando questo driver. Di seguito sono indicate le versioni dei database di Oracle supportate:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> Il driver Microsoft per Oracle supporta attualmente solo la copia dei dati da Oracle ma non la scrittura in Oracle. Si noti che la funzionalità di connessione di test nella scheda Diagnostica del Gateway di gestione dati supporta questo driver. In alternativa, è possibile usare la procedura guidata di copia per convalidare la connettività.
>

- **Provider di dati Oracle per .NET:** è possibile scegliere di usare il Provider di dati Oracle per copiare i dati da/in Oracle. Questo componente è incluso in [Oracle Data Access Components for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installare la versione appropriata (32/64 bit) nel computer in cui è installato il gateway. [Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) può accedere a Oracle Database 10g Release 2 o versione successiva.

    Se si sceglie di installare XCopy, eseguire la procedura indicata nel file readme.htm. È consigliabile scegliere il programma di installazione con l'interfaccia utente (non XCopy).

    Dopo avere installato il provider, **riavviare** il servizio host di Gateway di gestione dati nel computer mediante l'applet Services (o) Gestione configurazione di Gateway di gestione di dati.  

Se si usa la copia guidata per creare la pipeline di copia, il tipo di driver sarà determinato automaticamente. Verrà usato il driver Microsoft per impostazione predefinita, a meno che la versione del gateway sia inferiore a 2.7 o se si sceglie Oracle come sink.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un database di Oracle locale usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un database di Oralce in un archivio BLOB di Azure, creare due servizi collegati per collegare il database di Oracle e l'account di archiviazione di Azure alla data factory. Per le proprietà del servizio collegato specifiche per Oracle, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties).
3. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente si crea un set di dati per specificare la tabella nel database di Oracle che contiene i dati di input. Si crea anche un altro set di dati per specificare il contenitore BLOB e la cartella che contiene i dati copiati dal database di Oracle. Per le proprietà del set di dati specifiche per Oracle, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
4. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa OracleSource come origine e BlobSink come sink per l'attività di copia. Analogamente, se si effettua la copia dall'archivio BLOB di Azure al database di Oracle, usare BlobSource e OracleSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche per il database di Oracle, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per le entità di Data Factory che vengono usate per copiare i dati verso e dal database di Oracle locale, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-oracle-database) in questo articolo.

Nelle sezioni seguenti sono disponibili le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità della Data Factory:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Oracle.

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su: **OnPremisesOracle** |Sì |
| driverType | Specificare il driver da usare per copiare i dati da/verso il database Oracle. I valori consentiti sono **Microsoft** o **ODP** (impostazione predefinita). Per informazioni dettagliate sui driver, vedere la sezione [Versione e installazione supportate](#supported-versions-and-installation). | No  |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database Oracle per la proprietà connectionString. | Sì |
| gatewayName | Nome del gateway usato per connettersi al server Oracle locale |Sì |

**Esempio: mediante il driver Microsoft:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Esempio: mediante il driver ODP**

Per informazioni sui formati consentiti, vedere [questo sito](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (Oracle, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo OracleTable presenta le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database Oracle a cui fa riferimento il servizio collegato. |No, se **oracleReaderQuery** di **OracleSource** è specificato |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

### <a name="oraclesource"></a>OracleSource
Nell'attività di copia con origine di tipo **OracleSource** sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| oracleReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable <br/><br/>Se non specificato, l'istruzione SQL eseguita è: selezionare * da MyTable |No (se **tableName** di **set di dati** è specificato) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** supporta le proprietà seguenti:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No  |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 100) |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. |Istruzione di query. |No  |
| sliceIdentifierColumnName |Specificare il nome della colonna per l'attività di copia da riempire con l'identificatore di sezione generato automaticamente, che viene usato per eliminare i dati di una sezione specifica quando viene nuovamente eseguita. |Nome di colonna di una colonna con tipo di dati binario (32). |No  |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Esempi JSON per la copia dei dati da e verso un database di Oracle
L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un database Oracle in un archivio BLOB di Azure e viceversa. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Esempio: Copiare i dati da Oracle a BLOB di Azure

L'esempio include le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) come origine e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) come sink.

L'esempio copia i dati da una tabella di un database Oracle locale a un BLOB ogni ora. Per altre informazioni sulle varie proprietà usate nell'esempio, vedere la documentazione nelle sezioni che seguono gli esempi.

**Servizio collegato Oracle:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Set di dati di input Oracle:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Oracle e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo di **origine** è impostato su **OracleSource** e il tipo di **sink** è impostato su **BlobSink**.  La query SQL specificata con la proprietà **oracleReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Esempio: Copiare i dati dal BLOB di Azure in Oracle
Questo esempio illustra come copiare dati da un archivio BLOB di Azure a un database Oracle locale. I dati possono tuttavia essere copiati **direttamente** da qualsiasi origine dichiarata [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

L'esempio include le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) come origine e [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) come sink.

L'esempio copia i dati da un BLOB a una tabella nel database Oracle locale ogni ora. Per altre informazioni sulle varie proprietà usate nell'esempio, vedere la documentazione nelle sezioni che seguono gli esempi.

**Servizio collegato Oracle:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Set di dati di input del BLOB di Azure**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti di anno, mese e giorno della data/ora di inizio e il nome file usa la parte relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Set di dati di output Oracle:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Oracle. È necessario creare la tabella in Oracle con lo stesso numero di colonne contenute nel file con estensione csv del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
### <a name="problem-1-net-framework-data-provider"></a>Problema 1: provider di dati .NET Framework

Viene visualizzato il **messaggio di errore** seguente:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Possibili cause:**

1. Non è stato installato il Provider di dati Framework .NET per Oracle.
2. Il Provider di dati Framework .NET per Oracle è stato installato nel Framework .NET 2.0 e non è disponibile nelle cartelle del Framework.NET 4.0.

**Risoluzione/Soluzione alternativa:**

1. Se non è stato installato il provider .NET per Oracle, [installarlo](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e ripetere lo scenario.
2. Se viene visualizzato il messaggio di errore anche dopo l'installazione del provider, eseguire questa procedura:
   1. Aprire la configurazione del computer di .NET 2.0 dalla cartella: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Cercare **Provider di dati Oracle per .NET** per trovare una voce, come illustrato nell'esempio seguente in **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Provider di dati Oracle per .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />"
3. Copiare questa voce nel file .config del computer nella seguente cartella v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e modificare la versione a 4.xxx.x.x.
4. Installare "<percorso di installazione di ODP.NET>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" nella Global Assembly Cache eseguendo `gacutil /i [provider path]`.## Suggerimenti per la risoluzione dei problemi

### <a name="problem-2-datetime-formatting"></a>Problema 2: formattazione di DateTime

Viene visualizzato il **messaggio di errore** seguente:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Risoluzione/Soluzione alternativa:**

Potrebbe essere necessario modificare la stringa di query nell'attività di copia in base al modo in cui le date sono configurate nel database Oracle, come illustrato nell'esempio seguente (usando la funzione to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapping dei tipi per Oracle
Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da Oracle, vengono usati i mapping seguenti dal tipo di dati Oracle al tipo .NET e viceversa.

| Tipo di dati Oracle | Tipo di dati .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(supportato solo in Oracle 10g e versioni successive quando si usa il driver Microsoft) |
| CHAR |string |
| CLOB |string |
| DATE |Datetime |
| FLOAT |Decimal, String (se la precisione > 28) |
| INTEGER |Decimal, String (se la precisione > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |Intervallo di tempo |
| LONG |string |
| LONG RAW |Byte[] |
| NCHAR |string |
| NCLOB |string |
| NUMBER |Decimal, String (se la precisione > 28) |
| NVARCHAR2 |string |
| RAW |Byte[] |
| ROWID |string |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |Datetime |
| TIMESTAMP WITH TIME ZONE |Datetime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |string |
| XML |string |

> [!NOTE]
> I tipi di dati **INTERVAL YEAR TO MONTH** e **INTERVAL DAY TO SECOND** non sono supportati quando si usa il driver Microsoft.

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali
Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
