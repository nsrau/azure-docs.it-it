---
title: Copiare dati nel/dal database SQL di Azure | Microsoft Docs
description: Informazioni su come copiare dati da e verso il database SQL di Azure mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5718cfdca4e12edcb98e79807ffe86d7be16b07
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Copiare dati da e nel database SQL di Azure con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-azure-sql-connector.md)
> * [Versione 2 - Anteprima](../connector-azure-sql-database.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Azure SQL Database connector in V2](../connector-azure-sql-database.md) (Connettore del database SQL di Azure nella versione 2).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da e verso database SQL di Azure. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.  

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare i dati **da un database SQL di Azure** negli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **a un database SQL di Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Tipo di autenticazione supportato
Il connettore per database SQL di Azure supporta l'autenticazione di base.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un database SQL di Azure usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un'archiviazione BLOB di Azure in un database SQL di Azure, si creano due servizi collegati per collegare l'account di archiviazione di Azure e il database SQL di Azure alla data factory. Per le proprietà del servizio collegato specifiche per il database SQL di Azure, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties). 
3. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare un contenitore BLOB e la cartella che contiene i dati di input. Si crea anche un altro set di dati per specificare la tabella SQL nel database SQL di Azure che contiene i dati copiati dall'archiviazione BLOB. Per le proprietà del set di dati specifiche per Azure Data Lake Store, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
4. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa BlobSource come origine e SqlSink come sink per l'attività di copia. Analogamente, se si esegue la copia dal database SQL di Azure nell'archiviazione BLOB di Azure, si usa SqlSource e BlobSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche per il database SQL di Azure, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per entità di data factory utilizzate per copiare i dati da e verso un database SQL di Azure, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-sql-database) in questo articolo. 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di un database SQL di Azure: 

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
Un servizio collegato SQL di Azure collega un database SQL di Azure alla data factory. La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato SQL di Azure.

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| type |La proprietà del tipo deve essere impostata su: **AzureSqlDatabase** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di database SQL di Azure per la proprietà connectionString. È supportata solo l'autenticazione di base. |Sì |

> [!IMPORTANT]
> Configurare il [firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e il server di database in modo da [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Se si copiano dati nel database SQL di Azure dall'esterno di Azure e da origini dati locali con gateway di data factory, configurare anche un intervallo di indirizzi IP appropriato per il computer che invia dati al database SQL di Azure.

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per specificare un set di dati per rappresentare i dati di input o outpui in un database SQL di Azure, impostare la proprietà del tipo del set di dati su **AzureSqlTable**. Impostare la proprietà **linkedServiceName** del set di dati sul nome del servizio collegato SQL di Azure.  

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureSqlTable** presenta le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| tableName |Nome della tabella o vista nell'istanza di database SQL di Azure a cui fa riferimento il servizio collegato. |Sì |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Se si effettua il trasferimento dei dati da un database SQL di Azure, impostare il tipo di origine nell'attività di copia su **SqlSource**. Analogamente, se si effettua il trasferimento dei dati in un database SQL di Azure, impostare il tipo di sink nell'attività di copia su **SqlSink**. Questa sezione presenta un elenco delle proprietà supportate da SqlSource e SqlSink.

### <a name="sqlsource"></a>SqlSource
In caso di attività di copia con origine di tipo **SqlSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Esempio: `select * from MyTable`. |No  |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No  |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |

Se la proprietà **sqlReaderQuery** è specificata per SqlSource, l'attività di copia esegue questa query nell'origine del database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query (`select column1, column2 from mytable`) da eseguire sul database SQL di Azure. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

> [!NOTE]
> Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore per la proprietà **tableName** nel set di dati JSON. Non sono disponibili convalide eseguite su questa tabella.
>
>

### <a name="sqlsource-example"></a>Esempio SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Definizione della stored procedure:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** supporta le proprietà seguenti:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No  |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. Per altre informazioni, vedere la [copia ripetibile](#repeatable-copy). |Istruzione di query. |No  |
| sliceIdentifierColumnName |Specificare il nome di una colonna in cui inserire nell'attività di copia l'identificatore di sezione generato automaticamente che verrà usato per pulire i dati di una sezione specifica quando viene ripetuta l'esecuzione. Per altre informazioni, vedere la [copia ripetibile](#repeatable-copy). |Nome di colonna di una colonna con tipo di dati binario (32). |No  |
| sqlWriterStoredProcedureName |Nome della stored procedure che definisce come applicare i dati di origine nella tabella di destinazione, ad esempio per eseguire upsert o trasformazioni usando logica di business personalizzata. <br/><br/>Si noti che questa stored procedure verrà **richiamata per batch**. Se si vuole eseguire una sola volta un'operazione che non ha nulla a che fare con i dati di origine, ad esempio un'eliminazione o un troncamento, usare la proprietà `sqlWriterCleanupScript`. |Nome della stored procedure. |No  |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |
| sqlWriterTableType |Specificare il nome di un tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |Nome del tipo di tabella. |No  |

#### <a name="sqlsink-example"></a>Esempio SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Esempi JSON per la copia dei dati da e verso un database SQL
Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati in e da un database SQL di Azure e un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Esempio: Copiare i dati dal database SQL di Azure SQL nel BLOB di Azure
L'esempio definisce le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [AzureSqlDatabase](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia ogni ora i dati di una serie temporale (con frequenza oraria, giornaliera e così via) da una tabella del database SQL di Azure a un BLOB. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.  

**Servizio collegato per il database SQL Azure.**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Vedere la sezione sul [servizio collegato SQL di Azure](#linked-service) per l'elenco delle proprietà supportate da questo servizio collegato.

**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
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
Vedere l'articolo [BLOB di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) per l'elenco delle proprietà supportate da questo servizio collegato.


**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

L'impostazione di "external" su "true" comunica al servizio Azure Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Vedere la sezione sulle [proprietà del tipo di set di dati SQL di Azure](#dataset) per l'elenco delle proprietà supportate da questo tipo di set di dati.  

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Vedere la sezione sulle [proprietà del tipo di set di dati BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties) per l'elenco delle proprietà supportate da questo tipo di set di dati.  

**Un'attività di copia in una pipeline con un'origine SQL e un sink BLOB:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
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
Nell'esempio, la proprietà **sqlReaderQuery** è specificata per SqlSource. L'attività di copia esegue questa query nell'origine del database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query da eseguire sul database SQL di Azure. Ad esempio: `select column1, column2 from mytable`. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

Vedere la sezione [SqlSource](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) per l'elenco delle proprietà supportate da SqlSource e BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Esempio: Copiare i dati dal BLOB di Azure nel database SQL di Azure
L'esempio definisce le entità di Data Factory seguenti:  

1. Un servizio collegato di tipo [AzureSqlDatabase](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSqlTable](#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlSink](#copy-activity-properties).

L'esempio copia ogni ora i dati di una serie temporale (con frequenza oraria, giornaliera e così via) da un BLOB di Azure a una tabella del database SQL di Azure. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato SQL di Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Vedere la sezione sul [servizio collegato SQL di Azure](#linked-service) per l'elenco delle proprietà supportate da questo servizio collegato.

**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
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
Vedere l'articolo [BLOB di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) per l'elenco delle proprietà supportate da questo servizio collegato.


**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti di anno, mese e giorno della data/ora di inizio e il nome file usa la parte relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Vedere la sezione sulle [proprietà del tipo di set di dati BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties) per l'elenco delle proprietà supportate da questo tipo di set di dati.

**Set di dati di aoutput del database SQL di Azure**

L'esempio copia dati in una tabella denominata "MyTable" in SQL Azure. Creare la tabella nel database SQL di Azure con lo stesso numero di colonne previsto nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Vedere la sezione sulle [proprietà del tipo di set di dati SQL di Azure](#dataset) per l'elenco delle proprietà supportate da questo tipo di set di dati.

**Un'attività di copia in una pipeline con un'origine BLOB e un sink SQL:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo **sink** è impostato su **SqlSink**.

```JSON
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
            "name": "AzureSqlOutput"
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
Per l'elenco delle proprietà supportate da SqlSink e BlobSink, vedere la sezione [SqlSink](#sqlsink) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

## <a name="identity-columns-in-the-target-database"></a>Colonne Identity nel database di destinazione
In questa sezione viene fornito un esempio per la copia di dati da una tabella di origine senza una colonna identity in una tabella di destinazione con una colonna identity.

**Tabella di origine:**

```SQL
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Tabella di destinazione:**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
Si noti che la tabella di destinazione contiene una colonna identity.

**Definizione JSON del set di dati di origine**

```JSON
{
    "name": "SampleSource",
    "properties": {
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

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
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

## <a name="invoke-stored-procedure-from-sql-sink"></a>Chiamare una stored procedure da un sink SQL
Per un esempio di come chiamare una stored procedure da un sink SQL in un'attività di copia di una pipeline, vedere l'articolo su come [richiamare una stored procedure per il sink SQL nell'attività di copia](data-factory-invoke-stored-procedure-from-copy-activity.md). 

## <a name="type-mapping-for-azure-sql-database"></a>Mapping dei tipi per il database SQL di Azure
Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da e verso il database SQL di Azure vengono usati i mapping seguenti dal tipo SQL al tipo .NET e viceversa. Il mapping è uguale al mapping del tipo di dati di SQL Server per ADO.NET.

| Tipo di motore di database di SQL Server | Tipo di .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |boolean |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| immagine |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |Intervallo di tempo |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="map-source-to-sink-columns"></a>Eseguire il mapping delle colonne dell'origine alle colonne del sink
Per informazioni sul mapping delle colonne del set di dati di origine alle colonne del set di dati del sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Copia ripetibile
Quando si copiano dati in un database SQL Server, per impostazione predefinita l'attività di copia accoda i dati alla tabella di sink. Per eseguire invece un UPSERT, vedere l'articolo [Scrittura ripetibile in SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink). 

Quando si copiano dati da archivi dati relazionali, è necessario tenere presente la ripetibilità per evitare risultati imprevisti. In Azure Data Factory è possibile rieseguire una sezione manualmente. È anche possibile configurare i criteri di ripetizione per un set di dati in modo da rieseguire una sezione in caso di errore. Quando una sezione viene rieseguita in uno dei due modi, è necessario assicurarsi che non vengano letti gli stessi dati, indipendentemente da quante volte viene eseguita la sezione. Vedere [Lettura ripetibile da origini relazionali](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
