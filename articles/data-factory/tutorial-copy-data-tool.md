---
title: Copiare i dati tramite lo strumento Copia dati di Azure | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>Copiare dati da un BLOB di Azure al database SQL di Azure con lo strumento Copia dati
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](tutorial-copy-data-tool.md)

In questa esercitazione si usa il portale di Azure per creare una data factory. Viene quindi usato lo strumento Copia dati per creare una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL di Azure. 

> [!NOTE]
> - Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).
>
> - Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
* **Database SQL di Azure**. Usare il database come archivio dati **sink**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora il BLOB di Azure e il database SQL di Azure per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **inputEmp.txt** sul disco.

    ```
    John|Doe
    Jane|Doe
    ```

2. Usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per creare il contenitore **adfv2tutorial** e per caricare il file **inputEmp.txt** nel contenitore.

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

2. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'opzione **Consenti l'accesso a Servizi di Azure** sia abilitata per il server SQL di Azure in modo che il servizio Data Factory possa scrivere dati nel server. Per verificare e attivare l'impostazione, seguire questa procedura:

    1. Fare clic sull'hub **Altri servizi** a sinistra e selezionare **Server SQL**.
    2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**.
    3. Nella pagina **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso a Servizi Azure**.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Pagina Nuova data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre località/aree.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina delle attività iniziali fare clic sul riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** dello strumento Copia dati specificare **CopyFromBlobToSqlPipeline** per **Nome attività**, quindi fare clic su **Avanti**. L'interfaccia utente di Data Factory crea una pipeline con il nome specificato per l'attività. 

    ![Strumento Copia dati - Pagina Proprietà](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Nella pagina **Source data store** (Archivio dati di origine) selezionare **Archivio BLOB di Azure** e quindi fare clic su **Avanti**. I dati di origine si trovano in un archivio BLOB di Azure. 

    ![Pagina Archivio dati di origine](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Nella pagina **Specify the Azure Blob storage account** (Specificare l'account di archiviazione BLOB di Azure) seguire questa procedura: 
    1. Immettere **AzureStorageLinkedService** per il **nome connessione**.
    2. Selezionare il proprio **account di archiviazione** dall'elenco a discesa.
    3. Fare clic su **Avanti**. 

        ![Specificare l'account di archiviazione BLOB](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        Un servizio collegato collega un archivio dati o un ambiente di calcolo alla data factory. In questo caso viene creato un servizio collegato di archiviazione di Azure per collegare l'account di archiviazione di Azure all'archivio dati. Il servizio collegato ha le informazioni di connessione usate dal servizio Data Factory per connettersi all'archivio BLOB in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) che contiene i dati di origine. 
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) seguire questa procedura:
    
    1. Passare alla cartella **adfv2tutorial/input**.
    2. Selezionare **inputEmp.txt**
    3. Fare clic su **Scegli**. In alternativa fare doppio clic su **inputEmp.txt**. 
    4. Fare clic su **Avanti**. 

    ![Scegliere il file o la cartella di input](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. Nella pagina **File format settings** (Impostazioni di formato file) si noti che lo strumento rileva automaticamente i delimitatori di colonna e di riga, quindi fare clic su **Avanti**. È anche possibile visualizzare l'anteprima dei dati e lo schema dei dati di input in questa pagina. 

    ![Pagina di impostazioni del formato del file](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. Nella pagina **Destination data store** (Archivio dati di destinazione) selezionare **Azure SQL Database** (Database SQL di Azure) e quindi fare clic su **Avanti**. 

    ![Pagina dell'archivio dati di destinazione](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Nella pagina **Specify the Azure SQL database** (Specificare il database SQL di Azure) seguire questa procedura: 

    1. Specificare **AzureSqlDatabaseLinkedService** per il **nome connessione**. 
    2. Selezionare il server di Azure SQL per il **nome server**.
    3. Selezionare il database SQL di Azure per il **nome database**.
    4. Immettere il nome dell'utente per il **nome utente**.
    5. Immettere la password dell'utente per la **password**.
    6. Fare clic su **Avanti**. 

        ![Specificare il database SQL di Azure](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        È necessario associare un set di dati a un servizio collegato. Il servizio collegato ha la stringa di connessione usata dal servizio Data Factory per connettersi al database SQL di Azure in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) in cui vengono copiati i dati.
9.  Nella pagina **Mapping tabella** selezionare **[dbo].[emp]** e fare clic su **Avanti**. 

    ![Pagina Mapping tabella](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Nella pagina **Mapping dello schema** si noti che la prima e la seconda colonna del file di input sono mappate alle colonne **FirstName** e **LastName** della tabella **emp**. 

    ![Pagina Mapping dello schema](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Nella pagina **Impostazioni** fare clic su **Avanti**. 

    ![Pagina Impostazioni](./media/tutorial-copy-data-tool/settings-page.png)
12. Nella pagina **Riepilogo** esaminare le impostazioni e fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-copy-data-tool/summary-page.png)
13. Nella pagina **Distribuzione** fare clic su **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-copy-data-tool/deployment-page.png)
14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. Nella colonna **Azioni** sono disponibili i collegamenti per visualizzare i dettagli delle esecuzioni dell'attività e per eseguire di nuovo la pipeline. Fare clic su **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, fare clic sul collegamento **Dettagli** (icona degli occhiali) nella colonna **Azioni**. Per tornare alla visualizzazione delle esecuzioni di pipeline, fare clic sul collegamento **Pipeline** in alto. Per aggiornare la visualizzazione, fare clic su **Aggiorna**. 

    ![Monitorare le esecuzioni delle attività](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Fare clic sulla scheda **Modifica** a sinistra per passare alla modalità di modifica. L'editor consente di aggiornare i servizi collegati, i set di dati e le pipeline creati dallo strumento. Fare clic su **Codice** per visualizzare il codice JSON associato all'entità aperta nell'editor. Per informazioni dettagliate sulla modifica di queste entità nell'interfaccia utente di Data Factory, vedere [la versione del portale di Azure di questa esercitazione](tutorial-copy-data-portal.md).

    ![Scheda dell'editor](./media/tutorial-copy-data-tool/edit-tab.png)
17. Verificare che i dati siano inseriti nella tabella **emp** del database SQL di Azure. 

    ![Verificare l'output SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da un archivio BLOB di Azure a un database SQL di Azure. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-data-tool.md)