---
title: Spostare i dati da e verso SQL Server | Documentazione Microsoft
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
ms.date: 01/25/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 60d9284bd7deaeb5ba4078cfa889a516a7cf75e2
ms.openlocfilehash: 1352e6f145a099dfd3ec7003132172dcbeb90d94


---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Spostare i dati da e verso SQL Server locale o in IaaS (VM di Azure) utilizzando Data factory di Azure
Questo articolo illustra come usare l'attività di copia per spostare dati da e verso il server SQL a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati e degli archivi dati supportati come origini e sink.

## <a name="supported-versions"></a>Versioni supportate
Questo connettore di SQL Server supporta la copia dei dati da e verso le seguenti versioni dell'istanza ospitate in locale o in IaaS di Azure tramite l'autenticazione SQL e l'autenticazione Windows:

* SQL Server 2016
* SQL Server 2014
* SQL Server 2012
* SQL Server 2008 R2
* SQL Server 2008
* SQL Server 2005

## <a name="create-pipeline"></a>Creare una pipeline
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un SQL Server locale usando diversi strumenti/API.  

* Copia guidata
* Portale di Azure
* Visual Studio
* Azure PowerShell
* API .NET
* API REST

Vedere [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per ottenere le istruzioni dettagliate sulle diverse modalità di creazione di una pipeline con un'attività di copia.

## <a name="enabling-connectivity"></a>Abilitazione della connettività
I concetti e i passaggi necessari per la connessione con SQL Server ospitato in locale o in macchine virtuali di Azure IaaS (Infrastructure-as-a-Service) sono gli stessi. In entrambi i casi, è necessario usare il Gateway di gestione dati per la connettività.

Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway. L'impostazione di un'istanza del gateway è un prerequisito per la connessione con SQL Server.

Sebbene sia possibile installare il gateway nello stesso computer locale o istanza cloud della macchina virtuale come SQL Server per migliorare le prestazioni, si consiglia di installarli in computer separati, per evitare che il gateway e il server SQL entrino in conflitto sulle risorse.

## <a name="copy-data-wizard"></a>Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati da un database di SQL Server in uno degli archivi dati sink supportati consiste nell'usare la Copia dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Gli esempi seguenti mostrano come copiare dati da e verso SQL Server e l'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.     

## <a name="sample-copy-data-from-sql-server-to-azure-blob"></a>Esempio: Copiare i dati da SQL Server al BLOB di Azure
L'esempio seguente mostra:

1. Un servizio collegato di tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [set di dati](data-factory-create-datasets.md) di tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale dalla tabella del server SQL in un archivio BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il Gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato di SQL Server**
```JSON
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
**Set di dati input di SQL Server**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Server e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale. È possibile eseguire query su più tabelle all'interno dello stesso database usando un singolo set di dati, ma come typeProperty tableName del set di dati deve essere usata una sola tabella.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

```JSON
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

```JSON
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

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
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

Vedere la sezione [SqlSource](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) per l'elenco delle proprietà supportate da SqlSource e BlobSink.

## <a name="sample-copy-data-from-azure-blob-to-sql-server"></a>Esempio: Copiare i dati dal BLOB di Azure in SQL Server
L'esempio seguente mostra:

1. Il servizio collegato di tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2. Il servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
5. La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da un archivio BLOB di Azure a una tabella del server SQL ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di SQL Server**

```JSON
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
**Set di dati di input del BLOB di Azure**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che il set di dati è esterno a Data Factory e non è prodotto da un'attività al suo interno.

```JSON
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

```JSON
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
## <a name="sql-server-linked-service-properties"></a>Proprietà del servizio collegato SQL Server
Negli esempi si è usato un servizio collegato di tipo **OnPremisesSqlServer** per collegare un database di SQL Server locale a un data factory. La tabella seguente contiene le descrizioni degli elementi JSON specifici per il servizio collegato di SQL Server locale.

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

```JSON
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlLinkedService",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON per l'uso dell'Autenticazione Windows**

Se vengono specificati nome utente e password, il gateway li usa per rappresentare l'account utente specificato per la connessione al database di SQL Server locale. In caso contrario, il gateway si connette a SQL Server direttamente con il contesto di sicurezza del gateway (l'account di avvio).

```JSON
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlLinkedService",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

Vedere [Impostazione delle credenziali e della sicurezza](data-factory-data-management-gateway.md#encrypting-credentials) per informazioni dettagliate sull'impostazione delle credenziali per un'origine dei dati SQL Server.

## <a name="sql-server-dataset-type-properties"></a>Proprietà del tipo del set di dati di SQL Server
Negli esempi si è usato un set di dati di tipo **SqlServerTable** per rappresentare una tabella in un database di SQL Server.  

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Server, BLOB di Azure, tabelle di Azure e così via).

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **SqlServerTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o vista nell'istanza del database di SQL Server a cui fa riferimento il servizio collegato. |Sì |

## <a name="sql-server-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia di SQL Server
Se si effettua il trasferimento dei dati da un database di SQL Server, impostare il tipo di origine nell'attività di copia su **SqlSource**. Analogamente, se si effettua il trasferimento dei dati in un database di SQL Server, impostare il tipo di sink nell'attività di copia su **SqlSink**. Questa sezione presenta un elenco delle proprietà supportate da SqlSource e SqlSink.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.
>
>

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

### <a name="sqlsource"></a>SqlSource
Se in un'attività di copia l'origine è di tipo **SqlSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. Può fare riferimento a più tabelle del database a cui fa riferimento il set di dati di input. Se non specificato, l'istruzione SQL eseguita: selezionare da MyTable. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

Se la proprietà **sqlReaderQuery** è specificata per SqlSource, l'attività di copia esegue questa query nell'origine del database del server di SQL per ottenere i dati.

In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura vengono usate per compilare una query da eseguire nel database del server di SQL. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

> [!NOTE]
> Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore per la proprietà **tableName** nel set di dati JSON. Non sono disponibili convalide eseguite su questa tabella.
>
>

### <a name="sqlsink"></a>SqlSink
**SqlSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/>  Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| sqlWriterCleanupScript |Specificare la query per l'attività di copia da eseguire in modo che i dati di una sezione specifica vengano eliminati. Per altre informazioni, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy) . |Istruzione di query. |No |
| sliceIdentifierColumnName |Specificare il nome della colonna per l'attività di copia da riempire con l'identificatore di sezione generato automaticamente, che viene usato per eliminare i dati di una sezione specifica quando viene nuovamente eseguita. Per altre informazioni, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy) . |Nome di colonna di una colonna con tipo di dati binario (32). |No |
| sqlWriterStoredProcedureName |Nome della stored procedure che esegue l'upsert (aggiornamenti/inserimenti) nella tabella di destinazione. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |
| sqlWriterTableType |Specificare il tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |Nome del tipo di tabella. |No |

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
6. **Verificare la connessione**: per connettersi al server SQL con un nome completo, usare SQL Server Management Studio da un computer diverso. Ad esempio: "<machine>.<domain>.corp.<company>.com,1433."

   > [!IMPORTANT]

   > Per informazioni dettagliate, vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).
   >
   > Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Colonne Identity nel database di destinazione
Questa sezione fornisce un esempio per la copia di dati da una tabella di origine senza una colonna identity in una tabella di destinazione con una colonna identity.

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

```JSON
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

Dopodiché, eseguire il mapping delle colonne dal set di dati di origine alle colonne del set di dati di destinazione. Per avere un esempio, vedere la sezione [Esempi di mapping di colonne](#column-mapping-samples) .

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[!INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server--azure-sql"></a>Mapping dei tipi per SQL Server e Azure SQL
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

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).



<!--HONumber=Dec16_HO3-->


