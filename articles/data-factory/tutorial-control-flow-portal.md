---
title: Diramazione in una pipeline di Azure Data Factory | Microsoft Docs
description: "Informazioni su come controllare il flusso dei dati in Azure Data Factory con la diramazione e il concatenamento delle attività."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 2b1e3fa7fa57d92dbc3a33af20ed258d674e1625
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Diramazione e concatenamento delle attività in una pipeline di Data factory
In questa esercitazione si crea una pipeline di Data Factory che illustra alcune funzionalità del flusso di controllo. La pipeline esegue una semplice copia da un contenitore nell'archivio BLOB di Azure a un altro contenitore nello stesso account di archiviazione. Se l'attività di copia ha esito positivo, la pipeline invia i dettagli dell'operazione di copia completata (ad esempio, la quantità di dati scritti) in un messaggio di posta elettronica di operazione riuscita. Se l'attività di copia ha esito negativo, la pipeline invia i dettagli dell'errore di copia (ad esempio, il messaggio di errore) in un messaggio di posta elettronica di operazione non riuscita. Nel corso dell'esercitazione verrà illustrato come passare i parametri.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Panoramica generale dello scenario: ![Panoramica](media/tutorial-control-flow-portal/overview.png)

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un servizio collegato Archiviazione di Azure
> * Creare un set di dati del BLOB di Azure
> * Creare una pipeline contenente un'attività Copia e un'attività Web
> * Inviare gli output delle attività alle attività successive
> * Utilizzare il passaggio di parametri e le variabili di sistema
> * Avviare un'esecuzione della pipeline
> * Monitorare le esecuzioni di pipeline e attività

Questa esercitazione usa il portale di Azure. È possibile usare altri meccanismi per interagire con Azure Data Factory. Vedere "Guide introduttive" nel sommario.

## <a name="prerequisites"></a>prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
* **Database SQL di Azure**. Usare il database come archivio dati **sink**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).

### <a name="create-blob-table"></a>Creare la tabella BLOB

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **input.txt** sul disco.

    ```
    John,Doe
    Jane,Doe
    ```
2. Usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per seguire questa procedura: 
    1. Creare il contenitore **adfv2branch**.
    2. Creare la cartella di **input** nel contenitore **adfv2branch**.
    3. Caricare il file **input.txt** nel contenitore.

## <a name="create-email-workflow-endpoints"></a>Creare gli endpoint del flusso di lavoro del messaggio di posta elettronica
Per attivare l'invio di un messaggio di posta elettronica dalla pipeline, usare [App per la logica](../logic-apps/logic-apps-overview.md) per definire il flusso di lavoro. Per informazioni dettagliate sulla creazione di un flusso di lavoro di app per la logica, vedere l'articolo su [come creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Flusso di lavoro del messaggio di posta elettronica di operazione riuscita 
Creare un flusso di lavoro di app per la logica denominato `CopySuccessEmail`. Definire `When an HTTP request is received` come trigger del flusso di lavoro e aggiungere un'azione `Office 365 Outlook – Send an email`.

![Flusso di lavoro del messaggio di posta elettronica di operazione riuscita](media/tutorial-control-flow-portal/success-email-workflow.png)

Per il trigger di richiesta, compilare `Request Body JSON Schema` con il codice JSON seguente:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

La richiesta nella finestra di progettazione dell'app per la logica avrà un aspetto simile all'immagine seguente: 

![Progettazione app per la logica: richiesta](media/tutorial-control-flow-portal/logic-app-designer-request.png)

Per l'azione **Invia un messaggio di posta elettronica** personalizzare la formattazione del messaggio nel modo desiderato, utilizzando le proprietà passate nello schema JSON del corpo della richiesta. Di seguito è fornito un esempio: 

![Progettazione app per la logica: azione Invia un messaggio di posta elettronica](media/tutorial-control-flow-portal/send-email-action-2.png)

Salvare il flusso di lavoro. Prendere nota dell'URL della richiesta HTTP Post per il flusso di lavoro del messaggio di posta elettronica di operazione riuscita:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Flusso di lavoro del messaggio di posta elettronica di operazione non riuscita 
Seguire la stessa procedura per creare un flusso di lavoro di App per la logica di **CopyFailEmail**. Nel trigger di richiesta, `Request Body JSON schema` è uguale. Modificare la formattazione del messaggio di posta elettronica, ad esempio `Subject`, per adattarlo a un messaggio di posta elettronica di operazione non riuscita. Di seguito è fornito un esempio: 

![Progettazione app per la logica: flusso di lavoro del messaggio di posta elettronica di operazione non riuscita](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Salvare il flusso di lavoro. Prendere nota dell'URL della richiesta HTTP Post per il flusso di lavoro del messaggio di posta elettronica di operazione non riuscita:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Si avranno ora due URL di flusso di lavoro:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-control-flow-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-control-flow-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “ADFTutorialDataFactory” is not available`
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

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.


## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'attività Copia e due attività Web. Vengono usate le funzionalità seguenti per creare la pipeline:

- Parametri per la pipeline a cui accedono i set di dati. 
- Attività Web per richiamare i flussi di lavoro delle app per la logica per inviare messaggi di posta elettronica di operazione riuscita o non riuscita. 
- Connessione di un'attività con un'altra attività (in caso di esito positivo e negativo)
- Uso dell'output di un'attività come input per l'attività successiva

1. Nella pagina **Attività iniziali** dell'interfaccia utente di Data Factory fare clic sul riquadro **Create pipeline** (Crea pipeline).  

   ![Pagina delle attività iniziali](./media/tutorial-control-flow-portal/get-started-page.png) 
3. Nella finestra delle proprietà per la pipeline passare alla scheda **Parametri** e usare il pulsante **Nuovo** per aggiungere i tre parametri seguenti di tipo String: sourceBlobContainer, sinkBlobContainer e receiver. 

    - **sourceBlobContainer**: parametro nella pipeline utilizzato dal set di dati del BLOB di origine.
    - **sinkBlobContainer**: parametro nella pipeline utilizzato dal set di dati del BLOB sink.
    - **receiver**: questo parametro viene usato dalle due attività Web della pipeline che inviano i messaggi di posta elettronica di operazione riuscita o non riuscita al destinatario il cui indirizzo di posta elettronica è specificato da questo parametro.

   ![Menu per nuova pipeline](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. Nella casella degli strumenti **Attività** espandere **Flusso di dati** e trascinare l'attività **Copia** nell'area di progettazione della pipeline. 

   ![Trascinare l'attività di copia](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. Nella finestra **Proprietà** per l'attività **Copia** nella parte inferiore passare alla scheda **Origine** e fare clic su **+ Nuovo**. In questa attività viene creato un set di dati di origine per l'attività di copia. 

   ![Set di dati di origine](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e fare clic su **Fine**. 

   ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. Viene visualizzata una nuova **scheda** intitolata **AzureBlob1**. Modificare il nome del set di dati in **SourceBlobDataset**.

   ![Impostazioni generali del set di dati](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. Passare alla scheda **Connessione** nella finestra **Proprietà** e fare clic su Nuovo per **Servizio collegato**. In questo passaggio viene creato un servizio collegato per collegare l'account di archiviazione di Azure Storage alla data factory. 
    
   ![Connessione al set di dati - Nuovo servizio collegato](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureStorageLinkedService** per **Nome**.
    2. Selezionare l'account di archiviazione di Azure per **Nome account di archiviazione**.
    3. Fare clic su **Save**.

   ![Nuovo servizio collegato di archiviazione di Azure](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Immettere `@pipeline().parameters.sourceBlobContainer` per la cartella e `emp.txt` per il nome file. Usare il parametro sourceBlobContainer della pipeline per configurare il percorso della cartella per il set di dati. 

    ![Impostazioni del set di dati di origine](./media/tutorial-control-flow-portal/source-dataset-settings.png)
13. Passare alla scheda **pipeline** oppure fare clic sulla visualizzazione albero. Assicurarsi che **SourceBlobDataset** sia selezionato per **Source Dataset** (Set di dati di origine). 

   ![Set di dati di origine](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)
13. Nella finestra delle proprietà passare alla scheda **Sink** e fare clic su **+ Nuovo** per **Sink Dataset** (Set di dati sink). In questo passaggio viene creato un set di dati sink per l'attività di copia con una procedura simile a quella per il set di dati di origine. 

    ![Pulsante per il nuovo set di dati sink](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. Nella finestra **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e fare clic su **Fine**. 
15. Nella pagina di impostazioni **Generale** per il set di dati immettere **SinkBlobDataset** per **Nome**.
16. Passare alla scheda **Connessione** e seguire questa procedura: 

    1. Selezionare **AzureStorageLinkedService** per **LinkedService**.
    2. Immettere `@pipeline().parameters.sinkBlobContainer` per la cartella.
    1. Immettere `@CONCAT(pipeline().RunId, '.txt')` per il nome del file. L'espressione usa l'ID dell'esecuzione attuale della pipeline per il nome del file. Per l'elenco delle variabili di sistema e delle espressioni supportate, vedere [Variabili di sistema](control-flow-system-variables.md) e [Linguaggio delle espressioni](control-flow-expression-language-functions.md).

        ![Impostazioni del set di dati sink](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Passare alla scheda **pipeline** nella parte superiore della schermata. Espandere **Generale** nella casella degli strumenti **Attività** e trascinare un'attività **Web** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **SendSuccessEmailActivity**. L'attività Web consente una chiamata a qualsiasi endpoint REST. Per altre informazioni sull'attività, vedere l'articolo relativo all'[attività Web](control-flow-web-activity.md). Questa pipeline usa un'attività Web per chiamare il flusso di lavoro di app per la logica per il messaggio di posta elettronica. 

   ![Trascinare la prima attività Web](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Passare alla scheda **Impostazioni** dalla scheda **Generale** e seguire questa procedura: 
    1. Per **URL** specificare l'URL per il flusso di lavoro di app per la logica che invia il messaggio di posta elettronica di operazione riuscita.  
    2. Selezionare **POST** per **Metodo**. 
    3. Fare clic sul collegamento **+ Aggiungi intestazione** nella sezione **Intestazioni**. 
    4. Aggiungere un'intestazione **Tipo contenuto** e impostarla su **application/json**. 
    5. Specificare il codice JSON seguente per **Corpo**. 

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        Il corpo del messaggio contiene le proprietà seguenti:

        - Messaggio: passa il valore di `@{activity('Copy1').output.dataWritten`. Accede a una proprietà della precedente attività di copia e passa il valore di dataWritten. In caso di esito negativo, passa invece l'output di errore di `@{activity('CopyBlobtoBlob').error.message`.
        - Nome data factory: passa il valore di `@{pipeline().DataFactory}`. È una variabile di sistema che consente di accedere al nome di data factory corrispondente. Per un elenco delle variabili di sistema, vedere l'articolo relativo alle [variabili di sistema](control-flow-system-variables.md).
        - Nome pipeline: passa il valore di `@{pipeline().Pipeline}`. È anche questa una variabile di sistema, che consente di accedere al nome di pipeline corrispondente. 
        - Destinatario: passa il valore di "@pipeline().parameters.receiver"), per l'accesso ai parametri della pipeline.
    
        ![Impostazioni per la prima attività Web](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Connettere l'attività **Copia** all'attività **Web** trascinando il pulsante verde accanto all'attività Copia e rilasciandolo sull'attività Web. 

    ![Connettere l'attività Copia con la prima attività Web](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Trascinare un'altra attività **Web** dalla casella degli strumenti Attività all'area di progettazione della pipeline e impostare **nome** su **SendFailureEmailActivity**.

    ![Nome della seconda attività Web](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Passare alla scheda **Impostazioni** e seguire questa procedura:

    1. Per **URL** specificare l'URL per il flusso di lavoro di app per la logica che invia il messaggio di posta elettronica di operazione non riuscita.  
    2. Selezionare **POST** per **Metodo**. 
    3. Fare clic sul collegamento **+ Aggiungi intestazione** nella sezione **Intestazioni**. 
    4. Aggiungere un'intestazione **Tipo contenuto** e impostarla su **application/json**. 
    5. Specificare il codice JSON seguente per **Corpo**. 

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Impostazioni per la seconda attività Web](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. Selezionare l'attività **Copia** nella finestra di progettazione della pipeline e fare clic sul pulsante **+->**, quindi selezionare **Errore**.  

    ![Impostazioni per la seconda attività Web](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Trascinare il pulsante **rosso** accanto all'attività Copia sulla seconda attività Web **SendFailureEmailActivity**. È possibile spostare le attività in modo che l'aspetto della pipeline sia simile all'immagine seguente: 

    ![Pipeline completa con tutte le attività](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Per convalidare la pipeline, fare clic sul pulsante **Convalida** sulla barra degli strumenti. Chiudere la finestra **Pipeline Validation Output** (Output di convalida della pipeline) facendo clic sul pulsante **>>**.

    ![Convalidare la pipeline](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Per pubblicare le entità (set di dati, pipeline e così via) nel servizio Data Factory, selezionare **Pubblica tutti**. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**.

    ![Pubblica](./media/tutorial-control-flow-portal/publish-button.png)
 
## <a name="trigger-a-pipeline-run-that-succeeds"></a>Attivare un'esecuzione della pipeline con esito positivo
1. Per **attivare** un'esecuzione della pipeline, fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

    ![Attivare un'esecuzione della pipeline](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. Nella finestra **Pipeline Run** (Esecuzioni di pipeline) seguire questa procedura: 

    1. Immettere **adftutorial/adfv2branch/input** per il parametro **sourceBlobContainer**. 
    2. Immettere **adftutorial/adfv2branch/output** per il parametro **sinkBlobContainer**. 
    3. Immettere un **indirizzo di posta elettronica** per **receiver**. 
    4. Fare clic su **Fine**

        ![Parametri di esecuzione delle pipeline](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Monitorare l'esecuzione della pipeline con esito positivo

1. Per monitorare l'esecuzione della pipeline passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline attivata manualmente in precedenza. Usare il pulsante **Aggiorna** per aggiornare l'elenco. 
    
    ![Esecuzione riuscita della pipeline](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Per **visualizzare le esecuzioni attività** associate a questa esecuzione della pipeline, fare clic sul primo collegamento nella colonna **Azioni**. È possibile tornare alla visualizzazione precedente facendo clic su **Pipeline** in alto. Usare il pulsante **Aggiorna** per aggiornare l'elenco. 

    ![Esecuzioni attività](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Attivare un'esecuzione della pipeline con esito negativo
1. Passare alla scheda **Modifica** a sinistra. 
2. Per **attivare** un'esecuzione della pipeline, fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 
3. Nella finestra **Pipeline Run** (Esecuzioni di pipeline) seguire questa procedura: 

    1. Immettere **adftutorial/dummy/input** per il parametro **sourceBlobContainer**. Assicurarsi che la cartella fittizia non esista nel contenitore adftutorial. 
    2. Immettere **adftutorial/dummy/inputt** per il parametro **sinkBlobContainer**. 
    3. Immettere un **indirizzo di posta elettronica** per **receiver**. 
    4. Fare clic su **Fine**.

## <a name="monitor-the-failed-pipeline-run"></a>Monitorare l'esecuzione della pipeline non riuscita

1. Per monitorare l'esecuzione della pipeline passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata l'esecuzione della pipeline attivata manualmente in precedenza. Usare il pulsante **Aggiorna** per aggiornare l'elenco. 
    
    ![Esecuzione della pipeline non riuscita](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Fare clic sul collegamento **Errore** per l'esecuzione della pipeline per visualizzare i dettagli dell'errore. 

    ![Errore della pipeline](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Per **visualizzare le esecuzioni attività** associate a questa esecuzione della pipeline, fare clic sul primo collegamento nella colonna **Azioni**. Usare il pulsante **Aggiorna** per aggiornare l'elenco. Si noti che l'attività Copia nella pipeline ha avuto esito negativo. L'attività Web è riuscita a inviare il messaggio di posta elettronica di errore al destinatario specificato. 

    ![Esecuzioni attività](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Fare clic sul collegamento **Errore** nella colonna **Azioni** per visualizzare informazioni dettagliate sull'errore. 

    ![Errore di esecuzione dell'attività](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un servizio collegato Archiviazione di Azure
> * Creare un set di dati del BLOB di Azure
> * Creare una pipeline contenente un'attività di copia e un'attività Web
> * Inviare gli output delle attività alle attività successive
> * Utilizzare il passaggio di parametri e le variabili di sistema
> * Avviare un'esecuzione della pipeline
> * Monitorare le esecuzioni di pipeline e attività

È ora possibile passare alla sezione Concetti per altre informazioni su Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipeline e attività](concepts-pipelines-activities.md)
