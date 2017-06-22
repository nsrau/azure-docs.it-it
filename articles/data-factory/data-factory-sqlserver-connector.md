---
title: Spostare i dati da e verso SQL Server | Microsoft Docs
description: Informazioni su come spostare i dati da/verso il database di SQL Server locale o in una VM di Azure utilizzando Data factory di Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 25fca1ac29817fc6d72dd5ec5033a2962f3f0be4
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017


---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Spostare i dati da e verso SQL Server locale o in IaaS (VM di Azure) utilizzando Data factory di Azure
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da e verso un database SQL Server locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia. 

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare i dati **da un database SQL Server** negli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sink](../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **a un database SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versioni di SQL Server supportate
Questo connettore di SQL Server supporta la copia dei dati da e verso le seguenti versioni dell'istanza ospitate in locale o in IaaS di Azure tramite l'autenticazione SQL e l'autenticazione Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Abilitazione della connettività
I concetti e i passaggi necessari per la connessione con SQL Server ospitato in locale o in macchine virtuali di Azure IaaS (Infrastructure-as-a-Service) sono gli stessi. In entrambi i casi, è necessario usare il Gateway di gestione dati per la connettività.

Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway. L'impostazione di un'istanza del gateway è un prerequisito per la connessione con SQL Server.

Sebbene sia possibile installare il gateway nello stesso computer locale o istanza cloud della macchina virtuale come SQL Server per migliorare le prestazioni, si consiglia di installarli in computer separati, per evitare che il gateway e il server SQL entrino in conflitto sulle risorse.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un SQL Server locale usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un database SQL Server in un'archiviazione BLOB di Azure, si creano due servizi collegati per collegare il database SQL Server e l'account di archiviazione di Azure alla data factory. Per le proprietà del servizio collegato specifiche per il database SQL Server, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties). 
3. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare la tabella nel database SQL Server che contiene i dati di input. Si crea anche un altro set di dati per specificare il contenitore BLOB e la cartella che contiene i dati copiati dal database SQL Server. Per le proprietà del set di dati specifiche per il database SQL Server, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
4. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa SqlSource come origine e BlobSink come sink per l'attività di copia. Analogamente, se si effettua la copia dall'archiviazione BLOB di Azure al database SQL Server, usare BlobSource e SqlSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche per il database SQL Server, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory.  Per esempi con definizioni JSON per le entità di Data Factory che vengono usate per copiare dati da e verso un database SQL Server locale, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-from-and-to-sql-server) in questo articolo. 

Nelle sezioni seguenti sono disponibili le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità della Data Factory specifiche di SQL Server: 

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
Viene creato un servizio collegato di tipo **OnPremisesSqlServer** per collegare un database di SQL Server locale a una data factory. La tabella seguente contiene le descrizioni degli elementi JSON specifici per il servizio collegato di SQL Server locale.

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato SQL Server.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **OnPremisesSqlServer**. |Sì |
| connectionString |Specificare le informazioni di connectionString necessarie per connettersi al database di SQL Server locale usando l'autenticazione di SQL o Windows. |Sì |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database di SQL Server locale. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione Windows. Esempio: **nomedominio\\nomeutente**. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |

È possibile crittografare le credenziali usando il cmdlet **New-AzureRmDataFactoryEncryptValue** e usarle nella stringa di connessione, come illustrato nell'esempio seguente (proprietà **EncryptedCredential**):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Esempi
**JSON per l'uso dell'Autenticazione di SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON per l'uso dell'Autenticazione Windows**

Se vengono specificati nome utente e password, il gateway li usa per rappresentare l'account utente specificato per la connessione al database di SQL Server locale. In caso contrario, il gateway si connette a SQL Server direttamente con il contesto di sicurezza del gateway (l'account di avvio).

```json
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlServer",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Negli esempi si è usato un set di dati di tipo **SqlServerTable** per rappresentare una tabella in un database di SQL Server.  

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Server, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **SqlServerTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o vista nell'istanza del database di SQL Server a cui fa riferimento il servizio collegato. |Sì |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Se si effettua il trasferimento dei dati da un database di SQL Server, impostare il tipo di origine nell'attività di copia su **SqlSource**. Analogamente, se si effettua il trasferimento dei dati in un database di SQL Server, impostare il tipo di sink nell'attività di copia su **SqlSink**. Questa sezione presenta un elenco delle proprietà supportate da SqlSource e SqlSink.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

### <a name="sqlsource"></a>SqlSource
Se in un'attività di copia l'origine è di tipo **SqlSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. Può fare riferimento a più tabelle del database a cui fa riferimento il set di dati di input. Se non specificato, l'istruzione SQL eseguita: selezionare da MyTable. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

Se la proprietà **sqlReaderQuery** è specificata per SqlSource, l'attività di copia esegue questa query nell'origine del database del server di SQL per ottenere i dati.

In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura vengono usate per compilare una query da eseguire nel database del server di SQL. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

> [!NOTE]
> Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore per la proprietà **tableName** nel set di dati JSON. Non sono disponibili convalide eseguite su questa tabella.

### <a name="sqlsink"></a>SqlSink
**SqlSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| sqlWriterCleanupScript |Specificare la query per l'attività di copia da eseguire in modo che i dati di una sezione specifica vengano eliminati. Per altre informazioni, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy) . |Istruzione di query. |No |
| sliceIdentifierColumnName |Specificare il nome della colonna per l'attività di copia da riempire con l'identificatore di sezione generato automaticamente, che viene usato per eliminare i dati di una sezione specifica quando viene nuovamente eseguita. Per altre informazioni, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy) . |Nome di colonna di una colonna con tipo di dati binario (32). |No |
| sqlWriterStoredProcedureName |Nome della stored procedure che esegue l'upsert (aggiornamenti/inserimenti) nella tabella di destinazione. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |
| sqlWriterTableType |Specificare il tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |Nome del tipo di tabella. |No |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Esempi JSON per la copia dei dati da e a SQL Server
Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Gli esempi seguenti mostrano come copiare dati da e verso SQL Server e l'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Esempio: Copiare i dati da SQL Server al BLOB di Azure
L'esempio seguente mostra:

1. Un servizio collegato di tipo [OnPremisesSqlServer](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di tipo [SqlServerTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati di una serie temporale dalla tabella del server SQL in un archivio BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il Gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato di SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati input di SQL Server**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Server e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale. È possibile eseguire query su più tabelle all'interno dello stesso database usando un singolo set di dati, ma come typeProperty tableName del set di dati deve essere usata una sola tabella.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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
**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

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
**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
In questo esempio la proprietà **sqlReaderQuery** è specificata per SqlSource. L'attività di copia esegue questa query nell'origine del database del server SQL per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri). La proprietà sqlReaderQuery può fare riferimento a più tabelle nel database a cui fa riferimento il set di dati di input. Non è limitato solo alla tabella impostata come typeProperty tableName del set di dati.

Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura vengono usate per compilare una query di selezione da eseguire nel database del server di SQL. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

Vedere la sezione [SqlSource](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) per l'elenco delle proprietà supportate da SqlSource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Esempio: Copiare i dati dal BLOB di Azure in SQL Server
L'esempio seguente mostra:

1. Il servizio collegato di tipo [OnPremisesSqlServer](#linked-service-properties).
2. Il servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#sql-server-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da un archivio BLOB di Azure a una tabella del server SQL ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input del BLOB di Azure**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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
**Set di dati output di SQL Server**

L'esempio copia i dati in una tabella denominata "MyTable" in SQL Server. Creare la tabella in SQL Server con lo stesso numero di colonne di quelle contenute nel file con estensione csv del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo **sink** è impostato su **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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

## <a name="troubleshooting-connection-issues"></a>Risoluzione dei problemi di connessione
1. Configurare SQL Server per accettare le connessioni remote. Avviare **SQL Server Management Studio**, fare clic con il pulsante destro del mouse su **server** e selezionare **Properties**. Scegliere **Connessioni** dall'elenco e selezionare **Consenti connessioni remote al server**.

    ![Abilitare le connessioni remote](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Per una procedura dettagliata, vedere [Configurare l'opzione di configurazione del server remote access](https://msdn.microsoft.com/library/ms191464.aspx) .
2. Avviare **Gestione configurazione SQL Server**. Espandere **Configurazione di rete SQL Server** per l'istanza prevista e selezionare **Protocolli per MSSQLSERVER**. I protocolli sono visualizzati nel riquadro di destra. Abilitare il protocollo TCP/IP facendo clic con il pulsante destro del mouse su **TCP/IP** e selezionando **Abilita**.

    ![Abilitare TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Per informazioni dettagliate e modalità alternative di abilitazione del protocollo TCP/IP, vedere [Abilitare o disabilitare un protocollo di rete del server](https://msdn.microsoft.com/library/ms191294.aspx).
3. Nella stessa finestra fare doppio clic su **TCP/IP** per aprire la finestra **Proprietà TCP/IP**.
4. Passare alla scheda **Indirizzi IP** . Scorrere verso il basso per vedere la sezione **IPAll** . Annotare la **Porta TCP**: il valore predefinito è **1433**.
5. Creare una **regola per Windows Firewall** nel computer per consentire il traffico in ingresso attraverso questa porta.  
6. **Verificare la connessione**: per connettersi al server SQL con un nome completo, usare SQL Server Management Studio da un computer diverso. Ad esempio: "<machine><domain>.corp<company>.com,1433."

   > [!IMPORTANT]

   > Per informazioni dettagliate, vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).
   >
   > Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Colonne Identity nel database di destinazione
Questa sezione fornisce un esempio per la copia di dati da una tabella di origine senza una colonna identity in una tabella di destinazione con una colonna identity.

**Tabella di origine:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Tabella di destinazione:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Si noti che la tabella di destinazione contiene una colonna identity.

**Definizione JSON del set di dati di origine**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definizione JSON del set di dati di destinazione**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Si noti che la tabella di origine e la tabella di destinazione hanno schemi diversi (la destinazione include una colonna aggiuntiva identity). In questo scenario è necessario specificare la proprietà **structure** nella definizione del set di dati di destinazione che non include la colonna identity.

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Copia ripetibile
Quando si copiano dati in un database SQL Server, per impostazione predefinita l'attività di copia accoda i dati alla tabella di sink. Per eseguire invece un UPSERT, vedere l'articolo [Scrittura ripetibile in SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink). 

Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="invoke-stored-procedure-from-sql-sink"></a>Chiamare una stored procedure da un sink SQL
Per un esempio di come chiamare una stored procedure da un sink SQL in un'attività di copia di una pipeline, vedere l'articolo su come [richiamare una stored procedure per il sink SQL nell'attività di copia](data-factory-invoke-stored-procedure-from-copy-activity.md).

## <a name="type-mapping-for-sql-server--azure-sql"></a>Mapping dei tipi per SQL Server e Azure SQL
Come accennato nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni di tipi automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da e verso SQL Azure, SQL Server, Sybase sono usati i mapping seguenti dal tipo SQL di tipo .NET e viceversa.

Il mapping è uguale al mapping del tipo di dati di SQL Server per ADO.NET.

| Tipo di motore di database di SQL Server | Tipo di .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimale |Decimale |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| immagine |Byte[] |
| int |Int32 |
| money |Decimale |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimale |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimale |
| sql_variant |Object * |
| text |String, Char[] |
| time |Intervallo di tempo |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="mapping-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

