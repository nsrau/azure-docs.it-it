---
title: Copiare dati da o verso Oracle usando Data Factory
description: Informazioni su come copiare dati da o verso database Oracle locali tramite Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 822713d67790906c972ad77a748ef8d52b871bc4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682431"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Copiare dati da o verso un database Oracle locale con Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](data-factory-onprem-oracle-connector.md)
> * [Versione 2 (corrente)](../connector-oracle.md)

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Azure Data Factory. Se si usa la versione corrente del servizio Azure Data Factory, vedere le informazioni sul [connettore Oracle nella versione 2](../connector-oracle.md).


In questo articolo viene illustrato come usare l'attività di copia in Azure Data Factory per spostare i dati da o verso un database Oracle locale. L'articolo si basa su [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dati tramite l'attività di copia.

## <a name="supported-scenarios"></a>Scenari supportati

È possibile copiare i dati *da un database di Oracle* negli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti *in un database di Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Prerequisiti

Data Factory supporta la connessione a origini Oracle locali mediante Gateway di gestione dati. Vedere [Gateway di gestione dati](data-factory-data-management-gateway.md) per altre informazioni su Gateway di gestione dati. Per istruzioni dettagliate su come configurare il gateway in una pipeline di dati per spostare i dati, vedere [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

Il gateway è necessario anche se il database Oracle è ospitato in una macchina virtuale IaaS (infrastruttura distribuita come servizio) di Azure. È possibile installare il gateway nella stessa macchina virtuale IaaS dell'archivio dati o in una macchina virtuale diversa, purché il gateway possa connettersi al database.

> [!NOTE]
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione e al gateway, vedere [Risoluzione dei problemi di gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versioni supportate e installazione

Il connettore Oracle supporta due versioni di driver:

- **Driver Microsoft per Oracle (scelta consigliata)** : a partire da Gateway di gestione dati versione 2.7, un driver Microsoft per Oracle viene installato automaticamente con il gateway. Non è necessario installare o aggiornare il driver per stabilire la connettività a Oracle. È inoltre possibile ottenere migliori prestazioni di copia usando questo driver. Sono supportate le versioni seguenti dei database Oracle:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Il server proxy Oracle non è supportato.

    > [!IMPORTANT]
    > Il driver Microsoft per Oracle supporta attualmente solo la copia dei dati da Oracle, ma non la scrittura in Oracle. La funzionalità di connessione di test nella scheda **Diagnostica** di Gateway di gestione dati non supporta questo driver. In alternativa, è possibile usare la Copia guidata per convalidare la connettività.
    >

- **Provider di dati Oracle per .NET:** è possibile usare il provider di dati Oracle per copiare i dati da o verso Oracle. Questo componente è incluso in [Oracle Data Access Components for Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Installare la versione corrispondente (32 bit o 64 bit) nel computer in cui è installato il gateway. Il [provider di dati Oracle per .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) può accedere a Oracle Database 10g Release 2 e versioni successive.

    Se si sceglie di **installare XCopy**, completare i passaggi descritti nel file readme.htm. È consigliabile selezionare il programma di installazione con l'interfaccia utente (non il programma di installazione XCopy).

    Dopo avere installato il provider, riavviare il servizio host di Gateway di gestione dati nel computer mediante l'applet dei servizi o Gestione configurazione di Gateway di gestione di dati.

Se si usa la Copia guidata per creare la pipeline di copia, il tipo di driver viene determinato automaticamente. Per impostazione predefinita, viene usato il driver Microsoft, a meno che la versione del gateway sia inferiore a 2.7 o si selezioni Oracle come sink.

## <a name="get-started"></a>Introduzione

È possibile creare una pipeline con un'attività di copia. La pipeline sposta i dati da o verso un database Oracle locale usando diversi strumenti o API.

Il modo più semplice per creare una pipeline è usare la Copia guidata. Vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata di creazione di una pipeline mediante la procedura guidata Copia dati.

Per creare una pipeline, è anche possibile usare uno degli strumenti seguenti: **Visual Studio**, **Azure PowerShell**, un **modello di Azure Resource Manager**, l' **API .NET**o l' **API REST**. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se si usano gli strumenti o le API, completare la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline.
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un database Oracle in un archivio BLOB di Azure, creare due servizi collegati per collegare il database Oracle e l'account di archiviazione di Azure alla data factory. Per le proprietà del servizio collegato specifiche per Oracle, vedere [Proprietà del servizio collegato](#linked-service-properties).
3. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio nel passaggio precedente si crea un set di dati per specificare la tabella nel database Oracle che contiene i dati di input. Si crea un altro set di dati per specificare il contenitore BLOB e la cartella che contiene i dati copiati dal database Oracle. Per le proprietà del set di dati specifiche per Oracle, vedere [Proprietà dei set di dati](#dataset-properties).
4. Creare una **pipeline** che abbia un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio precedente si usa **OracleSource** come origine e **BlobSink** come sink per l'attività di copia. Analogamente, se si effettua la copia dall'archivio BLOB di Azure a un database Oracle, usare **BlobSource** e **OracleSink** nell'attività di copia. Per le proprietà dell'attività di copia specifiche per un database Oracle, vedere [Proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, selezionare il collegamento per l'archivio dati nella sezione precedente.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di Data Factory vengono create automaticamente: servizi collegati, set di dati e pipeline. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory. Per esempi con definizioni JSON per le entità di Data Factory che vengono usate per copiare i dati da o verso un database Oracle locale, vedere gli esempi JSON.

Nelle sezioni seguenti sono disponibili informazioni dettagliate sulle proprietà JSON che vengono usate per definire le entità di Data Factory.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente descrive gli elementi JSON specifici del servizio collegato Oracle:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà **type** deve essere impostata su **OnPremisesOracle**. |Sì |
| driverType | Specificare il driver da usare per copiare i dati da o verso il database Oracle. I valori consentiti sono **Microsoft** e **ODP** (impostazione predefinita). Per informazioni dettagliate sui driver, vedere [Versioni supportate e installazione](#supported-versions-and-installation). | No |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database Oracle per la proprietà **connectionString**. | Sì |
| gatewayName | Nome del gateway usato per connettersi al server Oracle locale. |Sì |

**Esempio: mediante il driver Microsoft**

> [!TIP]
> Se viene visualizzato un errore che indica "ORA 01025: UPI parameter out of range" (Parametro UPI fuori intervallo) e la versione Oracle è la 8i, aggiungere `WireProtocolMode=1` alla stringa di connessione e riprovare:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Esempio: mediante il driver ODP**

Per informazioni sui formati consentiti, fare riferimento al [provider di dati Oracle per .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione di set di dati](data-factory-create-datasets.md).

Le sezioni di un file JSON del set di dati, tra cui struttura, disponibilità e criteri, sono simili per tutti i tipi di set di dati (ad esempio, per Oracle, Archiviazione BLOB di Azure e Archiviazione tabelle di Azure).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **OracleTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nel database Oracle a cui fa riferimento il servizio collegato. |No (se è specificato **oracleReaderQuery** o **OracleSource**) |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Creating pipelines](data-factory-create-pipelines.md) (Creazione di pipeline).

Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano per ogni tipo di attività. Le proprietà dell'attività di copia variano in base al tipo di origine e sink.

### <a name="oraclesource"></a>OracleSource

Nell'attività di copia con origine di tipo **OracleSource** sono disponibili le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| oracleReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio, "select \* from **MyTable**". <br/><br/>Se non specificato, viene eseguita questa istruzione SQL: "select \* from **MyTable**" |No<br />(se è specificato **tableName** nel **set di dati**) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |**timespan**<br/><br/> Ad esempio: 00:30:00 (30 minuti) |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge il valore di **writeBatchSize**. |Numero intero (numero di righe) |No (valore predefinito: 100) |
| sqlWriterCleanupScript |Specifica una query da eseguire nell'attività di copia per eseguire la pulizia dei dati di una sezione specifica. |Istruzione di query. |No |
| sliceIdentifierColumnName |Specifica il nome della colonna per l'attività di copia da riempire con un identificatore di sezione generato automaticamente. Il valore di **sliceIdentifierColumnName** viene usato per eseguire la pulizia dei dati di una sezione specifica quando viene nuovamente eseguita. |Nome di colonna di una colonna con tipo di dati **binary(32)** . |No |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Esempi JSON per la copia dei dati da e verso il database Oracle

Gli esempi seguenti forniscono le definizioni JSON di esempio che è possibile usare per creare una pipeline usando [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Questi esempi illustrano come copiare dati da o verso un database Oracle e da o verso Archiviazione BLOB di Azure. I dati possono tuttavia essere copiati in uno qualsiasi dei sink elencati in [Archivi dati e formati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

**Esempio: Copiare i dati da Oracle ad Archiviazione BLOB di Azure**

L'esempio include le entità della data factory seguenti:

* Un servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) come origine e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) come sink.

L'esempio copia i dati da una tabella di un database Oracle locale a un BLOB ogni ora. Per altre informazioni sulle varie proprietà usate nell'esempio, vedere le sezioni che seguono gli esempi.

**Servizio collegato Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Set di dati di input Oracle**

L'esempio presuppone che sia stata creata una tabella denominata **MyTable** in Oracle. Contiene una colonna denominata **timestampcolumn** per i dati di una serie temporale.

Impostando **external** su **true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e che il set di dati non è prodotto da un'attività al suo interno.

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

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (**frequenza**: **oraria**, **intervallo**: **1**). Il percorso della cartella e il nome file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa la parte anno, mese, giorno e ora dell'ora di inizio.

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

**Pipeline con un'attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output e programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo **source** è impostato su **OracleSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata tramite la proprietà **oracleReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
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

**Esempio: copiare i dati da Archiviazione BLOB di Azure in Oracle**

Questo esempio illustra come copiare dati da un account di Archiviazione BLOB di Azure a un database Oracle locale. È tuttavia possibile copiare dati *direttamente* da una qualsiasi delle origini elencate in [Archivi dati e formati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tramite l'attività di copia in Azure Data Factory.

L'esempio include le entità della data factory seguenti:

* Un servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) come origine e [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) come sink.

L'esempio copia i dati da un BLOB a una tabella nel database Oracle locale ogni ora. Per altre informazioni sulle varie proprietà usate nell'esempio, vedere le sezioni che seguono gli esempi.

**Servizio collegato Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Servizio collegato di archiviazione BLOB di Azure**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Set di dati di input del BLOB di Azure**

I dati vengono prelevati da un nuovo BLOB ogni ora (**frequenza**: **ora**, **intervallo**: **1**). Il percorso della cartella e il nome file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa la parte anno, mese e giorno dell'ora di inizio. Il nome file usa la parte relativa all'ora dell'ora di inizio. L'impostazione di **external** su **true** comunica al servizio Data Factory che questa tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

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

**Set di dati di output Oracle**

L'esempio presuppone che sia stata creata una tabella denominata **MyTable** in Oracle. È necessario creare la tabella in Oracle con lo stesso numero di colonne contenute nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

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

**Pipeline con un'attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output e programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
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

**Messaggio di errore**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Possibili cause**

* Non è stato installato il provider di dati .NET Framework per Oracle.
* Il provider di dati .NET Framework per Oracle è stato installato in .NET Framework 2.0 e non è disponibile nelle cartelle di .NET Framework 4.0.

**Risoluzione**

* Se non è stato installato il provider .NET per Oracle, [installarlo](https://www.oracle.com/technetwork/topics/dotnet/downloads/) e quindi ripetere lo scenario.
* Se viene visualizzato il messaggio di errore anche dopo l'installazione del provider, completare questa procedura:
    1. Aprire il file machine.config per .NET 2.0 dalla cartella <disco di sistema\>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Cercare **Provider di dati Oracle per .NET**. Dovrebbe essere possibile trovare una voce, come illustrato nell'esempio seguente in **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Copiare questa voce nel file Machine. config nella seguente cartella .NET 4,0: <\>del disco di sistema: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Modificare quindi la versione in 4. xxx. x.x.
* Installare <percorso di installazione di ODP.NET\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll nella Global Assembly Cache eseguendo **gacutil /i [percorso del provider]** .

### <a name="problem-2-datetime-formatting"></a>Problema 2: formattazione di data/ora

**Messaggio di errore**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Risoluzione**

Potrebbe essere necessario modificare la stringa di query nell'attività di copia in base alla configurazione delle date nel database Oracle. Di seguito è riportato un esempio (basato sull'uso della funzione **to_date**):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapping dei tipi per Oracle

Come accennato in [Attività di spostamento dei dati](data-factory-data-movement-activities.md), l'attività di copia esegue conversioni di tipo automatiche dai tipi di origine ai tipi di sink usando l'approccio in due passaggi seguente:

1. Conversione dai tipi di origine nativi al tipo .NET.
2. Conversione dal tipo .NET al tipo di sink nativo.

Quando si spostano dati da Oracle, vengono usati i mapping seguenti dal tipo di dati Oracle al tipo .NET e viceversa:

| Tipo di dati Oracle | Tipo di dati .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(supportato solo in Oracle 10g e versioni successive quando si usa un driver Microsoft) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (se la precisione > 28) |
| INTEGER |Decimal, String (se la precisione > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |TimeSpan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (se la precisione > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> I tipi di dati **INTERVAL YEAR TO MONTH** e **INTERVAL DAY TO SECOND** non sono supportati quando si usa un driver Microsoft.

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink

Per altre informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lettura ripetibile da origini relazionali

Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire manualmente una sezione. È anche possibile configurare un criterio di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita manualmente o tramite un criterio di ripetizione, è necessario assicurarsi che vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Per altre informazioni, vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

Vedere la [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md) per informazioni sui fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Azure Data Factory. È anche possibile ottenere informazioni sui diversi modi per ottimizzarle.
