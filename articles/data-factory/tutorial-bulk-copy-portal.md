---
title: Copiare dati in blocco con Azure Data Factory | Microsoft Docs
description: "Informazioni su come usare Azure Data Factory e l'attività di copia per copiare dati in blocco da un archivio dati di origine a un archivio dati di destinazione."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 5f0703a3cb2ce912bfc042d0717ad8d921ec43e3
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiare più tabelle in blocco con Azure Data Factory
Questa esercitazione illustra la **copia di alcune tabelle dal database SQL di Azure ad Azure SQL Data Warehouse**. È possibile applicare lo stesso modello anche in altri scenari di copia, ad esempio per la copia di tabelle da SQL Server/Oracle in database SQL di Azure/SQL Data Warehouse/archivio BLOB di Azure o la copia di percorsi diversi dall'archivio BLOB alle tabelle del database SQL di Azure.

> [!NOTE]
> - Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).
> - Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

A livello generale, questa esercitazione prevede la procedura seguente:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare i servizi collegati database SQL di Azure, Azure SQL Data Warehouse e Archiviazione di Azure.
> * Creare i set di dati per database SQL di Azure e Azure SQL Data Warehouse.
> * Creare una pipeline per cercare le tabelle da copiare e un'altra pipeline per eseguire l'operazione di copia effettiva. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Questa esercitazione usa il portale di Azure. Per informazioni sull'uso di altri strumenti/SDK per creare una data factory, vedere le [Guide introduttive](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
In questo scenario il database SQL di Azure include alcune tabelle da copiare in SQL Data Warehouse. Ecco la sequenza logica di passaggi nel flusso di lavoro che si verifica nelle pipeline:

![Flusso di lavoro](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* La prima pipeline cerca l'elenco di tabelle da copiare negli archivi dati sink.  In alternativa è possibile mantenere una tabella di metadati che elenca tutte le tabelle da copiare nell'archivio dati sink. La pipeline attiva quindi un'altra pipeline, che esegue l'iterazione di ogni tabella nel database ed esegue l'operazione di copia dei dati.
* La seconda pipeline esegue la copia effettiva. Accetta l'elenco di tabelle come parametro. Per ogni tabella nell'elenco è necessario copiare la tabella specifica del database SQL di Azure nella tabella corrispondente in SQL Data Warehouse usando una [copia di staging tramite l'archivio BLOB e PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) per prestazioni ottimali. In questo esempio la prima pipeline passa l'elenco di tabelle come valore per il parametro. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* **Account di archiviazione di Azure**. L'account di archiviazione di Azure viene usato come archivio BLOB di staging nell'operazione di copia in blocco. 
* **Database SQL di Azure**. Questo database contiene i dati di origine. 
* **Azure SQL Data Warehouse**. Questo data warehouse include i dati copiati dal database SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Preparare il database SQL e SQL Data Warehouse

**Preparare il database SQL di Azure di origine**:

Creare un database SQL di Azure con i dati dell'esempio Adventure Works LT, seguendo le istruzioni disponibili nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md). Questa esercitazione copia tutte le tabelle da questo database di esempio in un'istanza di SQL Data Warehouse.

**Preparare l'istanza sink di Azure SQL Data Warehouse**:

1. Se non si ha un'istanza di Azure SQL Data Warehouse, vedere la procedura per la creazione di un'istanza nell'articolo [Creare un SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Creare gli schemi di tabella corrispondenti in SQL Data Warehouse. È possibile usare l'[Utilità per la migrazione](https://www.microsoft.com/download/details.aspx?id=49100) per **eseguire la migrazione dello schema** dal database SQL di Azure ad Azure SQL Data Warehouse. Azure Data Factory viene usato in un passaggio successivo per la migrazione/copia dei dati.

## <a name="azure-services-to-access-sql-server"></a>Accesso dei servizi di Azure a SQL Server

Per il database SQL e per SQL Data Warehouse è necessario consentire ai servizi di Azure di accedere a SQL server. Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia **ON** per il server Azure SQL. Questa impostazione consente al servizio Data Factory di leggere dati dal database SQL di Azure e di scrivere dati in Azure SQL Data Warehouse. Per verificare e attivare l'impostazione, seguire questa procedura:

1. Fare clic sull'hub **Altri servizi** a sinistra e selezionare **Server SQL**.
2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**.
3. Nella pagina **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso a Servizi Azure**.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialBulkCopyDF** per **nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialBulkCopyDF. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Data Factory V2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
    ![Home page di Data factory](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Data Factory in una scheda separata.
11. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra, come mostrato nell'immagine seguente:  

    ![Pagina delle attività iniziali](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati per collegare gli archivi dati e le risorse di calcolo a una data factory. Un servizio collegato include le informazioni di connessione usate dal servizio Data Factory per connettersi all'archivio dati in fase di esecuzione. 

In questa esercitazione gli archivi dati del database SQL di Azure, di Azure SQL Data Warehouse e dell'Archiviazione BLOB di Azure vengono collegati alla data factory. Il database SQL di Azure è l'archivio dati di origine. Azure SQL Data Warehouse è l'archivio dati del sink/di destinazione. L'Archiviazione BLOB di Azure consente di eseguire lo staging dei dati prima del caricamento dei dati in SQL Data Warehouse tramite PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Creare il servizio collegato database SQL di Azure di origine
In questo passaggio viene creato un servizio collegato per collegare il database SQL di Azure alla data factory. 

1. Fare clic su **Connessioni** nella parte inferiore della finestra e fare clic su **+ Nuovo** sulla barra degli strumenti. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Database SQL di Azure** e fare clic su **Continua**. 

    ![Selezionare il database SQL di Azure](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureSqlDatabaseLinkedService** per **Nome**. 
    2. Selezionare il server di Azure SQL per **Nome server**.
    3. Selezionare il database SQL di Azure per **Nome database**. 
    4. Immettere il **nome dell'utente** per connettersi al database SQL di Azure. 
    5. Immettere la **password** per l'utente. 
    6. Per eseguire il test della connessione al database SQL di Azure con le informazioni specificate, fare clic su **Connessione di test**.
    7. Fare clic su **Save**.

        ![Impostazioni del database SQL di Azure](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Creare il servizio collegato sink Azure SQL Data Warehouse

1. Nella scheda **Connessioni** fare di nuovo clic su **+ Nuovo** sulla barra degli strumenti. 
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Azure SQL Data Warehouse** e fare clic su **Continua**. 
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureSqlDWLinkedService** per **Nome**. 
    2. Selezionare il server di Azure SQL per **Nome server**.
    3. Selezionare il database SQL di Azure per **Nome database**. 
    4. Immettere il **nome dell'utente** per connettersi al database SQL di Azure. 
    5. Immettere la **password** per l'utente. 
    6. Per eseguire il test della connessione al database SQL di Azure con le informazioni specificate, fare clic su **Connessione di test**.
    7. Fare clic su **Save**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Creare il servizio collegato di staging Archiviazione di Azure
In questa esercitazione l'archivio BLOB di Azure viene usato come area di staging intermedia per abilitare PolyBase per ottimizzare le prestazioni della copia.

1. Nella scheda **Connessioni** fare di nuovo clic su **+ Nuovo** sulla barra degli strumenti. 
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**. 
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureStorageLinkedService** per **Nome**. 
    2. Selezionare l'**Account di archiviazione di Azure** per **Nome account di archiviazione**.
    4. Fare clic su **Save**.


## <a name="create-datasets"></a>Creare set di dati
In questa esercitazione vengono creati i set di dati di origine e sink, che specificano la posizione in cui vengono archiviati i dati. 

Il set di dati di input AzureSqlDatabaseDataset fa riferimento ad AzureSqlDatabaseLinkedService. Il servizio collegato specifica la stringa di connessione per la connessione al database. Il set di dati specifica il nome del database e la tabella che contiene i dati di origine. 

Il set di dati di output AzureSqlDWDataset fa riferimento ad AzureSqlDWLinkedService. Il servizio collegato specifica la stringa di connessione per la connessione al data warehouse. Il set di dati specifica il database e la tabella in cui vengono copiati i dati. 

In questa esercitazione le tabelle SQL di origine e di destinazione non sono hardcoded nelle definizioni del set di dati. L'attività ForEach passa invece il nome della tabella in fase di esecuzione all'attività di copia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Creare un set di dati per il database SQL di origine

1. Fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Set di dati**. 

    ![Menu Nuovo set di dati](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Fine**. Dovrebbe essere visualizzata una nuova scheda intitolata **AzureSqlTable1**. 
    
    ![Selezionare il database SQL di Azure](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. Nella finestra delle proprietà nella parte inferiore della schermata immettere **AzureSqlDatabaseDataset** per **Nome**.

    ![Nome del set di dati di origine](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. Passare alla scheda **Connessione** e seguire questa procedura: 

    1. Selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.
    2. Selezionare una tabella per **Tabella**. Questa è una tabella fittizia. Specificare una query sul set di dati di origine quando si crea una pipeline. La query viene usata per estrarre dati dal database SQL di Azure. In alternativa è possibile selezionare la casella di controllo **Modifica** e immettere **dummyName** come nome della tabella. 

    ![Pagina di connessione al set di dati di origine](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Creare un set di dati per l'istanza sink di SQL Data Warehouse

1. Fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Set di dati**. 
2. Nella finestra **Nuovo set di dati** selezionare **Azure SQL Data Warehouse** e fare clic su **Fine**. Dovrebbe essere visualizzata una nuova scheda intitolata **AzureSqlDWTable1**. 
3. Nella finestra delle proprietà nella parte inferiore della schermata immettere **AzureSqlDWDataset** per **Nome**.
4. Passare alla scheda **Connessione** e selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.
5. Passare alla scheda **Parametri** e fare clic su **+ Nuovo**

    ![Pagina di connessione al set di dati di origine](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. Immettere **DWTableName** per il nome del parametro. Se si copia/incolla questo nome dalla pagina, assicurarsi che non siano presenti **spazi finali** alla fine di **DWTableName**. 
7. Nella sezione **Parameterized properties** (Proprietà con parametri) immettere `@{dataset().DWTableName}` per la proprietà **tableName**. La proprietà **tableName** del set di dati è impostata sul valore passato come argomento per il parametro **DWTableName**. L'attività ForEach esegue l'iterazione di un elenco di tabelle e le passa una alla volta all'attività di copia. 
   
    ![Nome parametro](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>Creare le pipeline
In questa esercitazione vengono create due pipeline: **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

La pipeline **GetTableListAndTriggerCopyData** esegue due passaggi:

* Ricerca della tabella di sistema del database SQL di Azure per ottenere l'elenco di tabelle da copiare.
* Attivazione della pipeline **IterateAndCopySQLTables** per l'esecuzione della copia effettiva dei dati.

**GetTableListAndTriggerCopyData** accetta un elenco di tabelle come parametro. Per ogni tabella della lista, copia i dati dalla tabella del database SQL di Azure ad Azure SQL Data Warehouse usando una copia di staging e PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Creare la pipeline IterateAndCopySQLTables

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Pipeline**.

    ![Menu per nuova pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. Nella finestra Proprietà cambiare il nome della pipeline in **IterateAndCopySQLTables**. 

    ![Nome della pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. Passare alla scheda **Parametri** ed eseguire le azioni seguenti: 

    1. Fare clic su **+ Nuovo**. 
    2. Immettere **tableList** per il **nome** del parametro.
    3. Selezionare **Oggetto** per **Tipo**.

        ![Parametro della pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. Nella casella degli strumenti **Attività** espandere **Iteration & Conditions** (Iterazione e condizioni) e trascinare l'attività **ForEach** sull'area di progettazione della pipeline. È anche possibile eseguire una ricerca di attività nella casella degli strumenti **Attività**. Nella finestra **Proprietà** nella parte inferiore della schermata immettere **IterateSQLTables** per **Nome**. 

    ![Nome dell'attività ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. Passare alla scheda **Impostazioni** e immettere `@pipeline().parameters.tableList` per **Elementi**.

    ![Impostazioni dell'attività ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. Per aggiungere un'attività figlio all'attività **ForEach**, **fare doppio clic** sull'attività ForEach oppure fare clic su **Modifica (icona a forma di matita)**. I collegamenti all'azione vengono visualizzati solo quando si seleziona l'azione. 

    ![Nome dell'attività ForEach](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. Nella casella degli strumenti **Attività** espandere **DataFlow** e trascinare l'attività **Copia** nell'area di progettazione della pipeline, quindi cambiare il nome nella finestra Proprietà in **CopyData**. Si noti il menu di navigazione nella parte superiore della schermata. IterateAndCopySQLTable è il nome della pipeline e IterateSQLTables è il nome dell'attività ForEach. La finestra di progettazione rientra nell'ambito dell'attività. Per tornare all'editor di pipeline dall'editor di ForEach, fare clic sul collegamento nel menu di navigazione. 

    ![Copiare in ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. Passare alla scheda **Origine** e seguire questa procedura:

    1. Selezionare **AzureSqlDatabaseDataset** per **Source Dataset** (Set di dati di origine). 
    2. Selezionare l'opzione **Query** per **Query utente**. 
    3. Immettere la query SQL seguente per **Query**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Copiare le impostazioni di origine](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. Passare alla scheda **Sink** e seguire questa procedura: 

    1. Selezionare **AzureSqlDWDataset** per **Sink Dataset** (Set di dati sink).
    2. Espandere **Polybase Settings** (Impostazioni PolyBase) e selezionare **Allow polybase** (Consenti PolyBase). 
    3. Deselezionare l'opzione **Tipo di uso predefinito**. 
    4. Immettere lo script SQL seguente per **Script di pulizia**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Copiare le impostazioni del sink](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. Passare alla scheda **Parametri**, scorrere verso il basso, se necessario, per visualizzare la sezione **Sink Dataset** (Set di dati sink) con il parametro **DWTableName**. Impostare il valore di questo parametro su `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`.

    ![Copiare i parametri del sink](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. Passare alla scheda **Impostazioni** e seguire questa procedura: 

    1. Selezionare **True** per **Enable Staging** (Abilita staging).
    2. Selezionare **AzureStorageLinkedService** per **Staging Account Linked Service** (Servizio collegato dell'account di staging).

        ![Abilitare lo staging](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. Per convalidare le impostazioni della pipeline, fare clic su **Convalida**. Verificare che non sia presente alcun errore di convalida. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>**.

    ![Report di convalida della pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Creare la pipeline GetTableListAndTriggerCopyData

Questa pipeline esegue due passaggi:

* Ricerca della tabella di sistema del database SQL di Azure per ottenere l'elenco di tabelle da copiare.
* Attivazione della pipeline "IterateAndCopySQLTables" per l'esecuzione della copia effettiva dei dati.

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Pipeline**.

    ![Menu per nuova pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. Nella finestra Proprietà cambiare il nome della pipeline in **GetTableListAndTriggerCopyData**. 

    ![Nome della pipeline](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. Nella casella degli strumenti **Attività** espandere **Generale**, trascinare l'attività **Cerca** nell'area di progettazione della pipeline e seguire questa procedura:

    1. Immettere **LookupTableList** per **Nome**. 
    2. Immettere **Retrieve the table list from Azure SQL database** (Recuperare l'elenco di tabelle dal database SQL di Azure) per **Descrizione**.

        ![Attività Cerca - Pagina Generale](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. Passare alla pagina **Impostazioni** e seguire questa procedura:

    1. Selezionare **AzureSqlDatabaseDataset** per **Source Dataset** (Set di dati di origine). 
    2. Selezionare **Query** per **Use Query** (Usa query). 
    3. Immettere la query SQL seguente per **Query**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. Deselezionare la casella di controllo per il campo **First row only** (Solo prima riga).

        ![Attività Cerca - Pagina Impostazioni](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. Trascinare l'attività **Execute Pipeline** (Esegui pipeline) dalla casella degli strumenti Attività nell'area di progettazione della pipeline e impostare il nome su **TriggerCopy**.

    ![Attività Execute Pipeline (Esegui pipeline) - Pagina Generale](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. Passare alla pagina **Impostazioni** e seguire questa procedura: 

    1. Selezionare **IterateAndCopySQLTables** per **Invoked pipeline** (Pipeline richiamata). 
    2. Espandere la sezione **Avanzata**. 
    3. Fare clic su **+ Nuovo** nella sezione **Parametri**. 
    4. Immettere **tableList** per il **nome** del parametro.
    5. Immettere `@activity('LookupTableList').output.value` per il **valore** del parametro. L'elenco dei risultati dall'attività Cerca viene configurato come input per la seconda pipeline. L'elenco dei risultati contiene l'elenco di tabelle i cui dati devono essere copiati nella destinazione. 

        ![Attività (Esegui pipeline) - Pagina Impostazioni](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. **Connettere** l'attività **Cerca** all'attività **Execute Pipeline** (Esegui pipeline) trascinando la **casella verde** collegata all'attività Cerca a sinistra dell'attività Execute Pipeline (Esegui pipeline).

    ![Connettere le attività Cerca ed Execute Pipeline (Esegui pipeline)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. Per convalidare la pipeline, fare clic su **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>**.

    ![Seconda pipeline - Report di convalida](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. Per pubblicare le entità (set di dati, pipeline e così via) nel servizio Data Factory, fare clic su **Pubblica tutti**. Attendere fino al completamento della pubblicazione. 

    ![Pulsante Publish](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline

1. Confermare che la scheda **GetTableListAndTriggerCopyData** sia attiva. 
2. Fare clic su **Trigger** e quindi su **Trigger Now** (Attiva adesso). 

    ![Trigger now (Attiva adesso)](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Fare clic su **Aggiorna** fino a visualizzare le esecuzioni per entrambe le pipeline nella soluzione. Continuare ad aggiornare l'elenco fino a visualizzare lo stato **Operazione completata**. 

    ![Esecuzioni di pipeline](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. Per visualizzare le esecuzioni di attività associate alla pipeline GetTableListAndTriggerCopyData, fare clic sul primo collegamento nei collegamenti alle azioni per la pipeline specifica. Dovrebbero essere visualizzate due esecuzioni di attività per questa esecuzione di pipeline. 

    ![Esecuzioni attività](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. Per visualizzare l'output dell'attività **Cerca** fare clic sul collegamento nella colonna **Output** per tale attività. È possibile ingrandire e ripristinare la finestra **Output**. Dopo la verifica, fare clic su **X** per chiudere la finestra **Output**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
4. Per tornare alla visualizzazione **Pipeline Runs**, (Esecuzioni di pipeline) fare clic sul collegamento **Pipeline** in alto. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività), ovvero il primo collegamento della colonna **Azioni**, per la pipeline **IterateAndCopySQLTables**. Dovrebbe essere visualizzato l'output mostrato nell'immagine seguente. Si noti che è presente un'attività **Copia** per ogni tabella nell'output dell'attività **Cerca**. 

    ![Esecuzioni attività](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. Confermare che i dati sono stati copiati nell'istanza di SQL Data Warehouse di destinazione usata in questa esercitazione. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare i servizi collegati database SQL di Azure, Azure SQL Data Warehouse e Archiviazione di Azure.
> * Creare i set di dati per database SQL di Azure e Azure SQL Data Warehouse.
> * Creare una pipeline per cercare le tabelle da copiare e un'altra pipeline per eseguire l'operazione di copia effettiva. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per ottenere informazioni sulla copia incrementale di dati da un'origine a una destinazione:
> [!div class="nextstepaction"]
>[Copiare i dati in modo incrementale](tutorial-incremental-copy-portal.md)