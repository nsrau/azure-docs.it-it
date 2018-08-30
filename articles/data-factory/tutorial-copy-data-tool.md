---
title: Copiare i dati usando lo strumento Copia dati di Azure | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per copiare i dati da un archivio BLOB di Azure a un database SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 1be4769a8a07ac5d4a968ed5aa15ed2e0a2b6db2
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43086827"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copiare dati da un archivio BLOB di Azure a un database SQL con lo strumento Copia dati
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](tutorial-copy-data-tool.md)

In questa esercitazione si usa il portale di Azure per creare una data factory. Viene quindi usato lo strumento Copia dati per creare una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL. 

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: usare un archivio BLOB come archivio dati di _origine_. Se non si ha un account di archiviazione di Azure, vedere le istruzioni riportate in [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).
* **Database SQL di Azure**: usare un database SQL come archivio dati _sink_. Se non si ha un database SQL, vedere le istruzioni in [Creare un database SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare l'archivio BLOB e il database SQL per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il **Blocco note**. Copiare il testo seguente e salvarlo in un file denominato **inputEmp.txt** sul disco:

    ```
    John|Doe
    Jane|Doe
    ```

1. Creare un contenitore denominato **adfv2tutorial** e caricare il file inputEmp.txt nel contenitore. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

1. Usare lo script SQL seguente per creare una tabella denominata **dbo.emp** nel database SQL:

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

1. Consentire ai servizi di Azure di accedere a SQL Server. Verificare che per il server che esegue SQL Server sia abilitata l'impostazione **Consenti l'accesso a Servizi di Azure**, che consente a Data Factory di scrivere dati nell'istanza di SQL Server. Per verificare e attivare l'impostazione, seguire questa procedura:

    a. A sinistra selezionare **Altri servizi** e quindi **SQL Server**.

    b. Selezionare il server e quindi **IMPOSTAZIONI** > **Firewall**.

    c. Nella pagina **Impostazioni del firewall** impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **SÌ**.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **+ Nuovo** > **Dati e analisi** > **Data factory**: 
   
   ![Creazione di una nuova data factory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
      
     ![Nuova data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:
   
   ![Messaggio di errore per nuova data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
1. Selezionare la **sottoscrizione** di Azure in cui creare la nuova data factory. 
1. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

1. In **Versione** selezionare la versione **V2**.
1. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre località e aree.
1. Selezionare **Aggiungi al dashboard**. 
1. Selezionare **Create**.
1. Il riquadro **Deploying Data Factory** (Distribuzione della data factory) nel dashboard mostra lo stato del processo.

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-copy-data-tool/deploying-data-factory.png)
1. Al termine della creazione verrà visualizzata la home page **Data factory**.
   
    ![Home page di Data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
1. Per avviare l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **Crea e monitora**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
1. Nella pagina **Proprietà** immettere **CopyFromBlobToSqlPipeline** in **Nome attività**. Quindi selezionare **Avanti**. L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato. 

    ![Pagina Proprietà](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
1. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    ![Nuovo servizio collegato di origine](./media/tutorial-copy-data-tool/new-source-linked-service.png)

    b. Selezionare **Archiviazione BLOB di Azure** dalla raccolta e quindi selezionare **Avanti**.

    ![Selezionare l'origine BLOB](./media/tutorial-copy-data-tool/select-blob-source.png)

    c. Nella pagina **Nuovo Servizio collegato** selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione** e quindi selezionare **Fine**.

    ![Configurare l'archiviazione di Azure](./media/tutorial-copy-data-tool/configure-azure-storage.png)

    d. Selezionare il servizio collegato appena creato come origine, quindi fare clic su **Avanti**.

    ![Selezionare il servizio collegato di origine](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:
    
    a. Fare clic su **Sfoglia** per passare alla cartella **adfv2tutorial/input**, selezionare il file **inputEmp.txt** e quindi fare clic su **Scegli**.

    ![Scegliere il file o la cartella di input](./media/tutorial-copy-data-tool/specify-source-path.png)

    b. Fare clic su **Avanti** per andare al passaggio successivo.

1. Nella pagina **File format settings** (Impostazioni di formato file) si noti che lo strumento rileva automaticamente i delimitatori di colonna e di riga. Selezionare **Avanti**. In questa pagina è anche possibile visualizzare l'anteprima dei dati e lo schema dei dati di input. 

    ![Impostazioni sul formato del file](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Nella pagina **archivio dati di destinazione** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    ![Nuovo servizio collegato sink](./media/tutorial-copy-data-tool/new-sink-linked-service.png)

    b. Selezionare **Archiviazione BLOB di Azure** dalla raccolta e quindi selezionare **Avanti**.

    ![Selezionare il database SQL di Azure](./media/tutorial-copy-data-tool/select-azure-sql-db.png)

    c. Nella pagina **Nuovo Servizio collegato**selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e la password, quindi selezionare **Fine**.    

    ![Configurare il database SQL di Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selezionare il servizio collegato appena creato come sink e quindi fare clic su **Avanti**.

    ![Selezionare il servizio collegato sink](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Nella pagina **Mapping tabella** selezionare la tabella **[dbo].[emp]** e quindi **Avanti**. 

    ![Mapping tabella](./media/tutorial-copy-data-tool/table-mapping.png)
1. Nella pagina **Mapping dello schema** si noti che la prima e la seconda colonna del file di input sono mappate alle colonne **FirstName** e **LastName** della tabella **emp**. Selezionare **Avanti**.

    ![Pagina Mapping dello schema](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Nella pagina **Impostazioni** selezionare **Avanti**. 
1. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-copy-data-tool/summary-page.png)
1. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-copy-data-tool/deployment-page.png)
1. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-copy-data-tool/pipeline-monitoring.png)
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Per tornare alla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline), selezionare il collegamento **Pipeline** in alto. Per aggiornare la visualizzazione, selezionare **Aggiorna**. 

    ![Monitorare le esecuzioni delle attività](./media/tutorial-copy-data-tool/activity-monitoring.png)

    ![Copiare dettagli dell'attività](./media/tutorial-copy-data-tool/copy-execution-details.png)

1. Verificare che i dati siano inseriti nella tabella **emp** del database SQL.

    ![Verificare l'output SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

1. Selezionare la scheda **Autore** a sinistra per passare alla modalità di modifica. L'editor consente di aggiornare i servizi collegati, i set di dati e le pipeline che sono stati creati tramite lo strumento. Per informazioni dettagliate sulla modifica di queste entità nell'interfaccia utente di Data Factory, vedere [la versione del portale di Azure di questa esercitazione](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da un archivio BLOB a un database SQL. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-data-tool.md)
