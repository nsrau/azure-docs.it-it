---
title: Copiare dati da SQL Server a un archivio BLOB usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un archivio dati locale al cloud usando un runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: e21c08d418022430400ff14baedc1759d2d16069
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copiare dati da un database di SQL Server locale a un archivio BLOB di Azure
In questa esercitazione si usa l'interfaccia utente di Azure Data Factory per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Si crea e si usa un runtime di integrazione self-hosted, che sposta i dati tra gli archivi dati locali e cloud.

> [!NOTE]
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 di Data Factory, disponibile a livello generale, vedere la [documentazione per Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Questo articolo non offre una presentazione dettagliata di Data Factory. Per altre informazioni, vedere [Introduzione ad Azure Data Factory](introduction.md). 

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
Per creare istanze di Data Factory, è necessario che all'account utente usato per accedere ad Azure sia assegnato un ruolo *Collaboratore* o *Proprietario* oppure che l'account utente sia un *amministratore* della sottoscrizione di Azure. 

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al portale di Azure. Nell'angolo superiore destro selezionare il nome utente e quindi **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere l'articolo su come [aggiungere i ruoli](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
In questa esercitazione si usa un database di SQL Server locale come archivio dati di *origine*. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server locale (origine) a un archivio BLOB (sink). Si crea quindi una tabella denominata **emp** nel database di SQL Server e si inseriscono alcune voci di esempio nella tabella. 

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
In questa esercitazione, come archivio dati di destinazione/sink si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB). Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account). La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server locale (origine) a un archivio BLOB (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione. Per recuperare il nome e la chiave dell'account di archiviazione, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com) con nome utente e password di Azure. 

2. Nel riquadro sinistro selezionare **Altri servizi**. Usare la parola chiave **Archiviazione** come filtro e quindi selezionare **Account di archiviazione**.

    ![Ricerca dell'account di archiviazione](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Nell'elenco degli account di archiviazione filtrare, se necessario, il proprio account di archiviazione. Selezionare quindi l'account di archiviazione. 

4. Nella finestra **Account di archiviazione** selezionare **Chiavi di accesso**.

    ![Chiavi di accesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Nelle caselle **Nome account di archiviazione** e **key1** copiare i valori e incollarli nel Blocco note o in un altro editor per usarli in seguito nell'esercitazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB. 

1. Nella finestra **Account di archiviazione** passare a **Panoramica** e quindi selezionare **BLOB**. 

    ![Selezionare l'opzione BLOB](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Nella finestra **Servizio BLOB** selezionare **Contenitore**. 

    ![Pulsante Contenitore](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Nella finestra **Nuovo contenitore** immettere **adftutorial** in **Nome**. Selezionare **OK**. 

    ![Finestra Nuovo contenitore](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Selezionare **adftutorial** nell'elenco di contenitori.

    ![Selezione del contenitore](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Tenere aperta la finestra **Contenitore** per **adftutorial**. perché verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.

    ![Finestra Contenitore](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory. 

1. Aprire il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Nel menu a sinistra selezionare **Nuovo** > **Dati e analisi** > **Data factory**.
   
   ![Creazione di una nuova data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Il nome della data factory deve essere *univoco a livello globale*. Se viene visualizzato il messaggio di errore seguente per il campo Nome, modificare il nome della data factory, ad esempio usando nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).
  
   ![Nome della nuova data factory](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
5. In **Gruppo di risorse** eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).
6. In **Versione** selezionare **V2 (anteprima)**.
7. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati da Data Factory possono trovarsi in altre aree.
8. Selezionare **Aggiungi al dashboard**. 
9. Selezionare **Create**.
10. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory):

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
11. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine:
   
    ![Home page di Data factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
12. Selezionare il riquadro **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata. 


## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). Verrà creata automaticamente una pipeline. La pipeline sarà visibile nella visualizzazione albero e verrà aperto il relativo editor. 

   ![Pagina Attività iniziali](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. Nella scheda **Generale** nella parte inferiore della finestra **Proprietà** immettere **SQLServerToBlobPipeline** in **Nome**.

   ![Nome della pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
3. Nella casella degli strumenti **Attività** espandere **Flusso di dati**. Trascinare l'attività **Copia** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **CopySqlServerToAzureBlobActivity**.

   ![Nome dell'attività](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
4. Nella finestra **Proprietà** passare alla scheda **Origine** e selezionare **+ Nuovo**.

   ![Scheda Origine](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
5. Nella finestra **Nuovo set di dati** cercare **SQL Server**. Selezionare **SQL Server** e quindi **Fine**. Viene visualizzata una nuova scheda intitolata **SqlServerTable1**. Il set di dati **SqlServerTable1** sarà visibile anche nella visualizzazione albero a sinistra. 

   ![Selezione di SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
6. Nella scheda **Generale** nella parte inferiore della finestra **Proprietà** immettere **SqlServerDataset** in **Nome**.
    
   ![Nome del set di dati di origine](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
7. Passare alla scheda **Connessione** e selezionare **+ Nuovo**. In questo passaggio si crea una connessione all'archivio dati di origine (database SQL Server). 

   ![Connessione al set di dati di origine](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
8. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Nuovo runtime di integrazione**. In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati dal database di SQL Server presente nel computer all'archivio BLOB. 

   ![Nuovo runtime di integrazione](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
9. Nella finestra **Integration Runtime Setup** (Configurazione runtime di integrazione) selezionare **Private Network** (Rete privata) e quindi **Avanti**. 

   ![Selezione di una rete privata](./media/tutorial-hybrid-copy-portal/select-private-network.png)
10. Immettere un nome per il runtime di integrazione e selezionare **Avanti**.
    
    ![Nome del runtime di integrazione](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
11. In **Option 1: Express setup** (Opzione 1: Installazione rapida) selezionare **Click here to launch the express setup for this computer** (Fare clic qui per avviare l'installazione rapida per il computer). 

    ![Collegamento per l'installazione rapida](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
12. Nella finestra **Installazione rapida di Integration Runtime (self-hosted)** selezionare **Chiudi**. 

    ![Installazione rapida di Integration Runtime (self-hosted)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
13. Nel Web browser selezionare **Fine** nella finestra **Integration Runtime Setup** (Configurazione runtime di integrazione). 

    ![Impostazione runtime di integrazione](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
14. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    a. In **Nome** immettere **SqlServerLinkedService**.

    b. In **Connect via integration runtime** (Connetti tramite runtime di integrazione) verificare che venga visualizzato il runtime di integrazione self-hosted creato in precedenza.

    c. In **Nome server** immettere il nome dell'istanza di SQL Server. 

    d. In **Nome database** immettere il nome del database con la tabella **emp**.

    e. In **Tipo di autenticazione** selezionare il tipo di autenticazione appropriato che dovrà essere usato da Data Factory per la connessione al database di SQL Server.

    f. In **Nome utente** e **Password** immettere il nome utente e la password. Se è necessario usare una barra rovesciata (\\) nell'account utente o nel nome del server, farla precedere dal carattere di escape (\\). Ad esempio, usare *mydomain\\\\myuser*.

    g. Selezionare **Test connessione**. Eseguire questo passaggio per verificare che Data Factory possa connettersi al database di SQL Server usando il runtime di integrazione self-hosted creato.

    h. Per salvare il servizio collegato, selezionare **Salva**.

       ![Impostazioni del nuovo servizio collegato](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
15. Verrà visualizzata di nuovo la finestra con il set di dati di origine aperto. Nella scheda **Connessione** della finestra **Proprietà** seguire questa procedura: 

    a. In **Servizio collegato** verificare che venga visualizzato **SqlServerLinkedService**.

    b. In **Tabella** selezionare **[dbo].[emp]**.

    ![Informazioni per la connessione al set di dati di origine](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
16. Passare alla scheda con **SQLServerToBlobPipeline** oppure selezionare **SQLServerToBlobPipeline** nella visualizzazione albero. 

    ![Scheda con la pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
17. Passare alla scheda **Sink** nella parte inferiore della finestra **Proprietà** e selezionare **+ Nuovo**. 

    ![Scheda Sink](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
18. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure**. Selezionare quindi **Fine**. Verrà aperta una nuova scheda per il set di dati, che è riportato anche nella visualizzazione albero. 

    ![Selezione dell'archivio BLOB](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
19. In **Nome** immettere **AzureBlobDataset**.

    ![Nome del set di dati sink](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
20. Passare alla scheda **Connessione** nella parte inferiore della finestra **Proprietà**. Selezionare **+ Nuovo** accanto a **Servizio collegato**. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
21. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    a. In **Nome** immettere **AzureStorageLinkedService**.

    b. In **Nome account di archiviazione**selezionare il proprio account di archiviazione.

    c. Per testare la connessione all'account di archiviazione, selezionare **Test connessione**.

    d. Selezionare **Salva**.

    ![Impostazioni del servizio collegato per Archiviazione](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
22.  Verrà visualizzata di nuovo la finestra con il set di dati sink aperto. Nella scheda **Connessione** seguire questa procedura: 

        a. In **Servizio collegato** verificare che sia selezionato **AzureStorageLinkedService**.

        b. Nella parte relativa alla **cartella** di **Percorso file** immettere **adftutorial/fromonprem**. Se la cartella di output non esiste nel contenitore adftutorial, verrà creata automaticamente da Data Factory.

        c. Nella parte relativa al **nome file** di **Percorso file** immettere `@CONCAT(pipeline().RunId, '.txt')`.

     ![Connessione al set di dati sink](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
23. Passare alla scheda con la pipeline aperta oppure selezionare la pipeline nella visualizzazione albero. In **Sink Dataset** (Set di dati sink) verificare che sia selezionato **AzureBlobDataset**. 

    ![Set di dati sink selezionato](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
24. Per convalidare le impostazioni della pipeline, selezionare **Convalida** sulla barra degli strumenti della pipeline. Per chiudere il **report di convalida della pipeline**, selezionare **Chiudi**. 

    ![Convalidare la pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
25. Per pubblicare le entità create in Data Factory, selezionare **Pubblica tutti**.

    ![Pulsante Publish](./media/tutorial-hybrid-copy-portal/publish-button.png)
26. Attendere fino alla visualizzazione del messaggio popup **Pubblicazione riuscita**. Per verificare lo stato della pubblicazione, selezionare il collegamento **Show Notifications** (Mostra notifiche) a sinistra. Per chiudere la finestra delle notifiche, selezionare **Chiudi**. 

    ![Pubblicazione riuscita](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Selezionare **Trigger** sulla barra degli strumenti della pipeline e quindi **Trigger Now** (Attiva adesso).

![Attivare l'esecuzione della pipeline](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Verrà visualizzata la pipeline attivata manualmente nel passaggio precedente. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Vengono visualizzate solo le esecuzioni di attività perché la pipeline contiene solo un'attività. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Per tornare alla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline), selezionare **Pipeline** in alto.

    ![Monitorare le esecuzioni delle attività](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata *fromonprem* nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file *dbo.emp.txt*. 

1. Nella finestra del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output.

    ![Cartella di output creata](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Selezionare `fromonprem` nell'elenco di cartelle. 
3. Verificare che venga visualizzato un file denominato `dbo.emp.txt`.

    ![File di output](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da una posizione a un'altra in un archivio BLOB. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione. 
> * Creare i set di dati per SQL Server e l'archivio BLOB.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Per un elenco degli archivi dati supportati da Data Factory, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Per informazioni sulla copia di dati in blocco da un'origine a una destinazione, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
>[Copiare i dati nel bulk](tutorial-bulk-copy-portal.md)
