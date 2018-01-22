---
title: Eseguire la copia incrementale di una tabella usando Azure Data Factory | Microsoft Docs
description: In questa esercitazione si crea una pipeline di Azure Data Factory che copia i dati in modo incrementale da un database SQL di Azure a un archivio BLOB di Azure.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: af01fac2c552e038377f5b394fecbe3ad9fd3acc
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Caricare i dati in modo incrementale da un database SQL di Azure a un archivio BLOB di Azure
In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati delta da una tabella di un database SQL di Azure a un archivio BLOB di Azure. 


> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione di Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati per l'archiviazione del valore limite.
> * Creare una data factory.
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare una pipeline.
> * Eseguire la pipeline.
> * Monitorare l'esecuzione della pipeline. 
> * Esaminare i risultati
> * Aggiungere altri dati all'origine.
> * Eseguire di nuovo la pipeline.
> * Monitorare la seconda esecuzione della pipeline.
> * Esaminare i risultati della seconda esecuzione.


## <a name="overview"></a>Panoramica
Il diagramma generale della soluzione è il seguente: 

![Caricare i dati in modo incrementale](media\tutorial-Incremental-copy-portal\incrementally-load.png)

Di seguito sono descritti i passaggi fondamentali per la creazione di questa soluzione: 

1. **Selezionare la colonna del limite**.
    Selezionare una colonna nell'archivio dati di origine, che può essere usata per analizzare approfonditamente i record nuovi o aggiornati per ogni esecuzione. I dati della colonna selezionata (ad esempio last_modify_time o ID) continuano in genere ad aumentare quando le righe vengono create o aggiornate. Il valore massimo di questa colonna viene usato come limite.

2. **Preparare un archivio dati per l'archiviazione del valore limite**. In questa esercitazione si archivia il valore limite in un database SQL.
    
3. **Creare una pipeline con il flusso di lavoro seguente**: 
    
    La pipeline di questa soluzione contiene le attività seguenti:
  
    * Creare due attività di ricerca. Usare la prima attività di ricerca per recuperare l'ultimo valore limite. Usare la seconda attività di ricerca per recuperare il nuovo valore limite. Questi valori limite vengono passati all'attività di copia. 
    * Creare un'attività di copia che copi le righe dell'archivio dati di origine con il valore della colonna del limite maggiore del valore limite precedente e minore del nuovo valore limite. L'attività copierà quindi i dati delta dall'archivio dati di origine a un archivio BLOB come nuovo file. 
    * Creare un'attività stored procedure che aggiorni il valore limite per la pipeline che verrà eseguita la volta successiva. 


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* **Database SQL di Azure**. Usare il database come archivio dati di origine. Se non è disponibile un database SQL, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md) per crearne uno.
* **Archiviazione di Azure**. Usare l'archivio BLOB come archivio dati sink. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. Creare un contenitore denominato adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Creare una tabella di origine dati nel database SQL
1. Aprire SQL Server Management Studio. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

2. Eseguire questo comando SQL sul database SQL per creare una tabella denominata `data_source_table` come archivio dell'origine dati: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    In questa esercitazione si usa LastModifytime come colonna del valore limite. I dati nell'archivio dell'origine dati sono visualizzati nella tabella seguente:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Creare un'altra tabella nel database SQL per archiviare il valore del limite massimo
1. Eseguire questo comando SQL sul database SQL per creare una tabella denominata `watermarktable` in cui archiviare il valore limite:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Impostare il valore predefinito del limite massimo con il nome tabella dell'archivio dei dati di origine. In questa esercitazione, il nome della tabella è data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Esaminare i dati nella tabella `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Output: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Creare una stored procedure nel database SQL 

Eseguire questo comando per creare una stored procedure nel database SQL:

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFIncCopyTutorialDF** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato un punto esclamativo rosso con l'errore seguente, modificare il nome della data factory, ad esempio nomeutenteADFIncCopyTutorialDF, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
        Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.      
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline
In questa esercitazione si crea una pipeline con due attività di ricerca, un'attività di copia e un'attività di stored procedure concatenate in una pipeline. 

1. Nella pagina **Attività iniziali** dell'interfaccia utente di Data Factory fare clic sul riquadro **Create pipeline** (Crea pipeline). 

   ![Pagina Attività iniziali dell'interfaccia utente di Data Factory](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. Nella pagina **Generale** della finestra **Proprietà** per la pipeline immettere il nome **IncrementalCopyPipeline**. 

   ![Nome della pipeline](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Verrà ora aggiunta la prima attività di ricerca per recuperare il valore limite precedente. Nella casella degli strumenti **Attività** espandere **Database SQL** e trascinare l'attività **Cerca** nell'area di progettazione della pipeline. Modificare il nome dell'attività in **LookupOldWaterMarkActivity**.

   ![Prima attività di ricerca: nome](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Passare alla scheda **Impostazioni** e fare clic su **+ Nuovo** in corrispondenza di **Source Dataset** (Set di dati di origine). In questo passaggio viene creato un set di dati per rappresentare i dati in **watermarktable**. Questa tabella contiene il limite precedente che è stato usato nella precedente operazione di copia. 

   ![Menu per nuovo set di dati: limite precedente](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Fine**. Verrà aperta una nuova scheda per il set di dati. 

   ![Selezionare il database SQL di Azure](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. Nella finestra delle proprietà per il set di dati immettere **WatermarkDataset** per **Nome**.

   ![Set di dati per limite: nome](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Passare alla scheda **Connessione** e fare clic su **+ Nuovo** per stabilire una connessione al database SQL di Azure, ossia creare un servizio collegato. 

   ![Pulsante Nuovo per il servizio collegato](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **AzureSqlDatabaseLinkedService** per **Nome**. 
    2. Selezionare il server di Azure SQL per **Nome server**.
    3. Immettere il **nome utente** per accedere al server di Azure SQL. 
    4. Immettere la **password** dell'utente. 
    5. Per testare la connessione al database SQL di Azure, fare clic su **Test connessione**.
    6. Fare clic su **Save**.
    7. Nella scheda **Connessione** verificare che per **Servizio collegato** sia selezionato **AzureSqlDatabaseLinkedService**.
       
        ![Finestra New linked service (Nuovo servizio collegato)](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Selezionare **[dbo].[watermarktable]** per **Tabella**. Se si vuole visualizzare un'anteprima dei dati nella tabella, fare clic su **Anteprima dati**.

    ![Set di dati limite: impostazioni di connessione](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Passare all'editor di pipeline facendo clic sulla scheda della pipeline in alto oppure sul nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività **Cerca** verificare che nel campo **Source Dataset** (Set di dati di origine) sia selezionato **WatermarkDataset**. 

    ![Pipeline: set di dati per limite precedente](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. Nella casella degli strumenti **Attività** espandere **Database SQL**, trascinare un'altra attività **Cerca** nell'area di progettazione della pipeline e impostare il nome su **LookupNewWaterMarkActivity** nella scheda **Generale** della finestra delle proprietà. Questa attività di ricerca recupera il nuovo valore limite dalla tabella con i dati di origine da copiare nella destinazione. 

    ![Seconda attività di ricerca: nome](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. Nella finestra delle proprietà per la seconda attività **Cerca** passare alla scheda **Impostazioni** e fare clic su **Nuovo**. Si creerà un set di dati per fare riferimento alla tabella di origine contenente il nuovo valore limite, ossia il valore massimo di LastModifyTime. 

    ![Seconda attività di ricerca: nuovo set di dati](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Fine**. Verrà aperta una nuova scheda per questo set di dati, che è riportato anche nella visualizzazione albero. 
15. Nella scheda **Generale** della finestra delle proprietà immettere **SourceDataset** per **Nome**. 

    ![Set di dati di origine: nome](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Passare alla scheda **Connessione** e seguire questa procedura: 

    1. Selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.
    2. Selezionare **[dbo].[data_source_table]** per Tabella. Più avanti nell'esercitazione si specificherà una query su questo set di dati. La query avrà la precedenza rispetto alla tabella specificata in questo passaggio. 

        ![Seconda attività di ricerca: nuovo set di dati](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Passare all'editor di pipeline facendo clic sulla scheda della pipeline in alto oppure sul nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività **Cerca** verificare che nel campo **Source Dataset** (Set di dati di origine) sia selezionato **SourceDataset**. 
18. Selezionare **Query** per il campo **Use Query** (Usa query) e immettere la query seguente, con cui si seleziona solo il valore massimo di **LastModifytime** da **data_source_table**. Senza questa query, il set di dati recupera tutte le righe della tabella in base al nome di tabella specificato (data_source_table) nella definizione del set di dati.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Seconda attività di ricerca: query](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Nella casella degli strumenti **Attività** espandere **Flusso di dati**, trascinare l'attività **Copia** dalla casella degli strumenti Attività e impostare il nome su **IncrementalCopyActivity**. 

    ![Attività di copia: nome](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Connettere entrambe le attività di ricerca all'attività di copia** trascinando il **pulsante verde** associato alle attività di ricerca sull'attività di copia. Rilasciare il pulsante del mouse quando il bordo dell'attività di copia diventa di colore blu. 

    ![Connessione delle attività di ricerca all'attività di copia](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Selezionare l'attività **Copia** e verificare che le relative proprietà vengano visualizzate nella finestra **Proprietà**. 

    ![Proprietà dell'attività di copia](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Passare alla scheda **Origine** nella finestra **Proprietà** e seguire questa procedura:

    1. Selezionare **SourceDataset** nel campo **Source Dataset** (Set di dati di origine). 
    2. Selezionare **Query** per il campo **Use Query** (Usa query). 
    3. Immettere la query SQL seguente nel campo **Query**. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Attività di copia: origine](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Passare alla scheda **Sink** e fare clic su **+ Nuovo** in corrispondenza del campo **Sink Dataset** (Set di dati sink). 

    ![Pulsante per un nuovo set di dati](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. In questa esercitazione, l'archivio di dati sink è un archivio BLOB di Azure. Selezionare quindi **Archiviazione BLOB di Azure** e fare clic su **Fine** nella finestra **Nuovo set di dati**. 

    ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. Nella scheda **Generale** della finestra delle proprietà per il set di dati immettere **SinkDataset** per **Nome**. 

    ![Set di dati sink: nome](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Passare alla scheda **Connessione** e fare clic su **+ Nuovo**. In questo passaggio si crea una connessione all'**archivio BLOB di Azure**, ossia un servizio collegato.

    ![Set di dati sink: nuova connessione](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureStorageLinkedService** per **Nome**. 
    2. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione**.
    3. Fare clic su **Save**. 

        ![Servizio collegato Archiviazione di Azure: impostazioni](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. Nella scheda **Connessione** seguire questa procedura:

    1. Verificare che per **Servizio collegato** sia selezionato **AzureStorageLinkedService**. 
    2. Nella parte relativa alla **cartella** del campo **Percorso file** immettere **adftutorial/incrementalcopy**. **adftutorial** è il nome del contenitore BLOB e **incrementalcopy** è il nome della cartella. Questo frammento di codice presuppone che nell'archivio BLOB sia presente un contenitore BLOB denominato adftutorial. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. Se non esiste, la cartella di output **incrementalcopy** viene creata automaticamente da Azure Data Factory. È anche possibile usare il pulsante **Sfoglia** per **Percorso file** per passare a una cartella in un contenitore BLOB. .RunId, '.txt')`.
    3. Nella parte relativa al **nome file** del campo **Percorso file** immettere `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. Il nome file viene generato in modo dinamico usando l'espressione. Ogni esecuzione della pipeline ha un ID univoco, che viene usato dall'attività di copia per generare il nome file. 

        ![Set di dati sink: impostazioni di connessione](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Passare all'editor di **pipeline** facendo clic sulla scheda della pipeline in alto oppure sul nome della pipeline nella visualizzazione albero a sinistra. 
29. Nella casella degli strumenti **Attività** espandere **Database SQL** e trascinare l'attività **Stored procedure** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. **Connettere** l'output contrassegnato in verde (come operazione riuscita) dell'attività **Copia** all'attività **Stored procedure**. 
    
    ![Attività di copia: origine](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Selezionare l'attività **Stored procedure** nella finestra di progettazione della pipeline e modificarne il nome in **StoredProceduretoWriteWatermarkActivity**. 

    ![Attività stored procedure: nome](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Passare alla scheda **Account SQL** e selezionare *AzureSqlDatabaseLinkedService* per **Servizio collegato**. 

    ![Attività stored procedure: account SQL](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Passare alla scheda **Stored procedure** e seguire questa procedura: 

    1. Immettere **sp_write_watermark** per **Nome stored procedure**. 
    2. Per specificare i valori per i parametri della stored procedure, fare clic su **+ Nuovo** nella sezione **Parametri stored procedure** e immettere i valori seguenti: 

        | NOME | type | Valore | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue |
        | TableName | string | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Attività stored procedure: impostazioni della stored procedure](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Per convalidare le impostazioni della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Per chiudere la finestra del **report di convalida della pipeline** fare clic su >>.   

    ![Convalidare la pipeline](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Azure Data Factory facendo clic sul pulsante **Pubblica**. Attendere fino alla visualizzazione del messaggio che informa che la pubblicazione è riuscita. 

    ![Pulsante Publish](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

![Pulsante Trigger Now (Attiva adesso)](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzato lo stato dell'esecuzione della pipeline attivata dal trigger manuale. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco. 
    
    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul primo collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. È possibile tornare alla visualizzazione precedente facendo clic su **Pipeline** in alto. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco.

    ![Esecuzioni attività](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Esaminare i risultati
1. Connettersi all'account di archiviazione di Azure usando strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Verificare che sia stato creato un file di output nella cartella **incrementalcopy** del contenitore **adftutorial**.

    ![Primo file di output](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Aprire il file di output. Come si può notare, tutti i dati sono stati copiati da **data_source_table** al file BLOB. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Verificare il valore più recente da `watermarktable`. Si noterà che il valore limite è stato aggiornato.

    ```sql
    Select * from watermarktable
    ```
    
    Ecco l'output:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Aggiungere altri dati all'origine

Inserire nuovi dati nel database SQL (archivio dell'origine dati).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

I dati aggiornati nel database SQL sono i seguenti:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Attivare un'altra esecuzione della pipeline
1. Passare alla scheda **Modifica**. Fare clic sulla pipeline nella visualizzazione albero, se non è aperta nella finestra di progettazione. 

    ![Pulsante Trigger Now (Attiva adesso)](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

    ![Pulsante Trigger Now (Attiva adesso)](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Monitorare la seconda esecuzione della pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzato lo stato dell'esecuzione della pipeline attivata dal trigger manuale. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco. 
    
    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul primo collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. È possibile tornare alla visualizzazione precedente facendo clic su **Pipeline** in alto. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco.

    ![Esecuzioni attività](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>Verificare il secondo output

1. Nell'archivio BLOB è stato creato un altro file. In questa esercitazione, il nome del nuovo file è `Incremental-<GUID>.txt`. Aprendo il file si noteranno due righe di record.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Verificare il valore più recente da `watermarktable`. Si noterà che il valore limite è stato aggiornato di nuovo.

    ```sql
    Select * from watermarktable
    ```
    Output di esempio: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Preparare l'archivio dati per l'archiviazione del valore limite.
> * Creare una data factory.
> * Creare servizi collegati. 
> * Creare i set di dati di origine, sink e limite.
> * Creare una pipeline.
> * Eseguire la pipeline.
> * Monitorare l'esecuzione della pipeline. 
> * Esaminare i risultati
> * Aggiungere altri dati all'origine.
> * Eseguire di nuovo la pipeline.
> * Monitorare la seconda esecuzione della pipeline.
> * Esaminare i risultati della seconda esecuzione.

In questa esercitazione, la pipeline ha copiato dati da una singola tabella di un database SQL a un archivio BLOB. Passare all'esercitazione successiva per informazioni sulla copia di dati da più tabelle di un database di SQL Server locale a un database SQL. 

> [!div class="nextstepaction"]
>[Caricare dati in modo incrementale da più tabelle in SQL Server al database SQL di Azure](tutorial-incremental-copy-multiple-tables-powershell.md)



