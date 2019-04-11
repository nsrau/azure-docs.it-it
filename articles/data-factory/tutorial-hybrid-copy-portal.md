---
title: Copiare dati da SQL Server a un archivio BLOB usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un archivio dati locale al cloud usando un runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: abnarain
ms.openlocfilehash: 7e09c36128c3471ce9df33b2a78eb14fab89435b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256992"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copiare dati da un database di SQL Server locale a un archivio BLOB di Azure
In questa esercitazione si usa l'interfaccia utente di Azure Data Factory per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Si crea e si usa un runtime di integrazione self-hosted, che sposta i dati tra gli archivi dati locali e cloud.

> [!NOTE]
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

## <a name="prerequisites"></a>Prerequisiti
### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, all'account utente usato per accedere ad Azure deve essere assegnato un ruolo *collaboratore* o *proprietario* oppure l'account utente deve essere un *amministratore* della sottoscrizione di Azure. 

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al portale di Azure. Nell'angolo superiore destro selezionare il nome utente e quindi **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
In questa esercitazione si usa un database di SQL Server locale come archivio dati di *origine*. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server locale (origine) a un archivio BLOB (sink). Si crea quindi una tabella denominata **emp** nel database di SQL Server e si inseriscono alcune voci di esempio nella tabella. 

1. Avviare SQL Server Management Studio. Se non è già installato nel computer, passare a [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Connettersi all'istanza di SQL Server usando le credenziali. 

1. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**. 
1. Nella finestra **Nuovo database** immettere un nome per il database e fare clic su **OK**. 

1. Per creare la tabella **emp** e inserirvi alcuni dati di esempio, eseguire questo script di query sul database:

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

1. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul database creato e scegliere **Nuova query**.

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione, come archivio dati di destinazione/sink si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB). Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md). La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server locale (origine) a un archivio BLOB (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione. Per recuperare il nome e la chiave dell'account di archiviazione, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com) con nome utente e password di Azure. 

1. Nel riquadro sinistro selezionare **Altri servizi**. Usare la parola chiave **Archiviazione** come filtro e quindi selezionare **Account di archiviazione**.

    ![Ricerca dell'account di archiviazione](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Nell'elenco degli account di archiviazione filtrare, se necessario, il proprio account di archiviazione. Selezionare quindi l'account di archiviazione. 

1. Nella finestra **Account di archiviazione** selezionare **Chiavi di accesso**.

    ![Chiavi di accesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Nelle caselle **Nome account di archiviazione** e **key1** copiare i valori e incollarli nel Blocco note o in un altro editor per usarli in seguito nell'esercitazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB. 

1. Nella finestra **Account di archiviazione** passare a **Panoramica** e quindi selezionare **BLOB**. 

    ![Selezionare l'opzione BLOB](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Nella finestra **Servizio BLOB** selezionare **Contenitore**. 

    ![Pulsante Contenitore](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. Nella finestra **Nuovo contenitore** immettere **adftutorial** in **Nome**. Selezionare **OK**. 

    ![Finestra Nuovo contenitore](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Selezionare **adftutorial** nell'elenco di contenitori.

    ![Selezione del contenitore](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Tenere aperta la finestra **Contenitore** per **adftutorial** perché verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.

    ![Finestra Contenitore](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Creare una data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory. 

1. Aprire il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**:
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
   
     ![Pagina Nuova data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

Il nome della data factory deve essere *univoco a livello globale*. Se viene visualizzato il messaggio di errore seguente per il campo Nome, modificare il nome della data factory, ad esempio usando nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

   ![Nome della nuova data factory](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:
   
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.
        
     Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati da Data Factory possono trovarsi in altre aree.
1. Selezionare **Aggiungi al dashboard**. 
1. Selezionare **Crea**.
1. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory):

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
1. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine:
   
    ![Home page di Data factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
1. Selezionare il riquadro **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata. 


## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). Verrà creata automaticamente una pipeline. La pipeline sarà visibile nella visualizzazione albero e verrà aperto il relativo editor. 

   ![Pagina Attività iniziali](./media/tutorial-hybrid-copy-portal/get-started-page.png)

1. Nella scheda **Generale** nella parte inferiore della finestra **Proprietà** immettere **SQLServerToBlobPipeline** in **Nome**.

   ![Nome della pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. Nella casella degli strumenti **Attività** espandere **Flusso di dati**. Trascinare l'attività **Copia** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **CopySqlServerToAzureBlobActivity**.

   ![Nome dell'attività](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

1. Nella finestra **Proprietà** passare alla scheda **Origine** e selezionare **+ Nuovo**.

   ![Scheda Origine](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

1. Nella finestra **Nuovo set di dati** cercare **SQL Server**. Selezionare **SQL Server** e quindi **Fine**. Viene visualizzata una nuova scheda intitolata **SqlServerTable1**. Il set di dati **SqlServerTable1** sarà visibile anche nella visualizzazione albero a sinistra. 

   ![Selezione di SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

1. Nella scheda **Generale** nella parte inferiore della finestra **Proprietà** immettere **SqlServerDataset** in **Nome**.

   ![Nome del set di dati di origine](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

1. Passare alla scheda **Connessione** e selezionare **+ Nuovo**. In questo passaggio si crea una connessione all'archivio dati di origine (database SQL Server). 

   ![Connessione al set di dati di origine](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

1. Nella finestra **Nuovo Servizio collegato** aggiungere **Nome** come **SqlServerLinkedService**. Selezionare **Nuovo** in **Connect via integration runtime** (Connetti tramite runtime di integrazione). In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati dal database di SQL Server presente nel computer all'archivio BLOB. 

   ![Nuovo runtime di integrazione](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

1. Nella finestra **Integration Runtime Setup** (Configurazione runtime di integrazione) selezionare **Private Network** (Rete privata) e quindi **Avanti**. 

   ![Selezione di una rete privata](./media/tutorial-hybrid-copy-portal/select-private-network.png)

1. Immettere un nome per il runtime di integrazione e selezionare **Avanti**.

    ![Nome del runtime di integrazione](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

1. In **Option 1: Express setup** (Opzione 1: Installazione rapida) selezionare**Click here to launch the express setup for this computer** (Fare clic qui per avviare l'installazione rapida per il computer). 

    ![Collegamento per l'installazione rapida](./media/tutorial-hybrid-copy-portal/click-express-setup.png)

1. Nella finestra **Installazione rapida di Integration Runtime (self-hosted)** selezionare **Chiudi**. 

    ![Installazione rapida di Integration Runtime (self-hosted)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Nella finestra **Nuovo Servizio collegato** verificare che il **runtime di integrazione** creato in precedenza sia selezionato in **Connect via integration runtime** (Connetti tramite runtime di integrazione). 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

1. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    a. In **Nome** immettere **SqlServerLinkedService**.

    b. In **Connect via integration runtime** (Connetti tramite runtime di integrazione) verificare che venga visualizzato il runtime di integrazione self-hosted creato in precedenza.

    c. In **Nome server** immettere il nome dell'istanza di SQL Server. 

    d. In **Nome database** immettere il nome del database con la tabella **emp**.

    e. In **Tipo di autenticazione** selezionare il tipo di autenticazione appropriato che dovrà essere usato da Data Factory per la connessione al database di SQL Server.

    f. In **Nome utente** e **Password** immettere il nome utente e la password. Se è necessario usare una barra rovesciata (\\) nell'account utente o nel nome del server, farla precedere dal carattere di escape (\\). Ad esempio, usare *mydomain\\\\myuser*.

    g. Selezionare **Test connessione**. Eseguire questo passaggio per verificare che Data Factory possa connettersi al database di SQL Server usando il runtime di integrazione self-hosted creato.

    h. Per salvare il servizio collegato, selezionare **Fine**.

       

1. Verrà visualizzata di nuovo la finestra con il set di dati di origine aperto. Nella scheda **Connessione** della finestra **Proprietà** seguire questa procedura: 

    a. In **Servizio collegato** verificare che venga visualizzato **SqlServerLinkedService**.

    b. In **Tabella** selezionare **[dbo].[emp]**.

    ![Informazioni per la connessione al set di dati di origine](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

1. Passare alla scheda con **SQLServerToBlobPipeline** oppure selezionare **SQLServerToBlobPipeline** nella visualizzazione albero. 

    ![Scheda con la pipeline](./media/tutorial-hybrid-copy-portal/pipeline-tab.png)

1. Passare alla scheda **Sink** nella parte inferiore della finestra **Proprietà** e selezionare **+ Nuovo**. 

    ![Scheda Sink](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

1. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure**. Selezionare quindi **Fine**. Verrà aperta una nuova scheda per il set di dati, che è riportato anche nella visualizzazione albero. 

    ![Selezione dell'archivio BLOB](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

1. In **Nome** immettere **AzureBlobDataset**.

    ![Nome del set di dati sink](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

1. Passare alla scheda **Connessione** nella parte inferiore della finestra **Proprietà**. Selezionare **+ Nuovo** accanto a **Servizio collegato**. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

1. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    a. In **Nome** immettere **AzureStorageLinkedService**.

    b. In **Nome account di archiviazione**selezionare il proprio account di archiviazione.

    c. Per testare la connessione all'account di archiviazione, selezionare **Test connessione**.

    d. Selezionare **Salva**.

    ![Impostazioni del servizio collegato per Archiviazione](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

1. Verrà visualizzata di nuovo la finestra con il set di dati sink aperto. Nella scheda **Connessione** seguire questa procedura: 

    a. In **Servizio collegato** verificare che sia selezionato **AzureStorageLinkedService**.

    b. Nella parte **cartella**/ **directory** di **Percorso file** immettere **adftutorial/fromonprem**. Se la cartella di output non esiste nel contenitore adftutorial, verrà creata automaticamente da Data Factory.

    c. Nella parte **nome file** di **Percorso file** selezionare **Aggiungi contenuto dinamico**.   

    ![valore nome file dinamico](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Aggiungere `@CONCAT(pipeline().RunId, '.txt')`, selezionare **Fine**. In questo modo è possibile rinominare il file in PipelineRunID.txt. 

    ![espressione dinamica per la risoluzione del nome file](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![Connessione al set di dati sink](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

1. Passare alla scheda con la pipeline aperta oppure selezionare la pipeline nella visualizzazione albero. In **Sink Dataset** (Set di dati sink) verificare che sia selezionato **AzureBlobDataset**. 

    ![Set di dati sink selezionato](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

1. Per convalidare le impostazioni della pipeline, selezionare **Convalida** sulla barra degli strumenti della pipeline. Per chiudere il **report di convalida della pipeline**, selezionare **Chiudi**. 

    ![Convalidare la pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

1. Per pubblicare le entità create in Data Factory, selezionare **Pubblica tutti**.

    ![Pulsante Publish](./media/tutorial-hybrid-copy-portal/publish-button.png)

1. Attendere fino alla visualizzazione del messaggio popup **Pubblicazione riuscita**. Per verificare lo stato della pubblicazione, selezionare il collegamento **Show Notifications** (Mostra notifiche) a sinistra. Per chiudere la finestra delle notifiche, selezionare **Chiudi**. 

    ![Pubblicazione riuscita](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Selezionare **Trigger** sulla barra degli strumenti della pipeline e quindi **Trigger Now** (Attiva adesso).

![Attivare l'esecuzione della pipeline](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Verrà visualizzata la pipeline attivata manualmente nel passaggio precedente. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Vengono visualizzate solo le esecuzioni di attività perché la pipeline contiene solo un'attività. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Per tornare alla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline), selezionare **Pipeline** in alto.

    ![Monitorare le esecuzioni delle attività](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata *fromonprem* nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file *[pipeline().RunId].txt*. 

![confermare il nome file di output](./media/tutorial-hybrid-copy-portal/sink-output.png)


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
>[Copiare dati in blocco](tutorial-bulk-copy-portal.md)
