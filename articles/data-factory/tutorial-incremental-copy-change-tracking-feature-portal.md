---
title: Eseguire la copia incrementale tramite Rilevamento modifiche e Azure Data Factory | Microsoft Docs
description: "In questa esercitazione si creerà una pipeline di Azure Data Factory che copia dati differenziali in modo incrementale da più tabelle di un database di SQL Server locale a un database SQL di Azure. "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/12/2018
ms.author: jingwang
ms.openlocfilehash: 22a3972d7b2e9cf732f5dc75dd2b53b83570ee66
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Caricare dati in modo incrementale da un database SQL di Azure all'archiviazione BLOB di Azure tramite il rilevamento delle modifiche 
In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati differenziali basati su informazioni di **rilevamento delle modifiche** nel database SQL di Azure di origine in una risorsa di archiviazione BLOB di Azure.  

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati di origine.
> * Creare una data factory.
> * Creare servizi collegati. 
> * Creare set di dati di origine, sink e di rilevamento delle modifiche.
> * Creare, eseguire e monitorare la pipeline di copia completa
> * Aggiungere o aggiornare dati nella tabella di origine
> * Creare, eseguire e monitorare la pipeline di copia incrementale

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Panoramica
In una soluzione di integrazione dei dati il caricamento incrementale di dati dopo i caricamenti di dati iniziali è uno scenario ampiamente diffuso. In alcuni casi, i dati modificati entro un periodo di tempo nell'archivio dati di origine possono essere suddivisi facilmente, ad esempio con LastModifyTime e CreationTime. In alcuni casi, non esiste un modo esplicito per identificare i dati differenziali dall'ultima elaborazione dei dati. È possibile usare la tecnologia Rilevamento modifiche supportata da archivi dati come il database SQL di Azure e SQL Server per identificare i dati differenziali.  Questa esercitazione descrive come usare Azure Data Factory versione 2 per usare la tecnologia Rilevamento modifiche SQL per caricare in modo incrementare dati differenziali dal database SQL di Azure all'archiviazione BLOB di Azure.  Per altre informazioni pratiche sulla tecnologia Rilevamento modifiche SQL, vedere [Rilevamento modifiche in SQL Server](/sql/relational-databases/track-changes/about-change-tracking-sql-server). 

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
Ecco alcuni passaggi del tipico flusso di lavoro end-to-end per caricare dati in modo incrementale usando la tecnologia Rilevamento modifiche.

> [!NOTE]
> Sia il database SQL di Azure sia SQL Server supportano la tecnologia Rilevamento modifiche. Questa esercitazione usa il database SQL di Azure come archivio dati di origine. È anche possibile usare un database di SQL Server locale. 

1. **Caricamento iniziale di dati cronologici** (una sola esecuzione):
    1. Abilitare la tecnologia Rilevamento modifiche nel database SQL di Azure di origine.
    2. Ottenere il valore iniziale di SYS_CHANGE_VERSION nel database SQL di Azure come base per acquisire i dati modificati.
    3. Caricare i dati completi dal database SQL di Azure a una risorsa di archiviazione BLOB di Azure. 
2. **Caricamento incrementale di dati differenziali in base a una pianificazione** (esecuzione periodica dopo il caricamento iniziale dei dati):
    1. Ottenere i valori precedente e nuovo di SYS_CHANGE_VERSION.
    3. Caricare i dati differenziali unendo le chiavi primarie delle righe modificate (tra due valori di SYS_CHANGE_VERSION) da **sys.change_tracking_tables** ai dati nella **tabella di origine** e quindi spostare i dati differenziali nella destinazione.
    4. Aggiornare SYS_CHANGE_VERSION per il successivo caricamento differenziale.

## <a name="high-level-solution"></a>Soluzione di alto livello
In questa esercitazione vengono create due pipeline che eseguono le due operazioni seguenti:  

1. **Caricamento iniziale:** viene creata una pipeline con un'attività di copia che copia tutti i dati dall'archivio dati di origine (database SQL di Azure) all'archivio dati di destinazione (archiviazione BLOB di Azure).

    ![Caricamento completo dei dati](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Caricamento incrementale:** viene creata una pipeline con le attività seguenti, eseguita periodicamente. 
    1. Creare **due attività di ricerca** per ottenere i valori precedente e nuovo di SYS_CHANGE_VERSION dal database SQL di Azure e passarli all'attività di copia.
    2. Creare **un'attività di copia** per copiare i dati inseriti/aggiornati/eliminati tra i due valori di SYS_CHANGE_VERSION dal database SQL di Azure all'archiviazione BLOB di Azure.
    3. Creare **un'attività stored procedure** per aggiornare il valore di SYS_CHANGE_VERSION per la successiva esecuzione di pipeline.

    ![Diagramma di flusso del caricamento incrementale](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* **Database SQL di Azure**. Usare il database come archivio dati di **origine**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati **sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. Creare un contenitore denominato **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Creare una tabella di origine dati nel database SQL di Azure
1. Avviare **SQL Server Management Studio** e connettersi al server SQL di Azure. 
2. In **Esplora server** fare clic con il pulsante destro del mouse sul **database** e scegliere **Nuova query**.
3. Eseguire questo comando SQL sul database SQL di Azure per creare una tabella denominata `data_source_table` come archivio dell'origine dati.  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Abilitare il meccanismo **Rilevamento modifiche** nel database e nella tabella di origine (data_source_table) eseguendo la query SQL seguente: 

    > [!NOTE]
    > - Sostituire &lt;your database name&gt; con il nome del database SQL di Azure che include data_source_table. 
    > - Nell'esempio corrente i dati modificati vengono mantenuti per due giorni. Se i dati modificati vengono caricati ogni tre giorni o più, alcuni non verranno inclusi.  In questo caso, è necessario modificare il valore di CHANGE_RETENTION impostando un numero maggiore. In alternativa, assicurarsi che il periodo per il caricamento dei dati modificati sia non più di due giorni. Per altre informazioni, vedere [Abilitare il rilevamento delle modifiche per un database](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Creare una nuova tabella e archiviare ChangeTracking_version con un valore predefinito eseguendo la query seguente: 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > Se i dati non vengono modificati dopo aver abilitato il rilevamento delle modifiche per il database SQL, il valore della versione di rilevamento delle modifiche è 0.
6. Eseguire la query seguente per creare una stored procedure nel database SQL di Azure. La pipeline richiama questa stored procedure per aggiornare la versione di rilevamento delle modifiche nella tabella creata nel passaggio precedente. 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni descritte in [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “ADFTutorialDataFactory” is not available`
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

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.
11. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra, come mostrato nell'immagine seguente: 

    ![Pulsante per la creazione di pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa sezione si creano servizi collegati all'account di archiviazione di Azure e al database SQL di Azure. 

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure.
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory.

1. Fare clic su **Connessioni** e quindi su **+ Nuovo**.

   ![Pulsante per una nuova connessione](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**. 

   ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureStorageLinkedService** per **Nome**. 
    2. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione**. 
    3. Fare clic su **Save**. 
    
   ![Impostazioni account di archiviazione di Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure.
In questo passaggio viene collegato il database SQL di Azure alla data factory.

1. Fare clic su **Connessioni** e quindi su **+ Nuovo**.
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Database SQL di Azure** e fare clic su **Continua**. 
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureSqlDatabaseLinkedService** nel campo **Nome**. 
    2. Selezionare il server di Azure SQL nel campo **Nome server**.
    4. Selezionare il database SQL di Azure nel campo **Nome database**. 
    5. Immettere il nome dell'utente nel campo **Nome utente**. 
    6. Immettere la password dell'utente nel campo **Password**. 
    7. Fare clic su **Test connessione** per testare la connessione.
    8. Fare clic su **Salva** per salvare il servizio collegato. 
    
       ![Impostazioni servizio collegato Database SQL di Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati i set di dati per rappresentare l'origine dati, la destinazione dati e la posizione in cui archiviare SYS_CHANGE_VERSION.

### <a name="create-a-dataset-to-represent-source-data"></a>Creare un set di dati per rappresentare i dati di origine 
In questo passaggio viene creato un set di dati per rappresentare i dati di origine. 

1. Nella visualizzazione albero fare clic su **+ (segno più)** e quindi su **Set di dati**. 

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Selezionare **Database SQL di Azure** e fare clic su **Fine**. 

   ![Tipo di set di dati di origine: Database SQL di Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Verrà visualizzata una nuova scheda per la configurazione del set di dati. Il set di dati è riportato anche nella visualizzazione albero. Nella finestra **Proprietà** modificare il nome del set di dati in **SourceDataset**.

   ![Nome del set di dati di origine](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Passare alla scheda **Connessione** e seguire questa procedura: 
    
    1. Selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**. 
    2. Selezionare **[dbo].[data_source_table]** per **Tabella**. 

   ![Connessione all'origine](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Creare un set di dati per rappresentare i dati copiati nell'archivio dati sink 
In questo passaggio viene creato un set di dati per rappresentare i dati copiati dall'archivio dati di origine. Come parte dei prerequisiti è stato creato il contenitore adftutorial nell'archivio BLOB di Azure. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. In questa esercitazione, il nome del file di output viene generato in modo dinamico con l'espressione `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. Nella visualizzazione albero fare clic su **+ (segno più)** e quindi su **Set di dati**. 

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Selezionare **Archiviazione BLOB di Azure** e fare clic su **Fine**. 

   ![Tipo di set di dati sink: Archiviazione BLOB di Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Verrà visualizzata una nuova scheda per la configurazione del set di dati. Il set di dati è riportato anche nella visualizzazione albero. Nella finestra **Proprietà** modificare il nome del set di dati in **SinkDataset**.

   ![Set di dati sink: nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Passare alla scheda **Connessione** nella finestra delle proprietà e seguire questa procedura:

    1. Selezionare **AzureStorageLinkedService** per **Servizio collegato**.
    2. Immettere **adftutorial/incchgtracking** nella parte relativa alla **cartella** di **Percorso file**.
    3. Immettere **@CONCAT('Incremental-', pipeline().RunId, '.txt')** nella parte relativa al **file** di **Percorso file**.  

       ![Set di dati sink: connessione](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Creare un set di dati per rappresentare i dati di rilevamento delle modifiche 
In questo passaggio viene creato un set di dati per l'archiviazione della versione di rilevamento delle modifiche.  Come parte dei prerequisiti è stata creata la tabella table_store_ChangeTracking_version.

1. Nella visualizzazione albero fare clic su **+ (segno più)** e quindi su **Set di dati**. 
2. Selezionare **Database SQL di Azure** e fare clic su **Fine**. 
3. Verrà visualizzata una nuova scheda per la configurazione del set di dati. Il set di dati è riportato anche nella visualizzazione albero. Nella finestra **Proprietà** modificare il nome del set di dati in **ChangeTrackingDataset**.
4. Passare alla scheda **Connessione** e seguire questa procedura: 
    
    1. Selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**. 
    2. Selezionare **[dbo].[table_store_ChangeTracking_version]** per **Tabella**. 

## <a name="create-a-pipeline-for-the-full-copy"></a>Creare una pipeline per la copia completa
In questo passaggio viene creata una pipeline con un'attività di copia che copia tutti i dati dall'archivio dati di origine (database SQL di Azure) all'archivio dati di destinazione (Archiviazione BLOB di Azure).

1. Fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Pipeline**. 

    ![Menu per nuova pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Verrà visualizzata una nuova scheda per la configurazione della pipeline. La pipeline è riportata anche nella visualizzazione albero. Nella finestra **Proprietà** modificare il nome della pipeline in **FullCopyPipeline**.

    ![Menu per nuova pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. Nella casella degli strumenti **Attività** espandere **Flusso di dati**, trascinare l'attività **Copia** nell'area di progettazione della pipeline e impostare il nome **FullCopyActivity**. 

    ![Attività di copia completa: nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Passare alla scheda **Origine** e selezionare **SourceDataset** nel campo **Source Dataset** (Set di dati di origine). 

    ![Attività di copia: origine](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Passare alla scheda **Sink** e selezionare **SinkDataset** nel campo **Sink Dataset** (Set di dati sink). 

    ![Attività di copia: sink](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Per convalidare la definizione della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Verificare che non sia presente alcun errore di convalida. Chiudere il **report di convalida della pipeline** facendo clic su **>>**. 

    ![Convalidare la pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Per pubblicare le entità (servizi collegati, set di dati e pipeline), fare clic su **Pubblica**. Attendere fino al completamento della pubblicazione. 

    ![Pulsante Publish](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. 

    ![Pubblicazione riuscita](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. È anche possibile visualizzare le notifiche facendo clic sul pulsante **Show Notifications** (Mostra notifiche) a sinistra. Per chiudere la finestra delle notifiche, fare clic su **X**.

    ![Visualizzare le notifiche](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Eseguire la pipeline di copia completa
Fare clic su **Trigger** sulla barra degli strumenti per la pipeline e quindi su **Trigger Now** (Attiva adesso). 

![Menu Trigger Now (Attiva adesso)](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Monitorare la pipeline di copia completa

1. Fare clic sulla scheda **Monitoraggio** a sinistra. L'esecuzione della pipeline verrà visualizzata nell'elenco con il relativo stato. Per aggiornare l'elenco, fare clic su **Aggiorna**. I collegamenti nella colonna Azioni consentono di visualizzare le esecuzioni di attività associate all'esecuzione della pipeline ed eseguire di nuovo la pipeline. 

    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività, nell'elenco viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, fare clic sul collegamento **Pipeline** in alto. 

    ![Esecuzioni attività](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Esaminare i risultati
Viene visualizzato un file denominato `incremental-<GUID>.txt` nella cartella `incchgtracking` del contenitore `adftutorial`. 

![File di output dalla copia completa](media\tutorial-incremental-copy-change-tracking-feature-portal\full-copy-output-file.png)

Il file conterrà i dati del database SQL di Azure:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Aggiungere altri dati alla tabella di origine

Eseguire la query seguente sul database SQL di Azure per aggiungere una riga e aggiornare una riga. 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>Creare una pipeline per la copia differenziale
In questo passaggio viene creata una pipeline con le attività seguenti, eseguita periodicamente. Le **attività di ricerca** ottengono i valori precedente e nuovo di SYS_CHANGE_VERSION dal database SQL di Azure e li passano all'attività di copia. L'**attività di copia** copia i dati inseriti/aggiornati/eliminati tra i due valori di SYS_CHANGE_VERSION dal database SQL di Azure all'archiviazione BLOB di Azure. L'**attività stored procedure** aggiorna il valore di SYS_CHANGE_VERSION per la successiva esecuzione di pipeline.

1. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**. Fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Pipeline**. 

    ![Menu per nuova pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Verrà visualizzata una nuova scheda per la configurazione della pipeline. La pipeline è riportata anche nella visualizzazione albero. Nella finestra **Proprietà** modificare il nome della pipeline in **IncrementalCopyPipeline**.

    ![Nome della pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. Espandere **Database SQL** nella casella degli strumenti **Attività** e trascinare l'attività **Cerca** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **LookupLastChangeTrackingVersionActivity**. Questa attività recupera la versione del rilevamento modifiche usata nell'ultima operazione di copia archiviata nella tabella **table_store_ChangeTracking_version**.

    ![Attività di ricerca: nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Passare a **Impostazioni** nella finestra **Proprietà** e selezionare **ChangeTrackingDataset** nel campo **Source Dataset** (Set di dati di origine). 

    ![Attività di ricerca: impostazioni](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Trascinare l'attività **Cerca** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. Impostare il nome dell'attività su **LookupCurrentChangeTrackingVersionActivity**. Questa attività recupera la versione corrente del rilevamento modifiche.

    ![Attività di ricerca: nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Passare a **Impostazioni** nella finestra **Proprietà** e seguire questa procedura:

    1. Selezionare **SourceDataset** nel campo **Source Dataset** (Set di dati di origine).
    2. Selezionare **Query** per **Use Query** (Usa query). 
    3. Immettere la query SQL seguente per **Query**. 

        ```sql
        SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
        ```

    ![Attività di ricerca: impostazioni](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. Nella casella degli strumenti **Attività** espandere **Flusso di dati** e trascinare l'attività **Copia** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **IncrementalCopyActivity**. Questa attività copia i dati tra l'ultima versione del rilevamento modifiche e quella corrente nell'archivio dati di destinazione. 

    ![Attività di copia: nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Passare alla scheda **Origine** nella finestra **Proprietà** e seguire questa procedura:

    1. Selezionare **SourceDataset** per **Source Dataset** (Set di dati di origine). 
    2. Selezionare **Query** per **Use Query** (Usa query). 
    3. Immettere la query SQL seguente per **Query**. 

        ```sql
        select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
        ```
    
    ![Attività di copia: impostazioni origine](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Passare alla scheda **Sink** e selezionare **SinkDataset** nel campo **Sink Dataset** (Set di dati sink). 

    ![Attività di copia: impostazioni sink](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. Una alla volta, **connettere entrambe le attività di ricerca all'attività di copia**. Trascinare il pulsante **verde** associato all'attività **Cerca** sull'attività **Copia**. 

    ![Connettere le attività di ricerca e di copia](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Trascinare l'attività **Stored procedure** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. Impostare il nome dell'attività su **StoredProceduretoUpdateChangeTrackingActivity**. Questa attività aggiorna la versione del rilevamento modifiche nella tabella **table_store_ChangeTracking_version**.

    ![Attività stored procedure: nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Passare alla scheda *Account SQL* e selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**. 

    ![Attività stored procedure: account SQL](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Passare alla scheda **Stored procedure** e seguire questa procedura: 

    1. Immettere **Update_ChangeTracking_Version** per **Nome stored procedure**.  
    2. Nella sezione **Parametri stored procedure** usare il pulsante **+ Nuovo** per aggiungere i due parametri seguenti:

        | NOME | type | Valore | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | INT64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | string | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![Attività stored procedure: parametri](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Connettere l'attività di copia all'attività stored procedure**. Trascinare il pulsante **verde** associato all'attività Copia sull'attività Stored procedure. 

    ![Connettere le attività di copia e stored procedure](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Fare clic su **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Chiudere la finestra del **report di convalida della pipeline** facendo clic su **>>**. 

    ![Pulsante Convalida](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16.  Pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Data Factory facendo clic sul pulsante **Pubblica**. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. 

        ![Pulsante Publish](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Eseguire la pipeline di copia incrementale
Fare clic su **Trigger** sulla barra degli strumenti per la pipeline e quindi su **Trigger Now** (Attiva adesso). 

![Menu Trigger Now (Attiva adesso)](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)

### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorare la pipeline di copia incrementale
1. Fare clic sulla scheda **Monitoraggio** a sinistra. L'esecuzione della pipeline verrà visualizzata nell'elenco con il relativo stato. Per aggiornare l'elenco, fare clic su **Aggiorna**. I collegamenti nella colonna **Azioni** consentono di visualizzare le esecuzioni di attività associate all'esecuzione della pipeline ed eseguire di nuovo la pipeline. 

    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività, nell'elenco viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, fare clic sul collegamento **Pipeline** in alto. 

    ![Esecuzioni attività](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Esaminare i risultati
Il secondo file viene visualizzato nella cartella `incchgtracking` del contenitore `adftutorial`. 

![File di output dalla copia incrementale](media\tutorial-incremental-copy-change-tracking-feature-portal\incremental-copy-output-file.png)

Il file conterrà solo i dati differenziali del database SQL di Azure. Il record con `U` è la riga aggiornata nel database e `I` è la riga aggiunta. 

```
1,update,10,2,U
6,new,50,1,I
```
Le prime tre colonne sono i dati modificati da data_source_table. Le ultime due colonne sono i metadati dalla tabella di sistema del rilevamento delle modifiche. La quarta colonna è SYS_CHANGE_VERSION per ogni riga modificata. La quinta colonna è l'operazione:  U = aggiornamento, I = inserimento.  Per tutti i dettagli sulle informazioni di rilevamento delle modifiche, vedere [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati usando un cluster Spark nel cloud](tutorial-transform-data-spark-powershell.md)



