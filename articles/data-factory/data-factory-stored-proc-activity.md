---
title: "Attività di stored procedure di SQL Server"
description: "Informazioni sull&quot;uso dell&quot;attività di stored procedure di SQL Server da una pipeline di Data factory per richiamare una stored procedure in un database SQL di Azure o in Azure SQL Data Warehouse."
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
ms.date: 09/30/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 1b2514e1e6f39bb3ce9d8a46f4af01835284cdcc
ms.openlocfilehash: 3510b0446cf3c1a7ffb3ff02a4d84d24ead1cae7


---
# <a name="sql-server-stored-procedure-activity"></a>Attività di stored procedure di SQL Server
> [!div class="op_single_selector"]
> [Hive](data-factory-hive-activity.md)  
> [Pig](data-factory-pig-activity.md)  
> [MapReduce](data-factory-map-reduce.md)  
> [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> [Stored procedure](data-factory-stored-proc-activity.md)
> [U-SQL per Data Lake Analytics](data-factory-usql-activity.md)
> [.NET personalizzato](data-factory-use-custom-activities.md)
>
>

È possibile usare l'attività di stored procedure di SQL Server in una [pipeline](data-factory-create-pipelines.md) di Data factory per richiamare una stored procedure in uno degli archivi dati seguenti.

* Database SQL di Azure
* Azure SQL Data Warehouse  
* Database SQL Server in una VM di Azure o dell'azienda. È necessario installare Gateway di gestione dati nello stesso computer che ospita il database o in un computer separato per evitare che competa per le risorse con il database. Gateway di gestione dati è un software che connette le origini dati locali o ospitate in macchine virtuali di Azure ai servizi cloud, in modo sicuro e gestito. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati.

Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

## <a name="walkthrough"></a>Procedura dettagliata
### <a name="sample-table-and-stored-procedure"></a>Tabella di esempio e stored procedure
1. Creare la seguente **tabella** nel database SQL di Azure usando SQL Server Management Studio o qualsiasi altro strumento conosciuto. La colonna datetimestamp riporta la data e l'ora in cui viene generato l'ID corrispondente.

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    La colonna ID riporta l'identificatore univoco, mentre la colonna datetimestamp riporta la data e l'ora in cui viene generato l'ID corrispondente.
    ![Dati di esempio](./media/data-factory-stored-proc-activity/sample-data.png)

   > [!NOTE]
   > Questo esempio usa il database SQL di Azure, ma funziona nello stesso modo per Azure SQL Data Warehouse e il database di SQL Server.
   >
   >
2. Creare la seguente **stored procedure** che inserisce dati in **sampletable**.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS

        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

   > [!IMPORTANT]
   > Il **nome** e la **combinazione di maiuscole e minuscole** per il parametro (DateTime in questo esempio) devono corrispondere a quelli del parametro specificato nel codice JSON per la pipeline/attività. Nella definizione della stored procedure assicurarsi che **@** sia usato come prefisso per il parametro.
   >
   >

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
Dopo aver creato la data factory, si crea un servizio collegato SQL di Azure che collega il database SQL di Azure alla data factory. Il database contiene la tabella sampletable e la stored procedure sp_sample.

1. Fare clic su **Creare e distribuire** nel pannello **Data Factory** per **SProcDF** per avviare l'editor di Data Factory.
2. Fare clic su **Nuovo archivio dati** sul barra dei comandi e scegliere **Database SQL di Azure**. Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato SQL di Azure.

   ![Nuovo archivio dati](media/data-factory-stored-proc-activity/new-data-store.png)
3. Nello script JSON apportare le modifiche seguenti:

   1. Sostituire **&lt;servername&gt;** con il nome del server del database SQL di Azure.
   2. Sostituire **&lt;databasename&gt;** con il database in cui viene creata la tabella e la stored procedure.
   3. Sostituire **&lt;username@servername&gt;** con l'account utente che dispone dell'accesso al database.
   4. Sostituire la **&lt;password&gt;** con la password dell'account utente.

      ![Nuovo archivio dati](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato. Assicurarsi che AzureSqlLinkedService sia visibile nella visualizzazione albero sulla sinistra.

    ![Visualizzazione albero con servizio collegato](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Creare un set di dati di output
1. Fare clic su **... Altro** sulla barra degli strumenti, fare clic su **Nuovo set di dati**, fare clic su **SQL Azure**. **Nuovo set di dati** sulla barra dei comandi e selezionare **Azure SQL**.

    ![Visualizzazione albero con servizio collegato](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copiare e incollare il seguente script JSON nell'editor JSON.

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
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati. Assicurarsi che il set di dati sia visibile nella visualizzazione albero.

    ![Visualizzazione albero con servizi collegati](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Creare una pipeline con l'attività SqlServerStoredProcedure
Si crea ora una pipeline con un'attività SqlServerStoredProcedure.

1. Fare clic su **... Altro** sulla barra dei comandi e quindi su **Nuova pipeline**.
2. Copiare e incollare il frammento JSON seguente. Impostare **storedProcedureName** su **sp_sample**. Il nome e la combinazione di maiuscole e minuscole del parametro **DateTime** deve corrispondere al nome e alla combinazione di maiuscole e minuscole del parametro nella definizione della stored procedure.  

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
                 "start": "2016-08-02T00:00:00Z",
                 "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    Se è necessario passare null per un parametro, usare la sintassi "param1": null (tutte lettere minuscole).
3. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire la pipeline.  

### <a name="monitor-the-pipeline"></a>Monitorare la pipeline
1. Fare clic su **X** per chiudere i pannelli dell'editor di Data Factory, tornare al pannello Data Factory e quindi fare clic su **Diagramma**.

    ![Riquadro Diagramma](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. In **Vista diagramma**saranno visualizzati una panoramica delle pipeline e i set di dati usati in questa esercitazione.

    ![Riquadro Diagramma](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. In Vista diagramma fare doppio clic sul set di dati **sprocsampleout**. Verranno visualizzate le sezioni con stato pronto. Dovrebbero essere presenti cinque sezioni perché dal JSON viene generata una sezione ogni ora tra l'ora di inizio e l'ora di fine.

    ![Riquadro Diagramma](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando lo stato di una sezione è **Ready**, eseguire una query **select * from sampletable** sul database SQL di Azure per verificare che la stored procedure abbia inserito i dati nella tabella.

   ![Dati di output](./media/data-factory-stored-proc-activity/output.png)

   Vedere [Monitorare la pipeline](data-factory-monitor-manage-pipelines.md) per informazioni dettagliate sul monitoraggio delle pipeline di Data Factory di Azure.  

> [!NOTE]
> In questo esempio l'attività SprocActivitySample non contiene dati di input. Per concatenarla con un'attività upstream (ovvero l'elaborazione precedente), è possibile usare gli output dell'attività upstream come input di questa attività. In questo caso, l'attività non verrà eseguita finché l'attività upstream non sarà completata e gli output delle attività upstream non saranno disponibili (in stato Ready). Non è possibile usare gli input direttamente come parametri dell'attività di stored procedure.
>
>

## <a name="json-format"></a>Formato JSON
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

## <a name="json-properties"></a>Proprietà JSON
| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome dell'attività |Sì |
| Descrizione |Testo descrittivo per lo scopo dell'attività |No |
| type |SqlServerStoredProcedure |Sì |
| inputs |Facoltativo. Se si specifica un set di dati di input, questo dovrà essere disponibile (in stato 'Ready') per l'esecuzione dell'attività della stored procedure. Il set di dati di input non può essere usato nella stored procedure come parametro. Viene usato solo per verificare la dipendenza prima di iniziare l'attività della stored procedure. |No |
| outputs |È necessario specificare un set di dati di output per un'attività della stored procedure. Il set di dati di output specifica la **pianificazione** per le attività della stored procedure (ogni ora, ogni settimana, ogni mese e così via). <br/><br/>Il set di dati di output deve usare un **servizio collegato** che faccia riferimento a un database SQL di Azure, a un Azure SQL Data Warehouse o a un database SQL Server in cui si vuole che venga eseguita la stored procedure. <br/><br/>Il set di dati di output può essere usato per passare il risultato della stored procedure per la successiva elaborazione da parte di un'altra attività ([concatenamento di attività](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence)) nella pipeline. Data Factory non scrive tuttavia automaticamente l'output di una stored procedure in questo set di dati. È la stored procedure a scrivere dati in una tabella SQL cui punta il set di dati di output. <br/><br/>In alcuni casi, il set di dati di output può essere un **set di dati fittizio**che viene usato solo per specificare la pianificazione per l'esecuzione dell'attività della stored procedure. |Sì |
| storedProcedureName |Specificare il nome della stored procedure nel database SQL di Azure o Azure SQL Data Warehouse rappresentato dal servizio collegato che usa la tabella di output. |Sì |
| storedProcedureParameters |Specificare i valori dei parametri della stored procedure. Se è necessario passare null per un parametro, usare la sintassi "param1": null (tutte lettere minuscole). Vedere l'esempio seguente per informazioni sull'uso di questa proprietà. |No |

## <a name="passing-a-static-value"></a>Passaggio di un valore statico
A questo punto, si consideri l'aggiunta di un'altra colonna denominata 'Scenario' nella tabella contenente un valore statico denominato 'Document sample'.

![Dati di esempio 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)

    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

Passare ora il parametro di Scenario e il valore dall'attività della stored procedure. La sezione typeProperties nell'esempio precedente è simile al frammento seguente:

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters":
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }



<!--HONumber=Nov16_HO3-->


