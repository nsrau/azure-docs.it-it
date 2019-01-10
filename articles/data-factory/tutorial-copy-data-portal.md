---
title: Usare il portale di Azure per creare una pipeline di Data Factory | Microsoft Docs
description: Questa esercitazione offre istruzioni dettagliate per l'uso del portale di Azure per creare una data factory con una pipeline. La pipeline usa l'attività di copia per copiare i dati da un archivio BLOB di Azure a un database SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 5c7e6a4da9880677fbc4aad76b820ba596058bb6
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025250"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copiare dati da un archivio BLOB di Azure a un database SQL con Azure Data Factory
In questa esercitazione viene creata una data factory con l'interfaccia utente di Azure Data Factory. La pipeline in questa data factory copia i dati da un archivio BLOB di Azure a un database SQL. Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati basato su file a un archivio dati relazionale. Per un elenco degli archivi dati supportati come origini e sink, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Se non si ha familiarità con Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività di copia.
> * Eseguire test della pipeline.
> * Attivare manualmente la pipeline.
> * Attivare la pipeline in base a una pianificazione.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Come archivio dati di *origine* si usa un archivio BLOB. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-quickstart-create-account.md) per informazioni su come crearne uno.
* **Database SQL di Azure**. Il database viene usato come archivio dati *sink*. Se non si ha un database SQL, vedere la procedura per crearne uno in [Creare un database SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora l'archivio BLOB di Azure e il database SQL per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **emp.txt** sul disco:

    ```
    John,Doe
    Jane,Doe
    ```

1. Creare un contenitore denominato **adftutorial** nell'archivio BLOB. Creare una cartella denominata **input** in questo contenitore. Caricare quindi il file **emp.txt** nella cartella **input**. Usare il portale di Azure o strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per eseguire queste attività.

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

1. Usare lo script SQL seguente per creare la tabella **dbo.emp** nel database SQL:

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

1. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'opzione **Consenti l'accesso a Servizi di Azure** sia impostata su **SÌ** per SQL Server in modo che Data Factory possa scrivere dati in SQL Server. Per verificare e attivare l'impostazione, seguire questa procedura:

    a. A sinistra selezionare **Altri servizi** > **SQL Server**.

    b. Selezionare il server e quindi **Firewall** in **IMPOSTAZIONI**.

    c. Nella pagina **Impostazioni del firewall** selezionare **SÌ** per **Consenti l'accesso a Servizi di Azure**.

## <a name="create-a-data-factory"></a>Creare una data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory. 

1. Aprire il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Nuovo** > **Dati e analisi** > **Data factory**. 
  
   ![Creazione di una nuova data factory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
      
     ![Nuova data factory](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato il messaggio di errore seguente per il campo Nome, modificare il nome della data factory, ad esempio usando nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).
  
   ![Messaggio di errore](./media/tutorial-copy-data-portal/name-not-available-error.png)
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
1. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md). 
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre aree.
1. Selezionare **Aggiungi al dashboard**. 
1. Selezionare **Crea**. 
1. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory): 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-copy-data-portal/deploying-data-factory.png)
1. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
    ![Home page di Data factory](./media/tutorial-copy-data-portal/data-factory-home-page.png)
1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio si crea una pipeline con un'attività di copia nella data factory. L'attività copia i dati dall'archivio BLOB al database SQL. Nell'[Esercitazione introduttiva](quickstart-create-data-factory-portal.md) è stata creata una pipeline con questa procedura:

1. Creazione del servizio collegato. 
1. Creazione dei set di dati di input e di output.
1. Creare una pipeline.

In questa esercitazione si crea inizialmente la pipeline, quindi si creano i servizi collegati e i set di dati quando risultano necessari per la configurazione della pipeline. 

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Creare una pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
1. Nella scheda **Generale** della pipeline immettere **CopyPipeline** come **nome** della pipeline.

1. Nella casella degli strumenti **Attività** espandere la categoria **Flusso di dati** e trascinare l'attività **Copia** dalla casella degli strumenti all'area di progettazione della pipeline. Specificare **CopyFromBlobToSql** per **Nome**.

    ![Attività di copia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurare l'origine

1. Passare alla scheda **Origine**. Selezionare **+ Nuovo** per creare un set di dati di origine. 

1. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi **Fine**. I dati di origine si trovano in un archivio BLOB, quindi come set di dati di origine si seleziona **Archiviazione BLOB di Azure**. 

    ![Selezione dell'archiviazione](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

1. Verrà aperta una nuova scheda per il set di dati BLOB. Nella scheda **Generale** nella parte inferiore della finestra **Proprietà** immettere **SourceBlobDataset** in **Nome**.

    ![Nome del set di dati](./media/tutorial-copy-data-portal/dataset-name.png)

1. Passare alla scheda **Connessione** della finestra **Proprietà**. Selezionare **+ Nuovo** accanto alla casella di testo **Servizio collegato**. 

    ![Pulsante Nuovo per il servizio collegato](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

1. Nella finestra **New Linked Service** (Nuovo servizio collegato) immettere **AzureStorageLinkedService** come nome, selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione**, quindi selezionare **Salva** per distribuire il servizio collegato.

    ![Nuovo servizio collegato](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

1. Dopo aver creato il servizio collegato, si torna alle impostazioni del set di dati. Selezionare **Sfoglia** accanto a **Percorso file**.

    ![Pulsante Sfoglia per il percorso del file](./media/tutorial-copy-data-portal/file-browse-button.png)

1. Passare alla cartella **adftutorial/input** e selezionare il file **emp.txt** e quindi **Fine**. 

    ![Selezionare il file di input](./media/tutorial-copy-data-portal/select-input-file.png)

1. Verificare che **Formato file** sia impostato su **Text format** (Formato testo) e che l'opzione **Delimitatore di colonna** sia impostata su **Virgola (`,`)**. Se il file di origine usa delimitatori di colonna e di riga diversi, è possibile selezionare **Detect Text Format** (Rileva formato testo) per **Formato file**. Lo strumento Copia dati rileva automaticamente il formato del file e i delimitatori. È comunque possibile sostituire questi valori. Per visualizzare l'anteprima dei dati in questa pagina, selezionare **Anteprima dati**.

    ![Rilevare il formato del testo](./media/tutorial-copy-data-portal/detect-text-format.png)

1. Passare alla scheda **Schema** nella finestra **Proprietà** e selezionare **Importa schema**. Si noti che l'applicazione ha rilevato due colonne nel file di origine. Lo schema viene importato per poter eseguire il mapping delle colonne dall'archivio dati di origine all'archivio dati sink. Se non è necessario eseguire il mapping delle colonne, questo passaggio può essere ignorato. Per questa esercitazione importare lo schema.

    ![Rilevare lo schema di origine](./media/tutorial-copy-data-portal/detect-source-schema.png)  

1. Tornare ora alla pipeline -> scheda **Origine** e verificare che **SourceBlobDataset** sia selezionato. Per visualizzare l'anteprima dei dati in questa pagina, selezionare **Anteprima dati**. 
    
    ![Set di dati di origine](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurare il sink

1. Passare alla scheda **Sink** e selezionare **+ Nuovo** per creare un set di dati sink. 

    ![Set di dati sink](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
1. Nella finestra **Nuovo set di dati** immettere "SQL" nella casella di ricerca per filtrare i connettori, quindi selezionare **Database SQL di Azure** e infine selezionare **Fine**. In questa esercitazione si copiano i dati in un database SQL. 

    ![Selezione del database SQL](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
1. Nella scheda **Generale** della finestra **Proprietà** immettere **OutputSqlDataset** in **Nome**. 
    
    ![Nome del set di dati di output](./media/tutorial-copy-data-portal/output-dataset-name.png)
1. Passare alla scheda **Connessione** e selezionare **+ Nuovo** accanto a **Servizio collegato**. È necessario associare un set di dati a un servizio collegato. Il servizio collegato contiene la stringa di connessione usata da Data Factory per connettersi al database SQL in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) in cui vengono copiati i dati. 
    
    ![Servizio collegato](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
1. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    a. In **Nome** immettere **AzureSqlDatabaseLinkedService**.

    b. In **Nome server** selezionare l'istanza di SQL Server.

    c. In **Nome database** selezionare il database SQL.

    d. In **Nome utente** immettere il nome dell'utente.

    e. In **Password** immettere la password dell'utente.

    f. Selezionare **Test connessione** per testare la connessione.

    g. Selezionare **Salva** per salvare il servizio collegato. 
    
    ![Salvare il nuovo servizio collegato](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. In **Tabella** selezionare **[dbo].[emp]**. 

    ![Tabella](./media/tutorial-copy-data-portal/select-emp-table.png)
1. Passare alla scheda **Schema** e selezionare **Importa schema**. 

    ![Selezionare Importa schema](./media/tutorial-copy-data-portal/import-destination-schema.png)
1. Selezionare la colonna **ID** e quindi **Elimina**. La colonna **ID** è una colonna Identity del database SQL, quindi non è necessario che l'attività di copia inserisca dati in questa colonna.

    ![Eliminare la colonna ID](./media/tutorial-copy-data-portal/delete-id-column.png)
1. Passare alla scheda con la pipeline e verificare che in **Sink Dataset** (Set di dati sink) sia selezionato **OutputSqlDataset**.

    ![Scheda con la pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="configure-mapping"></a>Configurare il mapping

Passare alla scheda **Mapping** nella parte inferiore della finestra **Proprietà** e selezionare **Import Schemas** (Importa schemi). Si noti che la prima e la seconda colonna del file di origine sono mappate a **FirstName** e **LastName** nel database SQL.

![Schemi di mappatura](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>Convalidare la pipeline
Per convalidare la pipeline, selezionare **Convalida** dalla barra degli strumenti.
 
È possibile visualizzare il codice JSON associato alla pipeline facendo clic su **Codice** in alto a destra.

## <a name="debug-and-publish-the-pipeline"></a>Eseguire il debug della pipeline e pubblicarla
È possibile eseguire il debug di una pipeline prima di pubblicare gli artefatti (servizi collegati, set di dati e pipeline) in Data Factory o nel proprio repository GIT di Azure Repos. 

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 

1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.

    ![Pubblica](./media/tutorial-copy-data-portal/publish-button.png)

1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fare clic su **Mostra notifiche** in alto a destra (pulsante con il campanello). 

## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline
In questo passaggio si attiva manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Trigger** sulla barra degli strumenti e quindi selezionare **Trigger Now** (Attiva adesso). Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline** in alto per tornare alla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline). Per aggiornare la visualizzazione, selezionare **Aggiorna**.

    ![Monitorare le esecuzioni delle attività](./media/tutorial-copy-data-portal/view-activity-runs.png)
1. Verificare l'aggiunta di altre due righe alla tabella **emp** nel database SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Attivare la pipeline in base a una pianificazione
In questa pianificazione si crea un trigger di pianificazione per la pipeline. Il trigger esegue la pipeline in base alla pianificazione specificata, ad esempio ogni ora oppure ogni giorno. In questo esempio, il trigger viene impostato per l'esecuzione ogni minuto fino al valore di data e ora di fine specificato. 

1. Passare alla scheda **Autore** a sinistra sopra la scheda Monitoraggio. 

1. Accedere alla pipeline, fare clic su **Trigger** sulla barra degli strumenti e selezionare **New/Edit** (Nuovo/Modifica). 

1. Nella finestra **Add Triggers** (Aggiungi trigger) selezionare **Choose trigger** (Scegli trigger) e quindi **+ Nuovo**. 

    ![New button](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
1. Nella finestra **Nuovo trigger** seguire questa procedura: 

    a. In **Nome** immettere **RunEveryMinute**.

    b. In **Fine** selezionare **On Date** (In data).

    c. In **End On** (Data fine) selezionare l'elenco a discesa.

    d. Selezionare la **data odierna** come opzione. Per impostazione predefinita, il giorno di fine viene impostato sul giorno successivo.

    e. Modificare il campo relativo ai **minuti** in modo che il valore sia successivo di alcuni minuti rispetto alla data/ora corrente. Il trigger viene attivato solo dopo la pubblicazione delle modifiche. Se lo si imposta a pochissimi minuti di distanza e non viene pubblicato in quel lasso di tempo, il trigger non verrà eseguito.

    f. Selezionare **Applica**. 

    ![Proprietà del trigger](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Selezionare l'opzione **Attivato**. È possibile disattivare l'opzione e attivarla in un secondo momento tramite questa casella di controllo.

    h. Selezionare **Avanti**.

    ![Pulsante Attivato](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Dato che a ogni esecuzione della pipeline è associato un costo, impostare correttamente la data di fine. 
1. Nella pagina **Trigger Run Parameters** (Parametri esecuzione trigger) esaminare l'avviso e quindi selezionare **Fine**. La pipeline di questo esempio non accetta alcun parametro. 

    ![Parametri di esecuzione del trigger](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

1. Fare clic su **Pubblica tutto** per pubblicare la modifica. 

1. Passare alla scheda **Monitoraggio** a sinistra per visualizzare le esecuzioni della pipeline attivate. 

    ![Esecuzioni di pipeline attivate](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
1. Per passare dalla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline) alla visualizzazione **Trigger Runs** (Esecuzioni del trigger), selezionare **Pipeline Runs** (Esecuzioni di pipeline) e quindi **Trigger Runs** (Esecuzioni del trigger).
    
    ![Esecuzioni di trigger](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
1. Verrà visualizzato l'elenco di esecuzioni di trigger. 

    ![Elenco delle esecuzioni di trigger](./media/tutorial-copy-data-portal/trigger-runs-list.png)
1. Verificare che vengano inserite due righe per ogni minuto (per ogni esecuzione di pipeline) nella tabella **emp** fino alla data e ora di fine specificata. 

## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da una posizione a un'altra in un archivio BLOB. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività di copia.
> * Eseguire test della pipeline.
> * Attivare manualmente la pipeline.
> * Attivare la pipeline in base a una pianificazione.
> * Monitorare le esecuzioni di pipeline e attività.


Passare all'esercitazione successiva per informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-portal.md)
