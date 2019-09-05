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
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 9a2ad8070c0406446f53c1bcaa6d341cdca0bb2a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140721"
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
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
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

1. Creare un contenitore denominato **adftutorial** nell'archivio BLOB. Creare una cartella denominata **input** in questo contenitore. Caricare quindi il file **emp.txt** nella cartella **input**. Usare il portale di Azure o strumenti come [Azure Storage Explorer](https://storageexplorer.com/) per eseguire queste attività.

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

1. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'opzione **Consenti l'accesso a Servizi di Azure** sia impostata su **SÌ** per SQL Server in modo che Data Factory possa scrivere dati in SQL Server. Per verificare e attivare l'impostazione, passare a Server di Azure SQL > Panoramica > Imposta firewall server > impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **SÌ**.

## <a name="create-a-data-factory"></a>Creare una data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory. 

1. Aprire **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Nel menu a sinistra selezionare **Crea una risorsa** > **Analytics** > **Data Factory**: 
  
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).
        
     ![Nuova data factory](./media/doc-common-process/name-not-available-error.png)
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
5. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md). 
6. In **Versione** selezionare **V2**.
7. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre aree.
8. Selezionare **Create** (Crea). 
9. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
10. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.


## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio si crea una pipeline con un'attività di copia nella data factory. L'attività copia i dati dall'archivio BLOB al database SQL. Nell'[Esercitazione introduttiva](quickstart-create-data-factory-portal.md) è stata creata una pipeline con questa procedura:

1. Creazione del servizio collegato. 
1. Creazione dei set di dati di input e di output.
1. Creare una pipeline.

In questa esercitazione si crea inizialmente la pipeline, quindi si creano i servizi collegati e i set di dati quando risultano necessari per la configurazione della pipeline. 

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)
1. Nella scheda **Generale** della pipeline immettere **CopyPipeline** come **nome** della pipeline.

1. Nella casella degli strumenti **Attività** espandere la categoria **Move & Transform** (Sposta e trasforma) e trascinare l'attività **Copia dati** dalla casella degli strumenti all'area di progettazione della pipeline. Specificare **CopyFromBlobToSql** per **Nome**.

    ![Attività di copia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurare l'origine

1. Passare alla scheda **Origine**. Selezionare **+ Nuovo** per creare un set di dati di origine. 

1. Nella finestra di dialogo **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi **Continua**. I dati di origine si trovano in un archivio BLOB, quindi come set di dati di origine si seleziona **Archiviazione BLOB di Azure**. 

1. Nella finestra di dialogo **Select Format** (Seleziona formato) scegliere il tipo di formato dei dati e quindi fare clic su **Continua**.

    ![Tipo di formato dati](./media/doc-common-process/select-data-format.png)

1. Nella finestra di dialogo **Set Properties** (Imposta proprietà) immettere **SourceBlobDataset** come nome. Selezionare **+ Nuovo** accanto alla casella di testo **Servizio collegato**. 
    
1. Nella finestra **New Linked Service (Azure Blob Storage)** (Nuovo servizio collegato - Archivio BLOB di Azure) immettere **AzureStorageLinkedService** come nome e selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione**. Testare la connessione e quindi selezionare **Fine** per distribuire il servizio collegato.

1. Al termine della creazione del servizio collegato verrà visualizzata di nuovo la pagina **Set properties** (Imposta proprietà). Selezionare **Sfoglia** accanto a **Percorso file**.

1. Passare alla cartella **adftutorial/input** e selezionare il file **emp.txt** e quindi **Fine**.

1. Si passerà automaticamente alla pagina della pipeline. Nella scheda **Origine** verificare che sia selezionato il set di dati **SourceBlobDataset**. Per visualizzare l'anteprima dei dati in questa pagina, selezionare **Anteprima dati**. 
    
    ![Set di dati di origine](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurare il sink

1. Passare alla scheda **Sink** e selezionare **+ Nuovo** per creare un set di dati sink. 

1. Nella finestra di dialogo **Nuovo set di dati** immettere "SQL" nella casella di ricerca per filtrare i connettori e selezionare **Database SQL di Azure** e quindi **Continua**. In questa esercitazione si copiano i dati in un database SQL. 

1. Nella finestra di dialogo **Set Properties** (Imposta proprietà) immettere **OutputSqlDataset** come nome. Selezionare **+ Nuovo** accanto alla casella di testo **Servizio collegato**. È necessario associare un set di dati a un servizio collegato. Il servizio collegato contiene la stringa di connessione usata da Data Factory per connettersi al database SQL in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) in cui vengono copiati i dati. 
      
1. Nella finestra di dialogo **New Linked Service (Azure SQL Database)** (Nuovo servizio collegato - Database SQL di Azure) seguire questa procedura: 

    a. In **Nome** immettere **AzureSqlDatabaseLinkedService**.

    b. In **Nome server** selezionare l'istanza di SQL Server.

    c. In **Nome database** selezionare il database SQL.

    d. In **Nome utente** immettere il nome dell'utente.

    e. In **Password** immettere la password dell'utente.

    f. Selezionare **Test connessione** per testare la connessione.

    g. Selezionare **Fine** per distribuire il servizio collegato. 
    
    ![Salvare il nuovo servizio collegato](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Si passerà automaticamente alla finestra di dialogo **Set Properties** (Imposta proprietà). In **Tabella** selezionare **[dbo].[emp]** . Selezionare quindi **Fine**.

1. Passare alla scheda con la pipeline e verificare che in **Sink Dataset** (Set di dati sink) sia selezionato **OutputSqlDataset**.

    ![Scheda con la pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Facoltativamente, è possibile eseguire il mapping dello schema dell'origine allo schema corrispondente della destinazione seguendo le indicazioni riportate in [Mapping dello schema nell'attività di copia](copy-activity-schema-and-type-mapping.md).
    
## <a name="validate-the-pipeline"></a>Convalidare la pipeline
Per convalidare la pipeline, selezionare **Convalida** dalla barra degli strumenti.
 
È possibile visualizzare il codice JSON associato alla pipeline facendo clic su **Codice** in alto a destra.

## <a name="debug-and-publish-the-pipeline"></a>Eseguire il debug della pipeline e pubblicarla
È possibile eseguire il debug di una pipeline prima di pubblicare gli artefatti (servizi collegati, set di dati e pipeline) in Data Factory o nel proprio repository GIT di Azure Repos. 

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 

1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.

1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fare clic su **Mostra notifiche** in alto a destra (pulsante con il campanello). 

## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline
In questo passaggio si attiva manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  

1. Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline Runs** (Esecuzioni di pipeline) in alto per tornare alla visualizzazione delle esecuzioni. Per aggiornare la visualizzazione, selezionare **Aggiorna**.

    ![Monitorare le esecuzioni delle attività](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Verificare l'aggiunta di altre due righe alla tabella **emp** nel database SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Attivare la pipeline in base a una pianificazione
In questa pianificazione si crea un trigger di pianificazione per la pipeline. Il trigger esegue la pipeline in base alla pianificazione specificata, ad esempio ogni ora oppure ogni giorno. In questo caso si imposta il trigger per l'esecuzione ogni minuto fino al valore di data e ora di fine specificato. 

1. Passare alla scheda **Autore** a sinistra sopra la scheda Monitoraggio. 

1. Passare alla pipeline, fare clic su **Aggiungi trigger** sulla barra degli strumenti e selezionare **New/Edit** (Nuovo/Modifica). 

1. Nella finestra di dialogo **Add Triggers** (Aggiungi trigger) selezionare **+ Nuovo** nell'area **Choose trigger** (Scegli trigger).

1. Nella finestra **Nuovo trigger** seguire questa procedura: 

    a. In **Nome** immettere **RunEveryMinute**.

    b. In **Fine** selezionare **On Date** (In data).

    c. In **End On** (Data fine) selezionare l'elenco a discesa.

    d. Selezionare la **data odierna** come opzione. Per impostazione predefinita, il giorno di fine viene impostato sul giorno successivo.

    e. Modificare la parte relativa all'**ora di fine** in modo che corrisponda ad alcuni minuti dopo la data/ora corrente. Il trigger viene attivato solo dopo la pubblicazione delle modifiche. Se lo si imposta a pochi minuti di distanza e non viene pubblicato in quel lasso di tempo, il trigger non verrà eseguito.

    f. Selezionare **Applica**. 

    g. Per l'opzione **Attivato** selezionare **Sì**. 

    h. Selezionare **Avanti**.

    ![Pulsante Attivato](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Dato che a ogni esecuzione della pipeline è associato un costo, impostare correttamente la data di fine. 
1. Nella pagina **Trigger Run Parameters** (Parametri esecuzione trigger) esaminare l'avviso e quindi selezionare **Fine**. La pipeline di questo esempio non accetta alcun parametro. 

1. Fare clic su **Pubblica tutto** per pubblicare la modifica. 

1. Passare alla scheda **Monitoraggio** a sinistra per visualizzare le esecuzioni della pipeline attivate. 

    ![Esecuzioni di pipeline attivate](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. Per passare dalla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline) alla visualizzazione **Trigger Runs** (Esecuzioni del trigger), selezionare **Trigger Runs** (Esecuzioni del trigger) nella parte superiore della finestra.

1. Verrà visualizzato l'elenco di esecuzioni di trigger. 

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
