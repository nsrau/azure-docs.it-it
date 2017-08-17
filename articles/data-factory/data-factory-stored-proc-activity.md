---
title: "Attività di stored procedure di SQL Server"
description: "Informazioni sull'uso dell'attività di stored procedure di SQL Server da una pipeline di Data factory per richiamare una stored procedure in un database SQL di Azure o in Azure SQL Data Warehouse."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6505d9aa2c7ae003bd928e2fa82cd923a9615394
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="sql-server-stored-procedure-activity"></a>Attività di stored procedure di SQL Server
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Attività Hive](data-factory-hive-activity.md) 
> * [Attività di Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Attività di esecuzione batch di Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Attività della risorsa di aggiornamento di Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata di .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Panoramica
Le attività di trasformazione dei dati in una [pipeline](data-factory-create-pipelines.md) di Data factory trasformano ed elaborano i dati non elaborati in stime e approfondimenti. L'attività di stored procedure è una delle attività di trasformazione supportate da Data factory. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate in Data Factory.

È possibile usare l'attività stored procedure per richiamare una stored procedure in uno dei seguenti archivi dati presenti in azienda o in una macchina virtuale di Azure: 

- Database SQL di Azure
- Azure SQL Data Warehouse
- Database di SQL Server.  Se si usa SQL Server, è necessario installare Gateway di gestione dati nello stesso computer che ospita il database o in un computer separato che ha accesso al database. Gateway di gestione dati è un componente che connette in modo sicuro e gestito origini dati presenti in locale o in macchine virtuali di Azure ai servizi cloud. Per informazioni dettagliate, vedere [Data Management Gateway](data-factory-data-management-gateway.md) (Gateway di gestione dati).

> [!IMPORTANT]
> Quando si copiano dati in SQL Server o nel Database SQL di Azure, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure tramite la proprietà **sqlWriterStoredProcedureName**. Per altre informazioni, vedere [Chiamare una stored procedure da un'attività di copia](data-factory-invoke-stored-procedure-from-copy-activity.md). Per informazioni dettagliate sulla proprietà, vedere gli articoli connettore seguenti: [Database SQL di Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Non è possibile richiamare una stored procedure durante la copia dei dati in un Azure SQL Data Warehouse tramite un'attività di copia. Tuttavia, è possibile usare l'attività di stored procedure per richiamare una stored procedure in un SQL Data Warehouse. 
>  
> Quando si copiano dati in SQL Server, nel Database SQL di Azure o in Azure SQL Data Warehouse, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure che consenta di leggere i dati dal database di origine tramite la proprietà **sqlReaderStoredProcedureName**. Per altre informazioni, vedere gli articoli connettore seguenti: [Database SQL di Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


La procedura dettagliata seguente usa l'attività stored procedure in una pipeline per richiamare una stored procedure in un database SQL di Azure. 

## <a name="walkthrough"></a>Procedura dettagliata
### <a name="sample-table-and-stored-procedure"></a>Tabella di esempio e stored procedure
1. Creare la seguente **tabella** nel database SQL di Azure usando SQL Server Management Studio o qualsiasi altro strumento conosciuto. La colonna datetimestamp riporta la data e l'ora in cui viene generato l'ID corrispondente.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    La colonna ID riporta l'identificatore univoco, mentre la colonna datetimestamp riporta la data e l'ora in cui viene generato l'ID corrispondente.
    
    ![Dati di esempio](./media/data-factory-stored-proc-activity/sample-data.png)

    In questo esempio, la stored procedure si trova in un database SQL di Azure. L'approccio è simile indipendentemente dal fatto che la stored procedure si trovi in un database Azure SQL Data Warehouse o SQL Server. Per un database SQL Server, è necessario installare un [Gateway di gestione dati](data-factory-data-management-gateway.md).
2. Creare la seguente **stored procedure** che inserisce dati in **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > Il **nome** e la **combinazione di maiuscole e minuscole** per il parametro (DateTime in questo esempio) devono corrispondere a quelli del parametro specificato nel codice JSON per la pipeline/attività. Nella definizione della stored procedure assicurarsi che **@** sia usato come prefisso per il parametro.

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **NUOVO** nel menu a sinistra e quindi su **Intelligence e analisi** e **Data factory**.

    ![Nuova data factory](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. Nel pannello **Nuova data factory** immettere **SProcDF** come nome. I nomi di Data factory di Azure sono **univoci**. È necessario anteporre al nome della data factory il proprio nome, per consentire la corretta creazione della factory.

   ![Nuova data factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Selezionare la **sottoscrizione di Azure**.
5. In **Gruppo di risorse** eseguire una di queste operazioni:
   1. Fare clic su **Crea nuovo** e immettere un nome per il gruppo di risorse.
   2. Fare clic su **Usa esistente** e scegliere un gruppo di risorse esistente.  
6. Selezionare la **località** per la data factory.
7. Selezionare **Aggiungi al dashboard** per visualizzare la data factory nel dashboard al successivo tentativo di accesso.
8. Fare clic su **Crea** nel pannello **Nuova data factory**.
9. Nel **dashboard** del portale di Azure verrà visualizzata la data factory in fase di creazione. Dopo la creazione della data factory, viene visualizzata la pagina corrispondente con elencato il contenuto della data factory.

   ![Home page di Data factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Creare un servizio collegato SQL di Azure
Dopo aver creato la data factory, si crea un servizio collegato SQL di Azure che collega alla data factory il database SQL di Azure, contenente la tabella sampletable e la stored procedure sp_sample.

1. Fare clic su **Creare e distribuire** nel pannello **Data Factory** per **SProcDF** per avviare l'editor di Data Factory.
2. Fare clic su **Nuovo archivio dati** sul barra dei comandi e scegliere **Database SQL di Azure**. Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato SQL di Azure.

   ![Nuovo archivio dati](media/data-factory-stored-proc-activity/new-data-store.png)
3. Nello script JSON apportare le modifiche seguenti:

   1. Sostituire `<servername>` con il nome del server di database SQL di Azure.
   2. Sostituire `<databasename>` con il database in cui sono state create la tabella e la stored procedure.
   3. Sostituire `<username@servername>` con l'account utente che ha accesso al database.
   4. Sostituire `<password>` con la password dell'account utente.

      ![Nuovo archivio dati](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Per distribuire il servizio collegato, fare clic su **Distribuisci** sulla barra dei comandi. Assicurarsi che AzureSqlLinkedService sia visibile nella visualizzazione albero sulla sinistra.

    ![Visualizzazione albero con servizio collegato](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Creare un set di dati di output
È necessario specificare un set di dati di output per un'attività stored procedure, anche se questa non produce alcun dato. È il set di dati di output, infatti, che determina la pianificazione dell'attività (la frequenza con cui l'attività viene eseguita: ogni ora, ogni settimana e così via). Il set di dati di output deve usare un **servizio collegato** che faccia riferimento a un database SQL di Azure, a un Azure SQL Data Warehouse o a un database SQL Server in cui si vuole che venga eseguita la stored procedure. Il set di dati di output può essere usato per passare il risultato della stored procedure per la successiva elaborazione da parte di un'altra attività, [concatenamento di attività](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline), nella pipeline. Data Factory non scrive tuttavia automaticamente l'output di una stored procedure in questo set di dati. È la stored procedure a scrivere dati in una tabella SQL cui punta il set di dati di output. In alcuni casi, il set di dati di output può essere un **set di dati fittizio** (un set di dati che punta a una tabella che in realtà non contiene alcun output della stored procedure). Il set di dati fittizio viene usato solo per specificare la pianificazione di esecuzione dell'attività stored procedure. 

1. Fare clic su **... Altro** sulla barra degli strumenti, fare clic su **Nuovo set di dati**, fare clic su **SQL Azure**. **Nuovo set di dati** sulla barra dei comandi e selezionare **Azure SQL**.

    ![Visualizzazione albero con servizio collegato](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copiare e incollare il seguente script JSON nell'editor JSON.

    ```JSON
    {                
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Per distribuire il set di dati, fare clic su **Distribuisci** sulla barra dei comandi. Assicurarsi che il set di dati sia visibile nella visualizzazione albero.

    ![Visualizzazione albero con servizi collegati](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Creare una pipeline con l'attività SqlServerStoredProcedure
Si crea ora una pipeline con un'attività stored procedure. 

Tenere presenti le proprietà seguenti: 

- La proprietà **type** deve essere impostata su **SqlServerStoredProcedure**. 
- Nelle proprietà type, **storedProcedureName** deve essere impostato su **sp_sample** (nome della stored procedure).
- La sezione **storedProcedureParameters** contiene un parametro denominato **DataTime**. Il nome e la combinazione di maiuscole e minuscole del parametro in JSON deve corrispondere al nome e alla combinazione di maiuscole e minuscole del parametro nella definizione della stored procedure. Se è necessario passare null per un parametro, usare la sintassi `"param1": null` (tutte lettere minuscole).
 
1. Fare clic su **... Altro** sulla barra dei comandi e quindi su **Nuova pipeline**.
2. Copiare e incollare il frammento JSON seguente:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
             "start": "2017-04-02T00:00:00Z",
             "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Per distribuire la pipeline, fare clic su **Distribuisci** sulla barra degli strumenti.  

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline
1. Fare clic su **X** per chiudere i pannelli dell'editor di Data Factory, tornare al pannello Data Factory e quindi fare clic su **Diagramma**.

    ![Riquadro Diagramma](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. In **Vista diagramma**saranno visualizzati una panoramica delle pipeline e i set di dati usati in questa esercitazione.

    ![Riquadro Diagramma](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Nella vista Diagramma fare doppio clic sul set di dati `sprocsampleout`. Verranno visualizzate le sezioni con stato pronto. Dovrebbero essere presenti cinque sezioni perché dal JSON viene generata una sezione ogni ora tra l'ora di inizio e l'ora di fine.

    ![Riquadro Diagramma](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando lo stato di una sezione è **Pronto**, eseguire una query `select * from sampletable` sul database SQL di Azure per verificare che la stored procedure abbia inserito i dati nella tabella.

   ![Dati di output](./media/data-factory-stored-proc-activity/output.png)

   Vedere [Monitorare la pipeline](data-factory-monitor-manage-pipelines.md) per informazioni dettagliate sul monitoraggio delle pipeline di Data Factory di Azure.  


## <a name="specify-an-input-dataset"></a>Specificare un set di dati di input
Nella procedura dettagliata, l'attività stored procedure non contiene alcun set di dati di input. Se si specifica un set di dati di input, l'attività stored procedure non viene eseguita fino a quando la porzione del set di dati input non è disponibile (in stato Ready). Il set di dati può essere esterno (non generato da un'altra attività nella stessa pipeline) o interno, ovvero generato da un'attività upstream (l'attività eseguita prima dell'attività corrente). Per l'attività stored procedure è possibile specificare più set di dati di input. In questo caso, l'attività stored procedure viene eseguita solo quando sono disponibili tutte le porzioni di set di dati di input (in stato Ready). Il set di dati di input non può essere usato nella stored procedure come parametro. Viene usato solo per verificare la dipendenza prima di iniziare l'attività della stored procedure.

## <a name="chaining-with-other-activities"></a>Concatenamento con altre attività
Se si vuole concatenare un'attività upstream con questa attività, specificare l'output dell'attività upstream come input di questa attività. In questo caso, l'attività stored procedure non viene eseguita fino a quando non viene completata l'attività upstream e il set di dati di output dell'attività upstream non è disponibile (in stato Ready). È possibile specificare i set di dati di output di più attività upstream come set di dati di input dell'attività stored procedure. In questo caso, l'attività stored procedure viene eseguita solo quando sono disponibili tutte le porzioni di set di dati di input.  

Nell'esempio seguente, l'output dell'attività di copia è OutputDataset, ossia un input dell'attività stored procedure. L'attività stored procedure, quindi, non viene eseguita fino a quando non viene completata l'attività di copia e la porzione OutputDataset non è disponibile (in stato Ready). Se si specificano più set di dati di input, l'attività stored procedure non viene eseguita fino a quando non sono disponibili (in stato Ready) le varie porzioni del set di dati input. Non è possibile usare i set di dati di input direttamente come parametri dell'attività stored procedure. 

Per altre informazioni sul concatenamento di attività, vedere [attività multiple in una pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{

    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }

        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Analogamente, per collegare l'attività stored procedure con **attività downstream** (le attività eseguite al termine dell'attività stored procedure), specificare il set di dati di output dell'attività stored procedure come input dell'attività downstream nella pipeline.

> [!IMPORTANT]
> Quando si copiano dati in SQL Server o nel Database SQL di Azure, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure tramite la proprietà **sqlWriterStoredProcedureName**. Per altre informazioni, vedere [Chiamare una stored procedure da un'attività di copia](data-factory-invoke-stored-procedure-from-copy-activity.md). Per informazioni dettagliate sulla proprietà, vedere gli articoli connettore seguenti: [Database SQL di Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> Quando si copiano dati in SQL Server, nel Database SQL di Azure o in Azure SQL Data Warehouse, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure che consenta di leggere i dati dal database di origine tramite la proprietà **sqlReaderStoredProcedureName**. Per altre informazioni, vedere gli articoli connettore seguenti: [Database SQL di Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>Formato JSON
Di seguito è riportato il formato JSON per la definizione di un'attività di Stored Procedure:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":  
        {
            "param1": "param1Value"
            …
        }
    }
}
```

La tabella seguente illustra queste proprietà JSON:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name | Nome dell'attività |Sì |
| Descrizione |Testo descrittivo per lo scopo dell'attività |No |
| type | Deve essere impostato su: **SqlServerStoredProcedure** | Sì |
| inputs | Facoltativo. Se si specifica un set di dati di input, questo dovrà essere disponibile (in stato 'Ready') per l'esecuzione dell'attività della stored procedure. Il set di dati di input non può essere usato nella stored procedure come parametro. Viene usato solo per verificare la dipendenza prima di iniziare l'attività della stored procedure. |No |
| outputs | È necessario specificare un set di dati di output per un'attività della stored procedure. Il set di dati di output specifica la **pianificazione** per le attività della stored procedure (ogni ora, ogni settimana, ogni mese e così via). <br/><br/>Il set di dati di output deve usare un **servizio collegato** che faccia riferimento a un database SQL di Azure, a un Azure SQL Data Warehouse o a un database SQL Server in cui si vuole che venga eseguita la stored procedure. <br/><br/>Il set di dati di output può essere usato per passare il risultato della stored procedure per la successiva elaborazione da parte di un'altra attività, [concatenamento di attività](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline), nella pipeline. Data Factory non scrive tuttavia automaticamente l'output di una stored procedure in questo set di dati. È la stored procedure a scrivere dati in una tabella SQL cui punta il set di dati di output. <br/><br/>In alcuni casi, il set di dati di output può essere un **set di dati fittizio**che viene usato solo per specificare la pianificazione per l'esecuzione dell'attività della stored procedure. |Sì |
| storedProcedureName |Specificare il nome della stored procedure nel database SQL di Azure, nel database SQL Server o in Azure SQL Data Warehouse rappresentato dal servizio collegato usato dalla tabella di output. |Sì |
| storedProcedureParameters |Specificare i valori dei parametri della stored procedure. Se è necessario passare null per un parametro, usare la sintassi "param1": null (tutte lettere minuscole). Vedere l'esempio seguente per informazioni sull'uso di questa proprietà. |No |

## <a name="passing-a-static-value"></a>Passaggio di un valore statico
A questo punto, si consideri l'aggiunta di un'altra colonna denominata 'Scenario' nella tabella contenente un valore statico denominato 'Document sample'.

![Dati di esempio 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabella:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Stored procedure:**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Passare ora il parametro di **Scenario** e il valore dall'attività di stored procedure. La sezione **typeProperties** nell'esempio precedente è simile al seguente frammento:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Set di dati di Data factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline di Data factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
