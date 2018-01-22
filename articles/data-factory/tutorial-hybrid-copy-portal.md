---
title: Copiare dati da SQL Server a un archivio BLOB usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un archivio dati locale al cloud di Azure usando un runtime di integrazione self-hosted in Azure Data Factory.
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 7b734a76545dbcbddac3c7ad7beae60d662a9129
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Esercitazione: Copiare dati da un database di SQL Server locale all'archivio BLOB di Azure
In questa esercitazione si usa l'interfaccia utente di Azure Data Factory per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Si crea e si usa un runtime di integrazione self-hosted, che sposta i dati tra gli archivi dati locali e cloud. 

> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md). 

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure. 
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

## <a name="prerequisites"></a>prerequisiti
### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, all'account utente usato per accedere ad Azure deve essere assegnato un ruolo *collaboratore* o *proprietario* oppure l'account deve essere un *amministratore* della sottoscrizione di Azure. 

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al portale di Azure, selezionare il nome utente nell'angolo in alto a destra e quindi **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere l'articolo [Aggiungere i ruoli](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
In questa esercitazione si usa un database di SQL Server locale come archivio dati di *origine*. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server locale (origine) all'archivio BLOB di Azure (sink). Si crea quindi una tabella denominata **emp** nel database di SQL Server e inserire una coppia di voci di esempio nella tabella. 

1. Avviare SQL Server Management Studio. Se non è già installato nel computer, passare a [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Connettersi all'istanza di SQL Server usando le credenziali. 

3. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**. 
 
4. Nella finestra **Nuovo database** immettere un nome per il database e fare clic su **OK**. 

5. Per creare la tabella **emp** e inserirvi alcuni dati di esempio, eseguire questo script di query sul database:

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul database creato e scegliere **Nuova query**.

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati di destinazione/sink. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account). La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server locale (origine) a questo archivio BLOB di Azure (sink). 

#### <a name="get-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione di Azure. Recuperare il nome e la chiave dell'account di archiviazione nel modo seguente: 

1. Accedere al [portale di Azure](https://portal.azure.com) con nome utente e password. 

2. Nel riquadro a sinistra selezionare **Altri servizi**, usare la parola chiave **Archiviazione** come filtro e selezionare **Account di archiviazione**.

    ![Cercare l'account di archiviazione](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Nell'elenco degli account di archiviazione filtrare, se necessario, e quindi selezionare il proprio account di archiviazione. 

4. Nella finestra **Account di archiviazione** selezionare **Chiavi di accesso**.

    ![Recuperare il nome e la chiave dell'account di archiviazione](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Nelle caselle **Nome account di archiviazione** e **key1** copiare i valori e incollarli nel Blocco note o in un altro editor per usarli in seguito nell'esercitazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure. 

1. Nella finestra **Account di archiviazione** passare a **Panoramica** e quindi selezionare **BLOB**. 

    ![Selezionare l'opzione BLOB](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Nella finestra **Servizio BLOB** selezionare **Contenitore**. 

    ![Pulsante Aggiungi contenitore](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Nella finestra di dialogo **Nuovo contenitore** immettere **adftutorial** nella casella **Nome** e fare clic su **OK**. 

    ![Immettere il nome del contenitore](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Selezionare **adftutorial** nell'elenco di contenitori.  

    ![Selezionare il contenitore](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Tenere aperta la finestra **Contenitore** per **adftutorial**. perché verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.

    ![Finestra Contenitore](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
In questo passaggio viene creata una data factory e viene avviata l'interfaccia utente di Azure Data Factory per creare una pipeline nella data factory. 

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Pagina Nuova data factory](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata. 


## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella **pagina delle attività iniziali** fare clic su **Create pipeline** (Crea pipeline). Verrà creata automaticamente una pipeline. La pipeline sarà visibile nella visualizzazione albero e il relativo editor sarà aperto. 

   ![Pagina delle attività iniziali](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. Nella scheda **Generale** della finestra **Proprietà** in basso specificare **SQLServerToBlobPipeline** per **Nome**.

   ![Nome della pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. Nella casella degli strumenti **Attività** espandere **Flusso di dati** e trascinare l'attività **Copia** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **CopySqlServerToAzureBlobActivity**.

   ![Nome dell'attività](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. Nella finestra delle proprietà passare alla scheda **Origine** e fare clic su **+ Nuovo**.

   ![Pulsante per il nuovo set di dati di origine](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. Nella finestra **Nuovo set di dati** cercare **SQL Server**, selezionare **SQL Server** e fare clic su **Fine**. Viene visualizzata una nuova scheda intitolata **SqlServerTable1**. Il set di dati **SqlServerTable1** sarà anche visibile nella visualizzazione albero a sinistra. 

   ![Selezionare SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. Nella scheda **Generale** della finestra delle proprietà immettere **SqlServerDataset** per **Nome**.
    
   ![Set di dati di origine: nome](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. Passare alla scheda **Connessioni** e fare clic su **+ Nuovo**. In questo passaggio si crea una connessione all'archivio dati di origine (database SQL Server). 

   ![Set di dati di origine: pulsante per la nuova connessione](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. Nella pagina **New Linked Service** (Nuovo servizio collegato) fare clic su **Nuovo runtime di integrazione**. In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati dal database SQL Server presente nel computer all'archivio BLOB di Azure. 

   ![Nuovo runtime di integrazione](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. Nella finestra **Integration Runtime Setup** (Configurazione runtime di integrazione) selezionare **Private Network** (Rete privata) e fare clic su **Avanti**. 

   ![Selezionare una rete privata](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. Specificare un nome per il runtime di integrazione e fare clic su **Avanti**.  
    
   ![Runtime di integrazione: nome](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. Fare clic su **Click here to launch the express setup for this computer** (Fare clic qui per avviare l'installazione rapida per il computer) nella sezione **Option 1: Express setup** (Opzione 1: Installazione rapida). 

   ![Fare clic sul collegamento per l'installazione rapida](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. Nella finestra **Installazione rapida di Integration Runtime (self-hosted)** fare clic su **Chiudi**. 

   ![Installazione del runtime di integrazione completata](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. Nel Web browser fare clic su **Fine** nella finestra **Integration Runtime Setup** (Installazione runtime di integrazione). Verrà visualizzata di nuovo la finestra **New Linked Service** (Nuovo servizio collegato).

   ![Installazione del runtime di integrazione: fine](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **SqlServerLinkedService** per **Nome**.
    2. Verificare che il runtime di integrazione self-hosted creato in precedenza venga visualizzato per **Connect via integration runtime** (Connetti tramite runtime di integrazione).
    3. Specificare il nome dell'istanza di SQL Server per **Nome server**. 
    4. Specificare il nome del database con la tabella **emp** per il campo **Nome database**. 
    5. Selezionare il **tipo di autenticazione** appropriato che Data Factory dovrà usare per la connessione al database di SQL Server. 
    6. Specificare **nome utente** e **password**. Se è necessario usare una barra rovesciata (\\) nell'account utente o nel nome del server, farla precedere dal carattere di escape (\\). Ad esempio, usare *mydomain\\\\myuser*. 
    7. Fare clic su **Test connessione**. Eseguire questo passaggio per verificare che il servizio Data Factory possa connettersi al database di SQL Server usando il runtime di integrazione self-hosted creato. 
    8. Fare clic su **Salva** per salvare il servizio collegato.

       ![Servizio collegato di SQL Server: impostazioni](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. Verrà visualizzata di nuovo la finestra con il set di dati di origine aperto. Nella scheda **Connessione** della finestra **Proprietà** seguire questa procedura: 

    1. Verificare che per **Servizio collegato** sia visualizzato **SqlServerLinkedService**. 
    2. Selezionare **[dbo].[emp]** per **Tabella**.

        ![Set di dati di origine: informazioni di connessione](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. Passare alla scheda con SQLServerToBlobPipeline oppure fare clic su **SQLServerToBlobPipeline** nella visualizzazione albero. 

    ![Scheda con la pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. Passare alla scheda **Sink** della finestra **Proprietà** e fare clic su **+ Nuovo**. 

    ![Set di dati sink: pulsante Nuovo](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e fare clic su **Fine**. Verrà aperta una nuova scheda per il set di dati, che è riportato anche nella visualizzazione albero. 

    ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. Immettere **AzureBlobDataset** per **Nome**.

    ![Set di dati sink: nome](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. Passare alla scheda **Connessione** nella finestra **Proprietà** e fare clic su **+ Nuovo** per **Servizio collegato**. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **AzureStorageLinkedService** per **Nome**.
    2. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione**. 
    3. Fare clic su **Test connessione** per testare la connessione all'account di archiviazione di Azure.
    4. Fare clic su **Save**.

        ![Servizio collegato Archiviazione di Azure: impostazioni](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  Verrà visualizzata di nuovo la finestra con il set di dati sink aperto. Nella scheda **Connessione** seguire questa procedura: 

        1. Verificare che per **Servizio collegato** sia selezionato **AzureStorageLinkedService**.
        2. Nella parte relativa alla **cartella** del campo **Percorso file** immettere **adftutorial/fromonprem**. Se la cartella output non esiste nel contenitore adftutorial, il servizio Data Factory la crea automaticamente.
        3. Immettere `@CONCAT(pipeline().RunId, '.txt')` per la parte relativa al **nome file** del campo **Percorso file**.

            ![Set di dati sink: connessione](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. Passare alla scheda con la pipeline aperta oppure fare clic sulla **pipeline** nella **visualizzazione albero**. Assicurarsi che **AzureBlobDataset** sia selezionato per **Sink Dataset** (Set di dati sink). 

    ![Set di dati sink selezionato ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. Per convalidare le impostazioni della pipeline, fare clic su Convalida sulla barra degli strumenti della pipeline. Chiudere il **report di convalida della pipeline** facendo clic su **X** nell'angolo destro. 

    ![Convalidare la pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. Pubblicare le entità create nel servizio Azure Data Factory facendo clic su **Pubblica**.

    ![Pulsante Publish](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. È anche possibile verificare lo stato della pubblicazione facendo clic su **Show Notifications** (Mostra notifiche) a sinistra. Per chiudere la finestra delle notifiche, fare clic su **X**. 

    ![Pubblicazione riuscita](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Fare clic su **Trigger** sulla barra degli strumenti per la pipeline e quindi su **Trigger Now** (Attiva adesso).

![Trigger now (Attiva adesso)](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Verrà visualizzata la pipeline attivata manualmente nel passaggio precedente. 

    ![Esecuzioni di pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività, vengono visualizzate solo esecuzioni di attività. Per informazioni dettagliate sull'operazione di copia, fare clic sul collegamento **Dettagli** (icona degli occhiali) nella colonna **Azioni**. È possibile tornare alla visualizzazione delle esecuzioni della pipeline facendo clic su **Pipeline** in alto.

    ![Esecuzioni attività](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata *fromonprem* nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file *dbo.emp.txt*. 

1. Nella finestra del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output.

    ![Cartella di output creata](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Selezionare `fromonprem` nell'elenco di cartelle. 
3. Verificare che venga visualizzato un file denominato `dbo.emp.txt`.

    ![File di output](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure. 
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Per un elenco degli archivi dati supportati da Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati in blocco da un'origine a una destinazione:

> [!div class="nextstepaction"]
>[Copiare i dati nel bulk](tutorial-bulk-copy-portal.md)
