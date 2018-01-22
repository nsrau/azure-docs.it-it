---
title: Usare il portale di Azure per creare una pipeline di Data Factory | Microsoft Docs
description: "Questa esercitazione offre istruzioni dettagliate per l'uso del portale di Azure per creare una data factory con una pipeline. La pipeline usa l'attività di copia per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure. "
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
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 7486e7c6816538fc120fd0b0a8bea0b006fb21f0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiare dati da un BLOB di Azure al database SQL di Azure con Azure Data Factory
In questa esercitazione viene creata una data factory con l'interfaccia utente di Azure Data Factory. La pipeline in questa data factory copia i dati dall'archivio BLOB di Azure al database SQL di Azure. Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati basato su file a un archivio dati relazionale. Per un elenco degli archivi dati supportati come origini e sink, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).
>
> - Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività di copia.
> * Eseguire test della pipeline
> * Attivare manualmente la pipeline
> * Attivare la pipeline in base a una pianificazione
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
* **Database SQL di Azure**. Usare il database come archivio dati **sink**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora il BLOB di Azure e il database SQL di Azure per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **emp.txt** sul disco.

    ```
    John,Doe
    Jane,Doe
    ```

2. Nell'archivio BLOB di Azure creare un contenitore denominato **adftutorial**. Creare una cartella denominata **input** in questo contenitore. Caricare quindi il file **emp.txt** nella cartella **input**. Usare il portale di Azure o strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per eseguire queste attività.

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

1. Usare lo script SQL seguente per creare la tabella **dbo.emp** nel database SQL di Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'opzione **Consenti l'accesso a Servizi di Azure** sia impostata su **SÌ** per il server SQL di Azure in modo che il servizio Data Factory possa scrivere dati nel server. Per verificare e attivare l'impostazione, seguire questa procedura:

    1. Fare clic sull'hub **Altri servizi** a sinistra e selezionare **Server SQL**.
    2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**.
    3. Nella pagina **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso a Servizi Azure**.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
In questo passaggio viene creata una data factory e viene avviata l'interfaccia utente di Azure Data Factory per creare una pipeline nella data factory. 

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Pagina Nuova data factory](./media/tutorial-copy-data-portal/name-not-available-error.png)
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

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'attività di copia nella data factory. L'attività di copia copia i dati dall'archivio BLOB di Azure al database SQL di Azure. Nell'[Esercitazione introduttiva](quickstart-create-data-factory-portal.md) è stata creata una pipeline con questa procedura:

1. Creazione del servizio collegato. 
2. Creazione dei set di dati di input e di output.
3. Infine, creazione di una pipeline.

In questa esercitazione si inizia creando la pipeline, quindi vengono creati i servizi collegati e i set di dati quando si rendono necessari per la configurazione della pipeline. 

1. Nella pagina delle attività iniziali fare clic sul riquadro **Create pipeline** (Crea pipeline). 

   ![Riquadro Create pipeline (Crea pipeline)](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. Nella finestra **Proprietà** della pipeline modificare il **nome** della pipeline in **CopyPipeline**.

    ![Nome della pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
4. Nella casella degli strumenti **Attività** espandere la categoria **Flusso di dati** e trascinare l'attività **Copia** dalla casella degli strumenti all'area di progettazione della pipeline. 

    ![Trascinare l'attività di copia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. Nella scheda **Generale** della finestra **Proprietà** specificare **CopyFromBlobToSql** come nome dell'attività.

    ![Nome dell'attività](./media/tutorial-copy-data-portal/activity-name.png)
6. Passare alla scheda **Origine**. Fare clic su **+ Nuovo** per creare un set di dati di origine. 

    ![Menu per il nuovo set di dati di origine](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e fare clic su **Fine**. I dati di origine si trovano in un archivio BLOB di Azure, quindi selezionare Archiviazione BLOB di Azure per il set di dati di origine. 

    ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. Nell'applicazione si apre una nuova **scheda** con il titolo **AzureBlob1**.

    ![Scheda AzureBlob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. Nella scheda **Generale** della finestra **Proprietà** in basso, specificare **SourceBlobDataset** per **Nome**.

    ![Nome del set di dati](./media/tutorial-copy-data-portal/dataset-name.png)
10. Passare alla scheda **Connessione** nella finestra delle proprietà.   

    ![Scheda Connessione](./media/tutorial-copy-data-portal/source-dataset-connection-tab.png)
11. Fare clic su **+ Nuovo** accanto alla casella di testo **Servizio collegato**. Un servizio collegato collega un archivio dati o un ambiente di calcolo alla data factory. In questo caso viene creato un servizio collegato di archiviazione di Azure per collegare l'account di archiviazione di Azure all'archivio dati. Il servizio collegato ha le informazioni di connessione usate dal servizio Data Factory per connettersi all'archivio BLOB in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) che contiene i dati di origine. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureStorageLinkedService** per il campo **Nome**. 
    2. Selezionare l'account di archiviazione di Azure per il campo **Nome account di archiviazione**.
    3. Fare clic su **Test connessione** per testare la connessione all'account di archiviazione di Azure.  
    4. Fare clic su **Salva** per salvare il servizio collegato.

        ![Nuovo servizio collegato di archiviazione di Azure](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. Fare clic su **Sfoglia** per il campo **Percorso file**.  

    ![Pulsante Sfoglia per il percorso del file](./media/tutorial-copy-data-portal/file-browse-button.png)
14. Passare alla cartella **adftutorial/input**, selezionare il file **emp.txt** e fare clic su **Fine**. In alternativa fare doppio clic su emp.txt. 

    ![Selezionare il file di input](./media/tutorial-copy-data-portal/select-input-file.png)
15. Verificare che **Formato file** sia impostato su **Text format** (Formato testo) e che il **delimitatore di colonna** sia impostato su **Virgola (`,`)**. Se il file di origine usa delimitatori di colonna e riga diversi, è possibile fare clic su **Detect Text Format** (Rileva formato testo) per il campo **Formato file**. Lo strumento Copia dati rileva automaticamente il formato del file e i delimitatori. È comunque possibile sostituire questi valori. È possibile visualizzare l'anteprima dei dati in questa pagina facendo clic su **Anteprima dati**.

    ![Rilevare il formato del testo](./media/tutorial-copy-data-portal/detect-text-format.png)
17. Passare alla scheda **Schema** nella finestra delle proprietà e fare clic su **Importa schema**. Si noti che l'applicazione ha rilevato due colonne nel file di origine. Lo schema viene importato per poter eseguire il mapping delle colonne dall'archivio dati di origine all'archivio dati sink. Se non è necessario eseguire il mapping delle colonne, questo passaggio può essere ignorato. Per questa esercitazione importare lo schema.

    ![Rilevare lo schema di origine](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. Passare ora alla **scheda con la pipeline** oppure fare clic sulla pipeline nella **visualizzazione albero** a sinistra.  

    ![Scheda con la pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. Verificare che sia selezionato **SourceBlobDataset** per il campo Source Dataset (Set di dati di origine) nella finestra delle proprietà. È possibile visualizzare l'anteprima dei dati in questa pagina facendo clic su **Anteprima dati**. 
    
    ![Set di dati di origine](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. Passare alla scheda **Sink** e fare clic su **Nuovo** per creare un set di dati sink. 

    ![Menu per il nuovo set di dati sink](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Fine**. In questa esercitazione i dati vengono copiati in un database SQL di Azure. 

    ![Selezionare il database SQL di Azure](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. Nella scheda **Generale** della finestra delle proprietà impostare il nome su **OutputSqlDataset**. 
    
    ![Nome del set di dati di output](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. Passare alla scheda **Connessione** e fare clic su **Nuovo** per **Servizio collegato**. È necessario associare un set di dati a un servizio collegato. Il servizio collegato ha la stringa di connessione usata dal servizio Data Factory per connettersi al database SQL di Azure in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) in cui vengono copiati i dati. 
    
    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureSqlDatabaseLinkedService** nel campo **Nome**. 
    2. Selezionare il server di Azure SQL nel campo **Nome server**.
    4. Selezionare il database SQL di Azure nel campo **Nome database**. 
    5. Immettere il nome dell'utente nel campo **Nome utente**. 
    6. Immettere la password dell'utente nel campo **Password**. 
    7. Fare clic su **Test connessione** per testare la connessione.
    8. Fare clic su **Salva** per salvare il servizio collegato. 
    
        ![Nuovo servizio collegato per il database SQL Azure](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. Selezionare **[dbo].[emp]** per **Tabella**. 

    ![Selezionare la tabella emp](./media/tutorial-copy-data-portal/select-emp-table.png)
27. Passare alla scheda **Schema** e fare clic su Importa schema. 

    ![Importare lo schema di destinazione](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. Selezionare la colonna **ID** e fare clic su **Elimina**. La colonna ID è una colonna Identity del database SQL, quindi non è necessario che l'attività di copia inserisca i dati in questa colonna.

    ![Eliminare la colonna ID](./media/tutorial-copy-data-portal/delete-id-column.png)
30. Passare alla scheda con la **pipeline** e verificare che sia selezionato **OutputSqlDataset** per **Sink Dataset** (Set di dati sink).

    ![Scheda con la pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. Passare alla scheda **Mapping** nella finestra delle proprietà e fare clic su **Import Schemas** (Importa schemi). Si noti che la prima e la seconda colonna del file di origine vengono mappate sui campi **FirstName** e **LastName** del database SQL.

    ![Schemi di mappatura](./media/tutorial-copy-data-portal/map-schemas.png)
33. Convalidare la pipeline facendo clic sul pulsante **Convalida**. Fare clic sulla **freccia destra** per chiudere la finestra di convalida.

    ![Output di convalida della pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. Fare clic su **Codice** nell'angolo a destra. Verrà visualizzato il codice JSON associato alla pipeline. 

    ![Pulsante Codice](./media/tutorial-copy-data-portal/code-button.png)
35. Verrà visualizzato un frammento di codice JSON simile al seguente:  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>Eseguire test della pipeline
È possibile testare una pipeline prima di pubblicare gli elementi (servizi collegati, set di dati e pipeline) in Data Factory o nel proprio repository GIT VSTS. 

1. Per testare la pipeline fare clic su **Esecuzione dei test** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 

    ![Pulsante Esecuzione dei test](./media/tutorial-copy-data-portal/test-run-output.png)
2. Verificare che i dati del file di origine siano inseriti nel database SQL di destinazione. 

    ![Verificare l'output SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Fare clic su **Pubblica** nel riquadro a sinistra. Con questa azione vengono pubblicate le entità (servizi collegati, set di dati e pipeline) create in Azure Data Factory.

    ![Pulsante Publish](./media/tutorial-copy-data-portal/publish-button.png)
4. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fare clic sul pulsante delle **notifiche** nella barra laterale sinistra. Per chiudere la finestra delle notifiche, fare clic su **X**.

    ![Visualizzare le notifiche](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configurare il repository di codice
È possibile pubblicare il codice associato agli elementi della data factory in un repository di codice di Visual Studio Team System. In questo passaggio si crea il repository di codice. 

Se non si vuole usare il repository di codice di Visual Studio Team System, è possibile ignorare questo passaggio e proseguire la pubblicazione in Data Factory come nel passaggio precedente. 

1. Fare clic su **Data Factory** nell'angolo sinistro oppure sulla freccia a discesa accanto e fare clic su **Configure Code Repository** (Configura repository di codice). 

    ![Pulsante Codice](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Nella pagina **Impostazioni repository** seguire questa procedura: 
    1. Selezionare **Visual Studio Team Services Git** per il campo **Tipo di repository**.
    2. Selezionare l'account di Visual Studio Team Services nel campo **Account di Visual Studio Team Services**.
    3. Selezionare un progetto dell'account di Visual Studio Team Services nel campo **Nome progetto**.
    4. Immettere **Tutorial2** per il **nome del repository Git** da associare alla data factory. 
    5. Verificare che sia selezionata l'opzione **Import existing Data Factory resources to repository** (Importa risorse Data Factory esistenti nel repository). 
    6. Fare clic su **Salva** per salvare le impostazioni. 

        ![Impostazioni repository](./media/tutorial-copy-data-portal/repository-settings.png)
3. Assicurarsi che sia selezionato **VSTS GIT** per il repository.

    ![VSTS GIT selezionato](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. In una scheda separata nel Web browser passare al repository **Tutorial2**. Verranno visualizzati due rami: **master** e **adf_publish**.

    ![Rami master e adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Verificare che i **file JSON** per le entità di Data Factory si trovino nel ramo **master**.

    ![File nel ramo master](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Verificare che i **file JSON** non si trovino ancora nel ramo **adf_publish**. 

    ![File nel ramo adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Aggiungere la **descrizione** della **pipeline**, quindi fare clic sul pulsante **Salva** sulla barra degli strumenti. 

    ![Aggiungere la descrizione della pipeline](./media/tutorial-copy-data-portal/pipeline-description.png)
8. A questo punto sarà visibile un **ramo** con il proprio nome utente nel repository **Tutorial2**. La modifica apportata si trova nel ramo dell'utente, non nel ramo master. È possibile pubblicare entità solo dal ramo master.

    ![Ramo dell'utente](./media/tutorial-copy-data-portal/your-branch.png)
9. Passare il mouse sul pulsante **Sincronizza** senza fare ancora clic, selezionare l'opzione **Commit Changes** (Esegui il commit delle modifiche) e fare clic sul pulsante **Sincronizza** per sincronizzare le modifiche con il ramo **master**. 

    ![Eseguire il commit e la sincronizzazione delle modifiche](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. Nella finestra Sync your changes (Sincronizzazione modifiche) seguire questa procedura: 

    1. Verificare che sia visualizzato **CopyPipeline** nell'elenco aggiornato delle pipeline.
    2. Verificare che sia selezionato **Publish changes after sync** (Pubblica modifiche dopo la sincronizzazione). Se si deseleziona questa opzione, le modifiche presenti nel ramo dell'utente verranno solo sincronizzate con il ramo master e non verranno pubblicate nel servizio Data Factory. È possibile pubblicarle in un secondo momento usando il pulsante **Pubblica**. Se si seleziona questa opzione, le modifiche verranno prima sincronizzate con il ramo master, quindi pubblicate nel servizio Data Factory.
    3. Fare clic su **Sincronizza**. 

    ![Finestra Sync your changes (Sincronizzazione modifiche)](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. A questo punto saranno presenti file nel ramo **adf_publish** del repository **Tutorial2**. In questo ramo è anche possibile trovare il modello di Azure Resource Manager per la soluzione Data Factory.  

    ![File nel ramo adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline
In questo passaggio si attiva manualmente la pipeline pubblicata nel passaggio precedente. 

1. Fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

    ![Menu Trigger Now (Attiva adesso)](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. I collegamenti nella colonna Azioni possono essere usati per visualizzare i dettagli delle attività e per eseguire di nuovo la pipeline.

    ![Monitorare l'esecuzione della pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, fare clic sul collegamento **Dettagli** (icona degli occhiali) nella colonna **Azioni**. È possibile fare clic su **Pipeline** in alto per tornare alla visualizzazione delle **esecuzioni di pipeline**. Per aggiornare la visualizzazione, fare clic su **Aggiorna**.

    ![Visualizzare le esecuzioni di attività](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Verificare che vengano aggiunte altre due righe alla tabella **emp** nel database SQL di Azure. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Attivare la pipeline in base a una pianificazione
In questa pianificazione si crea un trigger dell'utilità di pianificazione per la pipeline. Il trigger esegue la pipeline in base alla pianificazione specificata (oraria, giornaliera e così via). In questo esempio, il trigger viene impostato per l'esecuzione ogni minuto fino al valore di data e ora di fine specificato. 

1. Passare alla scheda **Modifica** a sinistra. 

    ![Scheda Modifica](./media/tutorial-copy-data-portal/edit-tab.png)
2. Fare clic su **Trigger** e selezionare **New/Edit** (Nuovo/Modifica). Se la pipeline non è attiva, passare alla pipeline. 

    ![Menu New/Edit (Nuovo/Modifica) per il trigger](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Nella finestra **Add Triggers** (Aggiungi trigger) fare clic su **Choose trigger** (Scegli trigger) e quindi su **+Nuovo**. 

    ![Add triggers (Aggiungi trigger) - Nuovo trigger](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Nella finestra **Nuovo trigger** seguire questa procedura: 

    1. Specificare **RunEveryMinute** per il **nome**.
    2. Selezionare **On Date** (In data) per **End** (Fine). 
    3. Fare clic sull'elenco a discesa **End On** (Data fine).
    4. Selezionare la **data odierna**. Per impostazione predefinita, il giorno di fine viene impostato sul giorno successivo. 
    5. Modificare il campo relativo ai **minuti** in modo che il valore sia successivo di alcuni minuti rispetto alla data/ora corrente. Il trigger viene attivato solo dopo la pubblicazione delle modifiche. Se quindi si lo si imposta a pochissimi minuti di distanza e non viene pubblicato in quel lasso di tempo, il trigger non verrà eseguito.  
    6. Fare clic su **Apply**. 

        ![Impostare le proprietà del trigger](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. Selezionare l'opzione **Attivato**. È possibile disattivare l'opzione e attivarla in un secondo momento tramite questa casella di controllo.
    8. Fare clic su **Avanti**.

        ![Trigger attivato - Avanti](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Ogni esecuzione della pipeline è associata a un costo. Impostare quindi correttamente la data di fine. 
6. Nella pagina **Trigger Run Parameters** (Parametri esecuzione trigger) esaminare l'avviso e quindi fare clic su **Fine**. La pipeline in questo esempio non accetta alcun parametro. 

    ![Parametri della pipeline](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. Fare clic su **Pubblica** per pubblicare le modifiche nel repository. Il trigger viene attivato solo al termine della pubblicazione. 

    ![Pubblicare il trigger](./media/tutorial-copy-data-portal/publish-trigger.png) 
8. Passare alla scheda **Monitoraggio** a sinistra per visualizzare le esecuzioni di pipeline attivate. 

    ![Esecuzioni di pipeline attivate](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. Per passare dalla visualizzazione delle esecuzioni di pipeline alla visualizzazione delle esecuzioni di trigger, fare clic su Pipeline Runs (Esecuzioni di pipeline) e selezionare Trigger Runs (Esecuzioni del trigger).
    
    ![Menu Trigger Runs (Esecuzioni del trigger)](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. Verrà visualizzato l'elenco di esecuzioni di trigger. 

    ![Elenco delle esecuzioni di trigger](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. Verificare che vengano inserite due righe per ogni minuto (per ogni esecuzione di pipeline) nella tabella **emp** fino alla data e ora di fine specificata. 

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività di copia.
> * Eseguire test della pipeline
> * Attivare manualmente la pipeline
> * Attivare la pipeline in base a una pianificazione
> * Monitorare le esecuzioni di pipeline e attività.


Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-data-tool.md)
