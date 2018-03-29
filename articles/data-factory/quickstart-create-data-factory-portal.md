---
title: Creare una data factory di Azure tramite l'interfaccia utente di Azure Data Factory | Microsoft Docs
description: Creare una data factory con una pipeline che copia i dati da una posizione nell'archivio BLOB di Azure a un'altra posizione.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 02/01/2018
ms.author: jingwang
ms.openlocfilehash: ea70d6053f12c33d9c3ff4c69f22a41130c783e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Creare una data factory usando l'interfaccia utente di Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](quickstart-create-data-factory-portal.md)

La guida introduttiva descrive come usare l'interfaccia utente di Azure Data Factory per creare e monitorare una data factory. La pipeline creata in questa data factory *copia* dati da una cartella a un'altra nell'archivio BLOB di Azure. Per un'esercitazione su come *trasformare* i dati usando Azure Data Factory, vedere [Esercitazione: Trasformare dati usando Spark](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](data-factory-introduction.md) prima di seguire la guida introduttiva. 
>
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio con disponibilità generale, vedere l'[esercitazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Questo video contiene informazioni utili sull'interfaccia utente di Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Accedere al [portale di Azure](https://portal.azure.com). 
3. Scegliere **Nuovo** dal menu a sinistra, selezionare **Dati e analisi** e quindi selezionare **Data Factory**. 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
   ![Pagina "Nuova data factory"](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, **&lt;nomeutente&gt;ADFTutorialDataFactory**, e provare di nuovo a crearla. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
   ![Errore quando un nome non è disponibile](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
4. In **Gruppo di risorse** eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
   Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Per **Versione** selezionare **V2 (anteprima)**.
5. Per **Località** selezionare la località per la data factory.

   L'elenco visualizza solo le località supportate da Data Factory. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio, HDInsight) usati da Data Factory possono trovarsi in altre località.
6. Selezionare **Aggiungi al dashboard**.     
7. Selezionare **Create**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory): 

   ![Riquadro "Deploying data factory" (Distribuzione della data factory)](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Azure Data Factory in una scheda separata.
   
   ![Home page della data factory, con il riquadro "Crea e monitora"](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra. 

    ![Pagina "Attività iniziali"](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Creare un servizio collegato
In questa procedura viene creato un servizio collegato per collegare l'account di archiviazione di Azure alla data factory. Il servizio collegato ha le informazioni di connessione usate dal servizio Data Factory in fase di esecuzione per la connessione.

1. Selezionare **Connessioni** e quindi il pulsante **Nuovo** sulla barra degli strumenti. 

   ![Pulsanti per la creazione di una nuova connessione](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
2. Nella pagina **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e quindi **Continua**. 

   ![Selezione del riquadro "Archiviazione BLOB di Azure"](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
3. Completare questi passaggi: 

   a. Per **Nome** immettere **AzureStorageLinkedService**.

   b. Per **Nome account di archiviazione** selezionare il nome dell'account di archiviazione di Azure.

   c. Selezionare **Connessione di test** per confermare che il servizio Data Factory possa connettersi all'account di archiviazione. 

   d. Selezionare **Salva** per salvare il servizio collegato. 

   ![Impostazioni del servizio collegato Archiviazione di Azure](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
4. Assicurarsi che **AzureStorageLinkedService** sia visualizzato nell'elenco di servizi collegati. 

   ![Servizio collegato di archiviazione di Azure nell'elenco](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Creare set di dati
In questa procedura vengono creati due set di dati, **InputDataset** e **OutputDataset**. I set di dati sono di tipo **AzureBlob**. Fanno riferimento al servizio collegato Archiviazione di Azure creato nella sezione precedente. 

Il set di dati di input rappresenta i dati di origini nella cartella di input. Nella definizione del set di dati di input specificare il contenitore BLOB (**adftutorial**), la cartella (**input**) e il file (**emp.txt**) che includono i dati di origine. 

Il set di dati di output rappresenta i dati copiati nella destinazione. Nella definizione del set di dati di output specificare il contenitore BLOB (**adftutorial**), la cartella (**output**) e il file in cui vengono copiati i dati. A ogni esecuzione di una pipeline è associato un ID univoco. È possibile accedere a questo ID usando la variabile di sistema **RunId**. Il nome del file di output viene valutato dinamicamente in base all'ID dell'esecuzione della pipeline.   

Nelle impostazioni del servizio collegato è stato specificato l'account di archiviazione di Azure che contiene i dati di origine. Nelle impostazioni del set di dati di origine specificare la posizione esatta in cui si trovano i dati, ovvero contenitore BLOB, cartella e file. Nelle impostazioni del set di dati di sink specificare la posizione in cui vengono copiati i dati, ovvero contenitore BLOB, cartella e file. 
 
1. Selezionare il pulsante **+** (segno più) e quindi selezionare **Set di dati**.

   ![Menu per la creazione di un set di dati](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. Nella pagina **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi selezionare **Fine**. 

   ![Selezione di "Archiviazione BLOB di Azure"](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. Nella finestra **Proprietà** per il set di dati immettere **InputDataset** per **Nome**. 

   ![Impostazioni generali del set di dati](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Passare alla scheda **Connessione** e completare questa procedura: 

   a. Per **Servizio collegato** selezionare **AzureStorageLinkedService**.

   b. Per **Percorso file** selezionare il pulsante **Sfoglia**.

      ![Scheda "Connessione" e pulsante "Sfoglia"](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. Nella finestra **Choose a file or folder** (Scegliere un file o una cartella) passare alla cartella di **input** nel contenitore **adftutorial**, selezionare il file **emp.txt** e quindi selezionare **Fine**.

      ![Sfogliare per selezionare il file di input](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
   d. (Facoltativo) Selezionare **Anteprima dati** per visualizzare l'anteprima dei dati nel file emp.txt.     
5. Ripetere la procedura per creare il set di dati di output:  

   a. Selezionare il pulsante **+** (segno più) e quindi selezionare **Set di dati**.

   b. Nella pagina **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi selezionare **Fine**.

   c. Specificare **OutputDataset** per il nome.

   d. Immettere **adftutorial/output** per la cartella. Se la cartella **output** non esiste, l'attività di copia la crea in fase di esecuzione.

   e. Immettere `@CONCAT(pipeline().RunId, '.txt')` per il nome del file. 
   
      Ogni volta che si esegue una pipeline, l'esecuzione della pipeline è associata a un ID univoco. L'espressione concatena l'ID di esecuzione della pipeline con l'estensione **.txt** per valutare il nome del file di output. Per l'elenco delle variabili di sistema e delle espressioni supportate, vedere [Variabili di sistema](control-flow-system-variables.md) e [Linguaggio delle espressioni](control-flow-expression-language-functions.md).

   ![Impostazioni del set di dati di output](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Creare una pipeline 
In questa procedura viene creata e convalidata una pipeline con un'attività di copia che usa i set di dati di input e di output. Con l'attività di copia i dati vengono copiati dal file specificato nelle impostazioni del set di dati di input al file specificato nelle impostazioni del set di dati di output. Se il set di dati di input specifica solo una cartella, non il nome file, con l'attività di copia tutti i file della cartella di origine vengono copiati nella destinazione. 

1. Selezionare il pulsante **+** (segno più) e quindi selezionare **Pipeline**. 

   ![Menu per la creazione di una nuova pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. Nella finestra **Proprietà** specificare **CopyPipeline** per **Nome**. 

   ![Impostazioni generali della pipeline](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. Nella casella degli strumenti **Attività** espandere **Flusso di dati**. Trascinare l'attività **Copia** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. È anche possibile eseguire una ricerca di attività nella casella degli strumenti **Attività**. Specificare **CopyFromBlobToBlob** per **Nome**.

   ![Impostazioni generali dell'attività Copia](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Passare alla scheda **Origine** nelle impostazioni dell'attività Copia e selezionare **InputDataset** per **Source Dataset** (Set di dati di origine).

   ![Impostazioni dell'origine dell'attività Copia](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Passare alla scheda **Sink** nelle impostazioni dell'attività Copia e selezionare **OutputDataset** per **Sink Dataset** (Set di dati sink).

   ![Impostazioni del sink dell'attività Copia](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Selezionare **Convalida** per convalidare le impostazioni della pipeline. Assicurarsi che la pipeline sia stata convalidata correttamente. Per chiudere l'output della convalida, selezionare il pulsante **>>** (freccia destra). 

   ![Convalidare la pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Eseguire test della pipeline
In questo passaggio vengono eseguiti test della pipeline prima della distribuzione in Data Factory. 

1. Sulla barra degli strumenti per la pipeline selezionare **Esecuzione dei test**. 
    
   ![Esecuzioni dei test della pipeline](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Assicurarsi che sia visualizzato lo stato dell'esecuzione della pipeline nella scheda **Output** delle impostazioni della pipeline. 

   ![Output dell'esecuzione dei test](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Assicurarsi che venga visualizzato un file di output nella cartella **output** del contenitore **adftutorial**. Se la cartella output non esiste, il servizio Data Factory la crea automaticamente. 
    
   ![Verificare l'output](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline
In questa procedura vengono distribuite entità (servizi collegati, set di dati, pipeline) in Azure Data Factory. Viene quindi attivata manualmente un'esecuzione della pipeline. È anche possibile pubblicare entità nel proprio repository Git per Visual Studio Team Services. Questa procedura è illustrata in un'[altra esercitazione](tutorial-copy-data-portal.md?#configure-code-repository).

1. Prima di attivare una pipeline, è necessario pubblicare entità in Data Factory. Per pubblicare, selezionare **Pubblica tutti** nel riquadro a sinistra. 

   ![Pulsante Publish](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Per attivare manualmente la pipeline, selezionare **Trigger** sulla barra degli strumenti e quindi selezionare **Trigger Now** (Attiva adesso). 
    
   ![Comando "Trigger Now" (Attiva adesso)](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Usare il pulsante **Aggiorna** per aggiornare l'elenco.

   ![Scheda per il monitoraggio delle esecuzioni della pipeline, con il pulsante "Aggiorna"](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) in **Azioni**. In questa pagina viene visualizzato lo stato dell'esecuzione dell'attività Copia. 

   ![Esecuzioni delle attività della pipeline](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) nella colonna **Azioni**. Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività Copia](copy-activity-overview.md). 

   ![Dettagli dell'operazione di copia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Assicurarsi che un nuovo file sia visualizzato nella cartella **output**. 
5. È possibile tornare alla visualizzazione **Pipeline Runs** (Esecuzioni di pipeline) dalla visualizzazione **Esecuzioni attività** selezionando il collegamento **Pipeline**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Attivare la pipeline in base a una pianificazione
La procedura è facoltativa in questa esercitazione. È possibile creare un *trigger di pianificazione* per pianificare l'esecuzione periodica (ogni ora, ogni giorno e così via) della pipeline. In questa procedura viene creato un trigger per l'esecuzione ogni minuto fino alla data e ora di fine specificate. 

1. Passare alla scheda **Modifica**. 

   ![Pulsante Modifica](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Scegliere **Trigger** dal menu e quindi selezionare **New/Edit** (Nuovo/Modifica). 

   ![Menu per il nuovo trigger](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. Nella pagina **Add Triggers** (Aggiungi trigger) selezionare **Choose trigger** (Scegli trigger) e quindi selezionare **Nuovo**. 

   ![Selezioni per l'aggiunta di un nuovo trigger](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. Nella pagina **Nuovo trigger** in **Fine** selezionare **On Date** (In data), specificare un'ora di fine successiva di qualche minuto all'ora corrente e quindi selezionare **Applica**. 

   Per ogni esecuzione della pipeline sono previsti costi, quindi specificare un'ora di fine successiva di qualche minuto all'ora di inizio. Assicurarsi che si tratti dello stesso giorno. Assicurarsi tuttavia che il tempo specificato tra l'ora di pubblicazione e l'ora di fine sia sufficiente per l'esecuzione della pipeline. Il trigger viene applicato solo dopo la pubblicazione della soluzione in Data Factory, non quando si salva il trigger nell'interfaccia utente. 

   ![Impostazioni di trigger](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Nella pagina **Nuovo trigger** selezionare la casella di controllo **Attivato** e quindi selezionare **Avanti**. 

   ![Casella di controllo "Attivato" e pulsante "Avanti"](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Rivedere il messaggio di avviso e selezionare **Fine**.

   ![Avviso e pulsante "Fine"](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Selezionare **Pubblica tutti** per pubblicare le modifiche in Data Factory. 

   ![Pulsante Publish](./media/quickstart-create-data-factory-portal/publish-button.png)
8. Passare alla scheda **Monitoraggio** a sinistra. Selezionare **Aggiorna** per aggiornare l'elenco. La pipeline viene eseguita una volta al minuto a partire dall'ora di pubblicazione fino all'ora di fine. 

   Notare i valori della colonna **Attivato da**. L'esecuzione manuale del trigger è stata generata dal passaggio precedente, ovvero **Trigger Now** (Attiva adesso). 

   ![Elenco delle esecuzioni attivate](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Selezionare la freccia verso il basso accanto a **Pipeline Runs** (Esecuzioni di pipeline) per passare alla visualizzazione **Trigger Runs** (Esecuzioni del trigger). 

   ![Passare alla visualizzazione "Trigger Runs" (Esecuzioni del trigger)](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Assicurarsi che venga creato un file di output per ogni esecuzione della pipeline fino alla data e ora di fine specificate nella cartella **output**. 

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra nell'archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, vedere le [esercitazioni](tutorial-copy-data-portal.md). 