---
title: Copiare dati in blocco con Azure Data Factory | Microsoft Docs
description: Informazioni su come usare Azure Data Factory e l'attività di copia per copiare dati in blocco da un archivio dati di origine a un archivio dati di destinazione.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: d7f97eec4e0dc6e88d89e845e086b9e5242caa7b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616539"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiare più tabelle in blocco con Azure Data Factory
Questa esercitazione illustra la **copia di alcune tabelle dal database SQL di Azure ad Azure SQL Data Warehouse**. È possibile applicare lo stesso modello anche in altri scenari di copia, ad esempio per la copia di tabelle da SQL Server/Oracle in database SQL di Azure/SQL Data Warehouse/archivio BLOB di Azure o la copia di percorsi diversi dall'archivio BLOB alle tabelle del database SQL di Azure.

> [!NOTE]
> - Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

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

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* **Account di archiviazione di Azure**. L'account di archiviazione di Azure viene usato come archivio BLOB di staging nell'operazione di copia in blocco. 
* **Database SQL di Azure**. Questo database contiene i dati di origine. 
* **Azure SQL Data Warehouse**. Questo data warehouse include i dati copiati dal database SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Preparare il database SQL e SQL Data Warehouse

**Preparare il database SQL di Azure di origine**:

Creare un database SQL di Azure con i dati dell'esempio Adventure Works LT, seguendo le istruzioni disponibili nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md). Questa esercitazione copia tutte le tabelle da questo database di esempio in un'istanza di SQL Data Warehouse.

**Preparare l'istanza sink di Azure SQL Data Warehouse**:

1. Se non si ha un'istanza di Azure SQL Data Warehouse, vedere la procedura per la creazione di un'istanza nell'articolo [Creare un SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

1. Creare gli schemi di tabella corrispondenti in SQL Data Warehouse. È possibile usare l'[Utilità per la migrazione](https://www.microsoft.com/download/details.aspx?id=49100) per **eseguire la migrazione dello schema** dal database SQL di Azure ad Azure SQL Data Warehouse. Azure Data Factory viene usato in un passaggio successivo per la migrazione/copia dei dati.

## <a name="azure-services-to-access-sql-server"></a>Accesso dei servizi di Azure a SQL Server

Per il database SQL e per SQL Data Warehouse è necessario consentire ai servizi di Azure di accedere a SQL server. Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia **ON** per il server Azure SQL. Questa impostazione consente al servizio Data Factory di leggere dati dal database SQL di Azure e di scrivere dati in Azure SQL Data Warehouse. 

Per verificare e attivare l'impostazione, passare a Server di Azure SQL > Sicurezza > Firewall e reti virtuali, quindi impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **ON**.

## <a name="create-a-data-factory"></a>Creare una data factory
1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**: ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **ADFTutorialBulkCopyDF** per **nome**. 
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialBulkCopyDF. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
1. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
     Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
1. Selezionare **V2** per **version**.
1. Selezionare la **località** per la data factory. Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
1. Fare clic su **Create**(Crea).
1. Al termine della creazione verrà visualizzata la pagina **Data factory**.
   
1. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Data Factory in una scheda separata.
1. Nella pagina **Attività iniziali** passare alla scheda **Autore** nel pannello sinistro, come mostrato nell'immagine seguente:  

     ![Pagina delle attività iniziali](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati per collegare gli archivi dati e le risorse di calcolo a una data factory. Un servizio collegato include le informazioni di connessione usate dal servizio Data Factory per connettersi all'archivio dati in fase di esecuzione. 

In questa esercitazione gli archivi dati del database SQL di Azure, di Azure SQL Data Warehouse e dell'Archiviazione BLOB di Azure vengono collegati alla data factory. Il database SQL di Azure è l'archivio dati di origine. Azure SQL Data Warehouse è l'archivio dati del sink/di destinazione. L'Archiviazione BLOB di Azure consente di eseguire lo staging dei dati prima del caricamento dei dati in SQL Data Warehouse tramite PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Creare il servizio collegato database SQL di Azure di origine
In questo passaggio viene creato un servizio collegato per collegare il database SQL di Azure alla data factory. 

1. Fare clic su **Connessioni** nella parte inferiore della finestra e fare clic su **+ Nuovo** sulla barra degli strumenti. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Database SQL di Azure** e fare clic su **Continua**. 
1. Nella finestra **New Linked Service (Azure SQL Database)** (Nuovo servizio collegato - Database SQL di Azure), procedere come segue: 

    a. Immettere **AzureSqlDatabaseLinkedService** per **Nome**.
    
    b. Selezionare il server di Azure SQL per **Nome server**.
    
    c. Selezionare il database SQL di Azure per **Nome database**. 
    
    d. Immettere il **nome dell'utente** per connettersi al database SQL di Azure. 
    
    e. Immettere la **password** per l'utente. 

    f. Per eseguire il test della connessione al database SQL di Azure con le informazioni specificate, fare clic su **Connessione di test**.
  
    g. Fare clic su **Continue**.


### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Creare il servizio collegato sink Azure SQL Data Warehouse

1. Nella scheda **Connessioni** fare di nuovo clic su **+ Nuovo** sulla barra degli strumenti. 
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Azure SQL Data Warehouse** e fare clic su **Continua**. 
1. Nella finestra **New Linked Service (Azure SQL Data Warehouse)** (Nuovo servizio collegato - Azure SQL Data Warehouse), procedere come segue: 
   
    a. Immettere **AzureSqlDWLinkedService** per **Nome**.
     
    b. Selezionare il server di Azure SQL per **Nome server**.
     
    c. Selezionare il database SQL di Azure per **Nome database**. 
     
    d. Immettere il **nome dell'utente** per connettersi al database SQL di Azure. 
     
    e. Immettere la **password** per l'utente. 
     
    f. Per eseguire il test della connessione al database SQL di Azure con le informazioni specificate, fare clic su **Connessione di test**.
     
    g. Fare clic su **Continue**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Creare il servizio collegato di staging Archiviazione di Azure
In questa esercitazione l'archivio BLOB di Azure viene usato come area di staging intermedia per abilitare PolyBase per ottimizzare le prestazioni della copia.

1. Nella scheda **Connessioni** fare di nuovo clic su **+ Nuovo** sulla barra degli strumenti. 
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**. 
1. Nella finestra **New Linked Service (Azure Blog Storage)** (Nuovo servizio collegato - Archiviazione BLOB di Azure), procedere come segue: 

    a. Immettere **AzureStorageLinkedService** per **Nome**.                                                     
    
    b. Selezionare l'**Account di archiviazione di Azure** per **Nome account di archiviazione**.
    
    c. Fare clic su **Continue**.


## <a name="create-datasets"></a>Creare set di dati
In questa esercitazione vengono creati i set di dati di origine e sink, che specificano la posizione in cui vengono archiviati i dati. 

Il set di dati di input **AzureSqlDatabaseDataset** fa riferimento ad **AzureSqlDatabaseLinkedService**. Il servizio collegato specifica la stringa di connessione per la connessione al database. Il set di dati specifica il nome del database e la tabella che contiene i dati di origine. 

Il set di dati di output **AzureSqlDWDataset** fa riferimento ad **AzureSqlDWLinkedService**. Il servizio collegato specifica la stringa di connessione per la connessione al data warehouse. Il set di dati specifica il database e la tabella in cui vengono copiati i dati. 

In questa esercitazione le tabelle SQL di origine e di destinazione non sono hardcoded nelle definizioni del set di dati. L'attività ForEach passa invece il nome della tabella in fase di esecuzione all'attività di copia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Creare un set di dati per il database SQL di origine

1. Fare clic su **+ (segno più)** nel riquadro sinistro e quindi su **Set di dati**. 

    ![Menu Nuovo set di dati](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e quindi fare clic su **Fine**. 
    
1. Nella finestra **Imposta proprietà**, in **Nome**, immettere **AzureSqlDatabaseDataset**. In **Servizio collegato** selezionare **AzureSqlDatabaseLinkedService**. Fare quindi clic su **Continue** (Continua).
1. Passare alla scheda **Connessione** e selezionare una tabella per **Tabella**. Questa è una tabella fittizia. Specificare una query sul set di dati di origine quando si crea una pipeline. La query viene usata per estrarre dati dal database SQL di Azure. In alternativa è possibile selezionare la casella di controllo **Modifica** e immettere **dummyName** come nome della tabella. 
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Creare un set di dati per l'istanza sink di SQL Data Warehouse

1. Fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Set di dati**. 
1. Nella finestra **Nuovo set di dati** selezionare **Azure SQL Data Warehouse** e quindi fare clic su **Fine**.
1. Nella finestra **Imposta proprietà**, in **Nome**, immettere **AzureSqlDWDataset**. In **Servizio collegato** selezionare **AzureSqlDatabaseLinkedService**. Fare quindi clic su **Continue** (Continua).
1. Passare alla scheda **Parametri**, fare clic su **+ Nuovo** e immettere **DWTableName** come nome del parametro. Se si copia/incolla questo nome dalla pagina, assicurarsi che non siano presenti **spazi finali** alla fine di **DWTableName**.
1. Passare alla scheda **Connessione**. 

    a. Per **Tabella**, selezionare l'opzione **Modifica**, fare clic nella casella di input del nome della tabella, quindi fare clic sul collegamento **Aggiungi contenuto dinamico** sotto. 

    b. Nella pagina **Aggiungi contenuto dinamico** fare clic su **DWTAbleName** in **Parametri** per popolare automaticamente la casella di testo dell'espressione `@dataset().DWTableName` nella parte superiore, quindi fare clic su **Fine**. La proprietà **tableName** del set di dati è impostata sul valore passato come argomento per il parametro **DWTableName**. L'attività ForEach esegue l'iterazione di un elenco di tabelle e le passa una alla volta all'attività di copia. 

    ![Generatore di parametri di set di dati](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Creare le pipeline
In questa esercitazione vengono create due pipeline: **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

La pipeline **GetTableListAndTriggerCopyData** esegue due azioni:

* Ricerca della tabella di sistema del database SQL di Azure per ottenere l'elenco di tabelle da copiare.
* Attivazione della pipeline **IterateAndCopySQLTables** per l'esecuzione della copia effettiva dei dati.

La pipeline **IterateAndCopySQLTables** accetta un elenco di tabelle come parametro. Per ogni tabella della lista, copia i dati dalla tabella del database SQL di Azure ad Azure SQL Data Warehouse usando una copia di staging e PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Creare la pipeline IterateAndCopySQLTables

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Pipeline**.

    ![Menu per nuova pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Nella scheda **Generale** specificare **IterateAndCopySQLTables** come nome. 

1. Passare alla scheda **Parametri** ed eseguire le azioni seguenti: 

    a. Fare clic su **+ Nuovo**. 
    
    b. Immettere **tableList** per il **nome** del parametro.
    
    c. Selezionare **Matrice** per **Tipo**.

1. Nella casella degli strumenti **Attività** espandere **Iteration & Conditions** (Iterazione e condizioni) e trascinare l'attività **ForEach** sull'area di progettazione della pipeline. È anche possibile eseguire una ricerca di attività nella casella degli strumenti **Attività**. 

    a. Nella scheda **Generale** nella parte inferiore della schermata immettere **IterateSQLTables** per **Nome**. 

    b. Passare alla scheda **Impostazioni**, fare clic sulla casella di input **Elementi**, quindi fare clic sul collegamento **Aggiungi contenuto dinamico**. 

    c. Nella pagina **Aggiungi contenuto dinamico** comprimere le sezioni **Variabili di sistema** e **Funzioni**, fare clic su **tableList** in **Parametri** per popolare automaticamente la casella di testo dell'espressione come `@pipeline().parameter.tableList` nella parte superiore. Fare clic su **Fine**. 

    ![Generatore di parametri foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Passare alla scheda **Attività**, fare clic su **Aggiungi attività** per aggiungere un'attività figlio all'attività**ForEach**.

1. Nella casella degli strumenti **Attività** espandere **Move & Transfer** (Sposta e trasferisci) e trascinare l'attività **Copia dati** nell'area di progettazione della pipeline. Si noti il menu di navigazione nella parte superiore della schermata. **IterateAndCopySQLTable** è il nome della pipeline e **IterateSQLTables** è il nome dell'attività ForEach. La finestra di progettazione rientra nell'ambito dell'attività. Per tornare all'editor di pipeline dall'editor di ForEach, fare clic sul collegamento nel menu di navigazione. 

    ![Copiare in ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Passare alla scheda **Origine** e seguire questa procedura:

    1. Selezionare **AzureSqlDatabaseDataset** per **Source Dataset** (Set di dati di origine). 
    1. Selezionare l'opzione **Query** per **Query utente**. 
    1. Fare clic sulla casella di input **Query** -> selezionare **Aggiungi contenuto dinamico** -> immettere l'espressione seguente per **Query** -> selezionare **Fine**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Passare alla scheda **Sink** e seguire questa procedura: 

    1. Selezionare **AzureSqlDWDataset** per **Sink Dataset** (Set di dati sink).
    1. Fare clic sulla casella di input per il valore del parametro DWTableName -> selezionare **Aggiungi contenuto dinamico**, immettere l'espressione `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` come script -> selezionare **Fine**.
    1. Selezionare la casella di controllo **Allow PolyBase** (Consenti PolyBase). 
    1. Deselezionare l'opzione **Tipo di uso predefinito**. 
    1. Fare clic sulla casella di input **Pre-copy Script** (Script di pre-copia) -> selezionare **Aggiungi contenuto dinamico** -> immettere l'espressione seguente come script -> selezionare **Fine**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Copiare le impostazioni del sink](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Passare alla scheda **Impostazioni** e seguire questa procedura: 

    1. Selezionare la casella di controllo **Enable staging** (Abilita staging).
    1. Selezionare **AzureStorageLinkedService** per **Staging Account Linked Service** (Servizio collegato dell'account di staging).

1. Per convalidare le impostazioni della pipeline, fare clic su **Convalida** sulla barra degli strumenti superiore. Verificare che non sia presente alcun errore di convalida. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Creare la pipeline GetTableListAndTriggerCopyData

Questa pipeline esegue due azioni:

* Ricerca della tabella di sistema del database SQL di Azure per ottenere l'elenco di tabelle da copiare.
* Attivazione della pipeline "IterateAndCopySQLTables" per l'esecuzione della copia effettiva dei dati.

1. Nel riquadro a sinistra fare clic su **+ (segno più)** e quindi su **Pipeline**.
1. Nella scheda **Generale** cambiare il nome della pipeline in **GetTableListAndTriggerCopyData**. 

1. Nella casella degli strumenti **Attività** espandere **Generale**, trascinare l'attività **Cerca** nell'area di progettazione della pipeline e seguire questa procedura:

    1. Immettere **LookupTableList** per **Nome**. 
    1. Immettere **Retrieve the table list from Azure SQL database** (Recuperare l'elenco di tabelle dal database SQL di Azure) per **Descrizione**.

1. Passare alla scheda **Impostazioni** e seguire questa procedura:

    1. Selezionare **AzureSqlDatabaseDataset** per **Source Dataset** (Set di dati di origine). 
    1. Selezionare **Query** per **Use Query** (Usa query). 
    1. Immettere la query SQL seguente per **Query**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Deselezionare la casella di controllo per il campo **First row only** (Solo prima riga).

        ![Attività Cerca - Pagina Impostazioni](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Trascinare l'attività **Execute Pipeline** (Esegui pipeline) dalla casella degli strumenti Attività nell'area di progettazione della pipeline e impostare il nome su **TriggerCopy**.

1. Passare alla scheda **Impostazioni** e seguire questa procedura: 

    1. Selezionare **IterateAndCopySQLTables** per **Invoked pipeline** (Pipeline richiamata). 
    1. Espandere la sezione **Avanzata**. 
    1. Fare clic su **+ Nuovo** nella sezione **Parametri**. 
    1. Immettere **tableList** per il **nome** del parametro.
    1. Fare clic sulla casella di input del VALORE -> selezionare **Aggiungi contenuto dinamico** -> immettere `@activity('LookupTableList').output.value` come valore per il nome della tabella -> selezionare **Fine**. L'elenco dei risultati dall'attività Cerca viene configurato come input per la seconda pipeline. L'elenco dei risultati contiene l'elenco di tabelle i cui dati devono essere copiati nella destinazione. 

        ![Attività (Esegui pipeline) - Pagina Impostazioni](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. Connettere **** l'attività **Cerca** all'attività **Execute Pipeline** (Esegui pipeline) trascinando la **casella verde** collegata all'attività Cerca a sinistra dell'attività Execute Pipeline.

    ![Connettere le attività Cerca ed Execute Pipeline (Esegui pipeline)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. Per convalidare la pipeline, fare clic su **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>** .

1. Per pubblicare le entità (set di dati, pipeline e così via) nel servizio Data Factory, fare clic su **Pubblica tutti** nella parte superiore della finestra. Attendere fino al completamento della pubblicazione. 

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline

Passare alla pipeline **GetTableListAndTriggerCopyData**, fare clic su **Add Trigger** (Aggiungi trigger) e quindi su **Trigger Now** (Attiva adesso). 

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Fare clic su **Aggiorna** fino a visualizzare le esecuzioni per entrambe le pipeline nella soluzione. Continuare ad aggiornare l'elenco fino a visualizzare lo stato **Operazione completata**. 

1. Per visualizzare le esecuzioni di attività associate alla pipeline **GetTableListAndTriggerCopyData**, fare clic sul primo collegamento Azioni per la pipeline. Dovrebbero essere visualizzate due esecuzioni di attività per questa esecuzione di pipeline. 

1. Per visualizzare l'output dell'attività **Cerca** fare clic sul collegamento nella colonna **Output** per tale attività. È possibile ingrandire e ripristinare la finestra **Output**. Dopo la verifica, fare clic su **X** per chiudere la finestra **Output**.

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
1. Per tornare alla visualizzazione **Pipeline Runs**, (Esecuzioni di pipeline) fare clic sul collegamento **Pipeline** in alto. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività), ovvero il primo collegamento della colonna **Azioni**, per la pipeline **IterateAndCopySQLTables**. Si noti che è presente un'esecuzione attività **Copia** per ogni tabella nell'output dell'attività **Cerca**. 

1. Confermare che i dati sono stati copiati nell'istanza di SQL Data Warehouse di destinazione usata in questa esercitazione. 

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
