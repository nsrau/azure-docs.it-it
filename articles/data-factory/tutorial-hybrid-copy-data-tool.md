---
title: Copiare dati locali con lo strumento Copia dati di Azure
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per copiare i dati da un database SQL Server locale all'archivio BLOB di Azure.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 04/09/2018
ms.author: abnarain
ms.openlocfilehash: 3b9c9371d947b0193f93b8084fbaaf66e3253997
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683556"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copiare dati da un database di SQL Server locale a un archivio BLOB di Azure con lo strumento Copia dati
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](tutorial-hybrid-copy-data-tool.md)

In questa esercitazione si usa il portale di Azure per creare una data factory. Si userà quindi lo strumento Copia dati per creare una pipeline che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure.

> [!NOTE]
> - Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti
### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, all'account utente usato per accedere ad Azure deve essere assegnato un ruolo *collaboratore* o *proprietario* oppure l'account deve essere un *amministratore* della sottoscrizione di Azure. 

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al portale di Azure. Selezionare il nome utente nell'angolo superiore destro e quindi **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
In questa esercitazione si usa un database di SQL Server locale come archivio dati di *origine*. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server locale (origine) a un archivio BLOB (sink). Si crea quindi una tabella denominata **emp** nel database di SQL Server e si inseriscono alcune voci di esempio nella tabella. 

1. Avviare SQL Server Management Studio. Se non è già installato nel computer, passare a [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Connettersi all'istanza di SQL Server usando le credenziali. 

1. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**. 

1. Nella finestra **Nuovo database** immettere un nome per il database e fare clic su **OK**. 

1. Per creare la tabella **emp** e inserirvi alcuni dati di esempio, eseguire questo script di query sul database. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul database creato e scegliere **Nuova query**.

    ```sql
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

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione, come archivio dati di destinazione/sink si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB). Se non si ha un account di archiviazione per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md) per istruzioni su come crearne uno. La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server locale (origine) a questo archivio BLOB (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione. Per recuperare il nome e la chiave dell'account di archiviazione, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com) con nome utente e password di Azure. 

1. Nel riquadro sinistro selezionare **Tutti i servizi**. Usare la parola chiave **Archiviazione** come filtro e quindi selezionare **Account di archiviazione**.

    ![Ricerca dell'account di archiviazione](media/doc-common-process/search-storage-account.png)

1. Nell'elenco degli account di archiviazione filtrare, se necessario, il proprio account di archiviazione. Selezionare quindi l'account di archiviazione. 

1. Nella finestra **Account di archiviazione** selezionare **Chiavi di accesso**.


1. Nelle caselle **Nome account di archiviazione** e **key1** copiare i valori e incollarli nel Blocco note o in un altro editor per usarli in seguito nell'esercitazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB. 

1. Nella finestra **Account di archiviazione** passare a **Panoramica** e quindi selezionare **BLOB**. 

1. Nella finestra **BLOB** selezionare **+ Contenitore**. 

1. Nella finestra **Nuovo contenitore** immettere **adftutorial** nella casella **Nome** e fare clic su **OK**. 

1. Selezionare **adftutorial** nell'elenco di contenitori.


1. Tenere aperta la finestra **Contenitore** per **adftutorial**. Verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.


## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **+ Crea una risorsa** > **Analisi** > **Data factory**. 
  
   ![Creazione di una nuova data factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 

   Il nome della data factory deve essere *univoco a livello globale*. Se viene visualizzato il messaggio di errore seguente per il campo Nome, modificare il nome della data factory, ad esempio usando nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

   ![Nome della nuova data factory](./media/doc-common-process/name-not-available-error.png)
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
1. In **Gruppo di risorse** eseguire una di queste operazioni:
  
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
        
     Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati da Data Factory possono trovarsi in altre località/aree.
1. Selezionare **Create** (Crea).

1. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
  
     ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)
1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina **Attività iniziali** selezionare **Copia dati** per avviare lo strumento Copia dati. 

   ![Pagina delle attività iniziali](./media/doc-common-process/get-started-page.png)

1. Nella pagina **Proprietà** dello strumento Copia dati immettere **CopyFromOnPremSqlToAzureBlobPipeline** in **Nome attività**. Quindi selezionare **Avanti**. Lo strumento Copia dati crea una pipeline con il nome specificato per questo campo. 
  ![Nome attività](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Nella pagina **Archivio dati di origine** fare clic su **Crea nuova connessione**. 


1. In **Nuovo Servizio collegato** cercare **SQL Server** e quindi selezionare **Continua**. 

1. In **Nome** per il **nuovo servizio collegato (SQL Server)** immettere **SqlServerLinkedService**. Selezionare **+Nuovo** in **Connect via integration runtime** (Connetti tramite runtime di integrazione). È necessario creare un runtime di integrazione self-hosted, scaricarlo nel computer e registrarlo in Data Factory. Il runtime di integrazione self-hosted copia i dati tra l'ambiente locale e il cloud.


1. Nella finestra **Integration Runtime Setup** (Installazione runtime di integrazione) selezionare **Self-Hosted**. Quindi selezionare **Avanti**. 

   ![Creare il runtime di integrazione](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. Nella finestra di dialogo **Integration Runtime Setup** immettere **TutorialIntegrationRuntime** in **Nome**. Quindi selezionare **Avanti**. 


1. Nella finestra di dialogo **Integration Runtime Setup** selezionare **Click here to launch the express setup for this computer** (Fare clic qui per avviare l'installazione rapida per il computer). Questa azione installa il runtime di integrazione nel computer e lo registra in Data Factory. In alternativa è possibile usare l'opzione di installazione manuale per scaricare il file di installazione, eseguirlo e usare la chiave per registrare il runtime di integrazione. 

1. Eseguire l'applicazione scaricata. In questa finestra viene visualizzato lo stato dell'installazione rapida. 

    ![Stato dell'installazione rapida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Nella finestra di dialogo **New Linked Service (SQL Server)** (Nuovo servizio collegato (SQL Server)) verificare che nel campo Integration Runtime sia selezionato **TutorialIntegrationRuntime**. Seguire quindi questa procedura:

    a. In **Nome** immettere **SqlServerLinkedService**.

    b. In **Nome server** immettere il nome dell'istanza di SQL Server locale.

    c. In **Nome database** immettere il nome del database locale.

    d. In **Tipo di autenticazione** selezionare l'autenticazione appropriata.

    e. In **Nome utente** immettere il nome dell'utente con accesso all'istanza di SQL Server locale.

    f. Immettere la **password** dell'utente. 

    g. Testare la connessione e selezionare **Fine**.

      ![Runtime di integrazione selezionato](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Nella pagina **Archivio dati di origine** selezionare **Avanti**.

1. Nella pagina **Select tables from which to copy the data or use a custom query** (Selezionare le tabelle dalle quali copiare i dati oppure usare una query personalizzata) selezionare la tabella **[dbo].[emp]** nell'elenco e quindi **Avanti**. È possibile selezionare qualsiasi altra tabella a seconda del database.

1. Nella pagina **archivio dati di destinazione** selezionare **Crea nuova connessione**


1. In **Nuovo Servizio collegato** cercare e selezionare **BLOB di Azure**, quindi **Continua**. 

   ![Selezione dell'archivio BLOB](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Nella finestra di dialogo **Nuovo Servizio collegato (Archiviazione BLOB di Azure)** procedere come segue: 

   a. In **Nome** immettere **AzureStorageLinkedService**.

   b. In **Connect via integration runtime** (Connetti tramite runtime di integrazione) selezionare **TutorialIntegrationRuntime**

   c. In **Nome account di archiviazione** selezionare il proprio account di archiviazione nell'elenco a discesa. 

   d. Selezionare **Fine**.

1. Nella finestra di dialogo **Archivio dati di destinazione** assicurarsi che sia selezionata l'opzione **Archiviazione BLOB di Azure**. Quindi selezionare **Avanti**. 

1. Nella finestra di dialogo **Choose the output file or folder** (Scegliere il file o la cartella di output) immettere **adftutorial/fromonprem** in **Percorso cartella**. Come parte dei prerequisiti è stato creato il contenitore **adftutorial**. Se la cartella di output (in questo caso **fromonprem**) non esiste, Data Factory la crea automaticamente. È anche possibile usare il pulsante **Sfoglia** per passare all'archivio BLOB e ai relativi contenitori/cartelle. Se non si specifica alcun valore in **Nome file**, per impostazione predefinita viene usato il nome dell'origine (in questo caso **dbo.emp**).
           
   ![Scegliere il file o la cartella di output](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. Nella finestra di dialogo **File format settings** (Impostazioni formato file) selezionare **Avanti**. 

1. Nella finestra di dialogo **Impostazioni** selezionare **Avanti**. 

1. Nella finestra di dialogo **Riepilogo** esaminare i valori di tutte le impostazioni e selezionare **Avanti**. 

1. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline o l'attività creata.

   ![Pagina Distribuzione](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. Nella scheda **Monitoraggio** è possibile visualizzare lo stato della pipeline creata. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline ed eseguire di nuovo la pipeline. 
   
1. Selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni** per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Per tornare alla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline), selezionare **Pipeline Runs** in alto.

1. Assicurarsi che venga visualizzato il file di output nella cartella **fromonprem** del contenitore **adftutorial**. 


1. Selezionare la scheda **Modifica** a sinistra per passare alla modalità di modifica. Usando l'editor è possibile aggiornare i servizi collegati, i set di dati e le pipeline creati dallo strumento. Selezionare **Codice** per visualizzare il codice JSON associato all'entità aperta nell'editor. Per informazioni dettagliate sulla modifica di queste entità nell'interfaccia utente di Data Factory, vedere la [versione di questa esercitazione relativa al portale di Azure](tutorial-copy-data-portal.md).

   ![Scheda Modifica](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da un database di SQL Server locale a un archivio BLOB. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Per un elenco degli archivi dati supportati da Data Factory, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Per informazioni sulla copia di dati in blocco da un'origine a una destinazione, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
>[Copiare dati in blocco](tutorial-bulk-copy-portal.md)
