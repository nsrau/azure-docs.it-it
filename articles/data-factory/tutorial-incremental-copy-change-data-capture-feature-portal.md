---
title: Eseguire la copia incrementale dei dati tramite Change Data Capture
description: In questa esercitazione verrà creata una pipeline di Azure Data Factory che copia dati differenziali in modo incrementale da una tabella nel database dell'istanza gestita di SQL di Azure in Archiviazione di Azure.
services: data-factory
ms.author: nihurt
author: hurtn
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: ''
ms.date: 05/04/2020
ms.openlocfilehash: 06dd55ce400667939fca4b0f48159f8b7dde66c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825154"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Caricare dati in modo incrementale dall'istanza gestita di SQL di Azure in Archiviazione di Azure tramite la tecnologia Change Data Capture

Questa esercitazione illustra come creare una data factory di Azure con una pipeline che carica dati differenziali basati su informazioni di **Change Data Capture** nel database dell'istanza gestita di SQL di Azure di origine in una risorsa di archiviazione BLOB di Azure.  

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati di origine.
> * Creare una data factory.
> * Creare servizi collegati.
> * Creare set di dati di origine e sink.
> * Creare, eseguire il debug ed eseguire la pipeline per verificare la presenza di dati modificati
> * Modificare i dati nella tabella di origine
> * Completare, eseguire e monitorare la pipeline di copia incrementale completa

## <a name="overview"></a>Panoramica
È possibile usare la tecnologia Change Data Capture supportata da archivi dati come l'istanza gestita di SQL di Azure e SQL Server per identificare i dati modificati.  Questa esercitazione descrive come usare Azure Data Factory con la tecnologia Change Data Capture SQL per caricare in modo incrementale dati differenziali dall'istanza gestita di SQL di Azure in Archiviazione BLOB di Azure.  Per altre informazioni pratiche sulla tecnologia Change Data Capture SQL, vedere [Change Data Capture in SQL Server](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
Ecco alcuni passaggi del flusso di lavoro end-to-end tipico per caricare dati in modo incrementale usando la tecnologia Change Data Capture.

> [!NOTE]
> Sia l'istanza gestita di SQL di Azure sia SQL Server supportano la tecnologia Change Data Capture. Questa esercitazione usa l'istanza gestita di SQL di Azure come archivio dati di origine. È anche possibile usare un database di SQL Server locale.

## <a name="high-level-solution"></a>Soluzione di alto livello
In questa esercitazione verrà creata una pipeline che esegue le operazioni seguenti:  

   1. Creare un'**attività di ricerca** per contare il numero di record modificati nella tabella CDC del database SQL e passarli a un'attività Condizione if.
   2. Creare una **Condizione if** per verificare se sono presenti record modificati e, in tal caso, richiamare l'attività di copia.
   3. Creare un'**attività di copia** per copiare i dati inseriti/aggiornati/eliminati dalla tabella CDC ad Archiviazione BLOB di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* **Istanza gestita di database SQL di Azure**. Usare il database come archivio dati di **origine**. Se non si ha un'istanza gestita di database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati **sink**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-account-create.md) per informazioni su come crearne uno. Creare un contenitore denominato **raw**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Creare una tabella di origine dati nel database SQL di Azure

1. Avviare **SQL Server Management Studio** e connettersi al server delle istanze gestite di SQL di Azure.
2. In **Esplora server** fare clic con il pulsante destro del mouse sul **database** e scegliere **Nuova query**.
3. Eseguire questo comando SQL sul database delle istanze gestite di SQL di Azure per creare una tabella denominata `customers` come archivio dell'origine dati.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Abilitare il meccanismo **Change Data Capture** nel database e nella tabella di origine (customers) eseguendo la query SQL seguente:

    > [!NOTE]
    > - Sostituire il &lt;nome dello schema di origine&gt; con lo schema dell'istanza gestita di SQL di Azure contenente la tabella customers.
    > - Change Data Capture non esegue alcuna operazione come parte delle transazioni che modificano la tabella rilevata. Al contrario, le operazioni di inserimento, aggiornamento ed eliminazione vengono scritte nel log delle transazioni. Se non vengono eliminati in modo periodico e sistematico, i dati inseriti nelle tabelle delle modifiche aumenteranno notevolmente e non sarà più possibile gestirli. Per altre informazioni, vedere [Abilitare Change Data Capture per un database](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?enable-change-data-capture-for-a-database=&view=sql-server-ver15)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Per inserire i dati nella tabella customers. eseguire questo comando:

    ```sql
     insert into customers 
        (customer_id, first_name, last_name, email, city) 
     values 
        (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
        (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Non vengono acquisite modifiche cronologiche apportate alla tabella prima dell'abilitazione di Change Data Capture.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**:

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**.

     ![Pagina Nuova data factory](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).

    *Il nome della data factory "ADFTutorialDataFactory" non è disponibile.*
3. Selezionare **V2** per **version**.
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
5. Per il **gruppo di risorse**, eseguire una di queste operazioni:

   1. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.
   2. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
    Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
6. Deselezionare **Abilita GIT**.     
7. Fare clic su **Crea**.
8. Una volta completata la distribuzione, fare clic su **Vai alla risorsa**

   ![Screenshot che mostra un messaggio in cui si informa che la distribuzione è completa e un'opzione per passare alla risorsa.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.

   ![Screenshot che mostra la data factory distribuita.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.
11. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra, come mostrato nell'immagine seguente:

    ![Pulsante per la creazione di pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa sezione verranno creati i servizi collegati all'account di archiviazione di Azure e all'istanza gestita di SQL di Azure.

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure.
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory.

1. Fare clic su **Connessioni** e quindi su **+ Nuovo**.

   ![Pulsante per una nuova connessione](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**.

   ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

   1. Immettere **AzureStorageLinkedService** per **Nome**.
   2. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione**.
   3. Fare clic su **Salva**.

   ![Impostazioni account di archiviazione di Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Creare un servizio collegato al database dell'istanza gestita di SQL di Azure.
In questo passaggio viene collegato il database dell'istanza gestita di SQL di Azure alla data factory.

> [!NOTE]
> Per coloro che usano l'istanza gestita di SQL, vedere [qui](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database-managed-instance#prerequisites) per informazioni sull'accesso tramite un endpoint pubblico o un endpoint privato. Se si usa un endpoint privato, è necessario eseguire questa pipeline usando un runtime di integrazione self-hosted. Lo stesso vale per gli scenari in cui è in esecuzione SQL Server locale, in una macchina virtuale o in una rete virtuale.

1. Fare clic su **Connessioni** e quindi su **+ Nuovo**.
2. Nella finestra **Nuovo servizio collegato** selezionare **Istanza gestita di database SQL di Azure** e fare clic su **Continua**.
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

   1. Immettere **AzureSqlMI1** nel campo **Nome**.
   2. Selezionare il server SQL in uso nel campo **Nome server**.
   4. Selezionare il database SQL in uso nel campo **Nome database**.
   5. Immettere il nome dell'utente nel campo **Nome utente**.
   6. Immettere la password dell'utente nel campo **Password**.
   7. Fare clic su **Test connessione** per testare la connessione.
   8. Fare clic su **Salva** per salvare il servizio collegato.

   ![Impostazioni del servizio collegato al database dell'istanza gestita di SQL di Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio verranno creati i set di dati per rappresentare l'origine dati e la destinazione dati.

### <a name="create-a-dataset-to-represent-source-data"></a>Creare un set di dati per rappresentare i dati di origine
In questo passaggio viene creato un set di dati per rappresentare i dati di origine.

1. Nella visualizzazione albero fare clic su **+ (segno più)** e quindi su **Set di dati**.

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selezionare **Istanza gestita di database SQL di Azure** e fare clic su **Continua**.

   ![Tipo di set di dati di origine: Database SQL di Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. Nella scheda **Imposta proprietà** impostare il nome del set di dati e le informazioni di connessione:
 
   1. Selezionare **AzureSqlMI1** per **Servizio collegato**.
   2. Selezionare **[dbo].[dbo_customers_CT]** per **Nome tabella**.  Nota: questa tabella è stata creata automaticamente quando la funzionalità Change Data Capture è stata abilitata per la tabella customers. I dati modificati non vengono mai sottoposti a query direttamente da questa tabella, ma vengono invece estratti tramite le [funzioni di Change Data Capture](https://docs.microsoft.com/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql?view=sql-server-ver15).

   ![Connessione all'origine](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Creare un set di dati per rappresentare i dati copiati nell'archivio dati sink
In questo passaggio viene creato un set di dati per rappresentare i dati copiati dall'archivio dati di origine. Come parte dei prerequisiti è stato creato il contenitore data lake nell'archivio BLOB di Azure. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. In questa esercitazione il nome del file di output viene generato in modo dinamico usando l'ora di attivazione, che verrà configurata in seguito.

1. Nella visualizzazione albero fare clic su **+ (segno più)** e quindi su **Set di dati**.

   ![Menu per nuovo set di dati](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**.

   ![Tipo di set di dati sink: Archiviazione BLOB di Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Selezionare **DelimitedText** e fare clic su **Continua**.

   ![Formato del set di dati sink: DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. Nella scheda **Imposta proprietà** impostare il nome del set di dati e le informazioni di connessione:

   1. Selezionare **AzureStorageLinkedService** per **Servizio collegato**.
   2. Immettere **raw** per la parte **container** di **filePath**.
   3. Abilitare **Prima riga come intestazione**
   4. Fare clic su **OK**.

   ![Set di dati sink: connessione](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Creare una pipeline per copiare i dati modificati
In questo passaggio verrà creata una pipeline che controlla prima di tutto il numero di record modificati presenti nella tabella delle modifiche tramite un'**attività di ricerca**. Un'attività Condizione if controlla se il numero di record modificati è maggiore di zero ed esegue un'**attività di copia** per copiare i dati inseriti/aggiornati/eliminati dal database SQL di Azure alla risorsa di archiviazione BLOB di Azure. Infine, viene configurato un trigger di finestra a cascata e le ore di inizio e di fine verranno passate alle attività come parametri della finestra iniziale e finale. 

1. Nell'interfaccia utente di Data Factory passare alla scheda **Modifica**. Fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Pipeline**.

    ![Menu per nuova pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Verrà visualizzata una nuova scheda per la configurazione della pipeline. La pipeline è riportata anche nella visualizzazione albero. Nella finestra **Proprietà** modificare il nome della pipeline in **IncrementalCopyPipeline**.

    ![Nome della pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. Espandere **Generale** nella casella degli strumenti **Attività** e trascinare l'attività **Cerca** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **GetChangeCount**. Questa attività ottiene il numero di record nella tabella delle modifiche per un intervallo di tempo specificato.

    ![Attività di ricerca: nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Passare a **Impostazioni** nella finestra **Proprietà**:
   1. Specificare il nome del set di dati dell'istanza gestita di SQL per il campo **Set di dati di origine**.
   2. Selezionare l'opzione Query e immettere quanto segue nella casella query:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Abilitare **Solo prima riga**

    ![Attività di ricerca: impostazioni](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Fare clic sul pulsante **Anteprima dati** per assicurarsi che l'attività di ricerca restituisca un output valido

    ![Attività di ricerca: anteprima](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Nella casella degli strumenti **Attività** espandere **Iterazione e condizionali** e trascinare l'attività **Condizione if** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **HasChangedRows**. 

    ![Attività Condizione if: nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Passare ad **Attività** nella finestra **Proprietà**:

   1. Immettere l'**Espressione** seguente
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Fare clic sull'icona a forma di matita per modificare la condizione True.

   ![Attività Condizione if: impostazioni](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Espandere **Generali** nella casella degli strumenti **Attività** e trascinare un'attività **Attendi** nell'area di progettazione della pipeline. Si tratta di un'attività temporanea per eseguire il debug della condizione if e verrà modificata più avanti nell'esercitazione. 

   ![Condizione if True: Attendi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Fare clic sulla barra di navigazione IncrementalCopyPipeline per tornare alla pipeline principale.

8. Eseguire la pipeline in modalità **Debug** per verificare che la pipeline venga eseguita correttamente. 

   ![Pipeline: debug](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Tornare quindi al passaggio relativo alla condizione True ed eliminare l'attività **Attendi**. Nella casella degli strumenti **Attività** espandere **Sposta e trasforma** e trascinare l'attività **Copia** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **IncrementalCopyActivity**. 

   ![Attività di copia: nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Passare alla scheda **Origine** nella finestra **Proprietà** e seguire questa procedura:

   1. Specificare il nome del set di dati dell'istanza gestita di SQL per il campo **Set di dati di origine**. 
   2. Selezionare **Query** per **Use Query** (Usa query).
   3. In **Query** immettere quanto segue.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Attività di copia: impostazioni origine](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Fare clic su Anteprima per verificare che la query restituisca correttamente le righe modificate.

    ![Screenshot che mostra l'anteprima per verificare la query.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Passare alla scheda **Sink** e specificare il set di dati di archiviazione di Azure per il campo **Set di dati sink**.

    ![Screenshot che mostra la scheda Sink.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Fare clic per tornare all'area di disegno della pipeline principale e connettere l'attività **Ricerca** all'attività **Condizione if** una alla volta. Trascinare il pulsante **verde** associato all'attività **Ricerca** sull'attività **Condizione if**.

    ![Connettere le attività di ricerca e di copia](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Fare clic su **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Chiudere la finestra del **report di convalida della pipeline** facendo clic su **>>** .

    ![Pulsante Convalida](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Fare clic su Debug per testare la pipeline e verificare che nella posizione di archiviazione sia stato generato un file.

    ![Debug pipeline incrementale: 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Per pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Data Factory, fare clic sul pulsante **Pubblica tutti**. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**.

    ![Pulsante Publish](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Configurare il trigger di finestra a cascata e i parametri della finestra di Change Data Capture 
In questo passaggio verrà creato un trigger di finestra a cascata per eseguire il processo in base a una pianificazione frequente. Verranno usate le variabili di sistema WindowStart e WindowEnd del trigger di finestra a cascata che saranno passate come parametri alla pipeline da usare nella query di Change Data Capture.

1. Passare alla scheda **Parametri** della pipeline **IncrementalCopyPipeline** e usare il pulsante **+ Nuovo** per aggiungere alla pipeline due parametri (**triggerStartTime** e **triggerEndTime**), che rappresenteranno l'ora di inizio e di fine della finestra a cascata. Ai fini del debug, aggiungere i valori predefiniti nel formato **AAAA-MM-GG HH24:MI:SS.FFF** e assicurarsi che triggerStartTime non sia precedente all'ora di abilitazione di Change Data Capture nella tabella, perché in caso contrario verrà generato un errore.

    ![Menu Trigger Now (Attiva adesso)](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Fare clic sulla scheda Impostazioni dell'attività **Ricerca** e configurare la query per l'utilizzo dei parametri relativi all'ora iniziale e all'ora finale. Copiare quanto segue nella query:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Passare all'attività **Copia** nel caso True dell'attività **Condizione if** e fare clic sulla scheda **Origine**. Copiare quanto segue nella query:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Fare clic sulla scheda **Sink** dell'attività **Copia** e fare clic su **Apri** per modificare le proprietà del set di dati. Fare clic sulla scheda **Parametri** e aggiungere un nuovo parametro denominato **triggerStart**    

    ![Screenshot che mostra l'aggiunta di un nuovo parametro alla scheda Parametri.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Configurare quindi le proprietà del set di dati per archiviare i dati in una sottodirectory **customers/incremental** con le partizioni basate su data.
   1. Fare clic sulla scheda **Connessione** delle proprietà del set di dati e aggiungere contenuto dinamico per le sezioni **Directory** e **File**. 
   2. Immettere l'espressione seguente nella sezione **Directory** facendo clic sul collegamento al contenuto dinamico nella casella di testo:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Immettere l'espressione seguente nella sezione **File**. In questo modo i nomi dei file verranno creati in base alla data e all'ora di inizio del trigger, a cui verrà aggiunto un suffisso con l'estensione csv:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Configurazione set di dati sink: 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Tornare alle impostazioni del **Sink** nell'attività **Copia** facendo clic sulla scheda **IncrementalCopyPipeline**. 
   5. Espandere le proprietà del set di dati e immettere il contenuto dinamico nel valore del parametro triggerStart con l'espressione seguente:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Configurazione set di dati sink: 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Fare clic su Debug per testare la pipeline e verificare che la struttura di cartelle e il file di output vengano generati come previsto. Scaricare e aprire il file per verificarne il contenuto. 

    ![Debug copia incrementale: 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Verificare che i parametri vengano inseriti nella query esaminando i parametri di input dell'esecuzione della pipeline.

    ![Debug copia incrementale: 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Per pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Data Factory, fare clic sul pulsante **Pubblica tutti**. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**.
9. Infine, configurare un trigger di finestra a cascata per eseguire la pipeline a intervalli regolari e impostare i parametri dell'ora di inizio e di fine. 
   1. Fare clic sul pulsante **Aggiungi trigger** e selezionare **Nuova/Modifica**

   ![Aggiungere un nuovo trigger](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Immettere un nome di trigger e specificare un'ora di inizio, che è uguale all'ora di fine della finestra di debug precedente.

   ![Trigger di finestra a cascata](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Nella schermata successiva specificare rispettivamente i valori seguenti per i parametri di inizio e di fine.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Trigger di finestra a cascata: 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Si noti che il trigger verrà eseguito solo dopo la pubblicazione. Inoltre, il comportamento previsto della finestra a cascata prevede l'esecuzione di tutti gli intervalli cronologici dalla data di inizio fino a questo momento. Altre informazioni sui trigger di finestra a cascata sono disponibili [qui](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). 
  
10. In **SQL Server Management Studio** apportare alcune modifiche aggiuntive alla tabella customer eseguendo il codice SQL seguente:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Fare clic sul pulsante **Pubblica tutti**. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**.  
12. Dopo alcuni minuti la pipeline sarà stata attivata e un nuovo file sarà stato caricato in Archiviazione di Azure


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorare la pipeline di copia incrementale
1. Fare clic sulla scheda **Monitoraggio** a sinistra. L'esecuzione della pipeline verrà visualizzata nell'elenco con il relativo stato. Per aggiornare l'elenco, fare clic su **Aggiorna**. Passare il puntatore del mouse accanto al nome della pipeline per accedere all'azione Riesegui e al report sull'utilizzo.

    ![Esecuzioni di pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Per visualizzare le esecuzioni attività associate a questa esecuzione della pipeline, fare clic sul nome della pipeline. Se sono stati rilevati dati modificati, saranno presenti tre attività, inclusa l'attività di copia, in caso contrario saranno presenti solo due voci nell'elenco. Per tornare alla visualizzazione delle esecuzioni di pipeline, fare clic sul collegamento **Tutte le pipeline** in alto.

    ![Esecuzioni attività](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Esaminare i risultati
Il secondo file viene visualizzato nella cartella `customers/incremental/YYYY/MM/DD` del contenitore `raw`.

![File di output dalla copia incrementale](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla copia di file nuovi e modificati solo in base a LastModifiedDate:

> [!div class="nextstepaction"]
>[Copiare i nuovi file in base a LastModifiedDate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
