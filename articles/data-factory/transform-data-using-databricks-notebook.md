---
title: Eseguire un notebook di Databricks con l'attività dei notebook di Databricks in Azure Data Factory
description: Informazioni su come usare l'attività dei notebook di Databricks in una data factory di Azure per eseguire un notebook di Databricks nel cluster dei processi Databricks.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: b8333ae7eb676fd38bf7d654cecdb4dd07ff7c03
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788796"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Eseguire un notebook di Databricks con l'attività dei notebook di Databricks in Azure Data Factory

In questa esercitazione si usa il portale di Azure per creare una pipeline di Azure Data Factory che eseguirà un notebook di Databricks nel cluster dei processi Databricks e passerà i parametri di Azure Data Factory al notebook di Databricks durante l'esecuzione.

In questa esercitazione vengono completati i passaggi seguenti:

  - Creare una data factory.

  - Creare una pipeline che usa l'attività dei notebook di Databricks.

  - Attivare un'esecuzione della pipeline.

  - Monitorare l'esecuzione della pipeline.

Se non si ha una sottoscrizione di Azure, creare un  [account gratuito](https://azure.microsoft.com/free/)  prima di iniziare.

Per un'introduzione di undici minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Prerequisiti

  - **Area di lavoro di Azure Databricks**. [Creare un'area di lavoro di Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) o usarne una esistente. Creare un notebook Python nell'area di lavoro di Azure Databricks. Eseguire quindi il notebook e passare i parametri al notebook stesso usando Azure Data Factory.

## <a name="create-a-data-factory"></a>Creare una data factory

1.  Avviare il Web browser **Microsoft Edge** o **Google Chrome** . L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.

1.  Selezionare **Crea una risorsa** dal menu a sinistra, selezionare **Analytics** e quindi selezionare **Data Factory**.

    ![Creare una nuova data factory](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  Nel riquadro **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

    È necessario specificare un nome  *univoco globale* per la data factory di Azure. Se viene visualizzato l'errore seguente, cambiare il nome della data factory. Ad esempio, usare  **\<nome\>ADFTutorialDataFactory**). Per le regole di denominazione per gli artefatti di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](https://docs.microsoft.com/azure/data-factory/naming-rules) .

    ![Specificare un nome per la nuova data factory](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory.

1.  Per **Gruppo di risorse** eseguire una di queste operazioni:
    
    - Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.
    
    - Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.

    Alcuni dei passaggi di questa guida introduttiva presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  Per **Versione**, selezionare **V2**.

1.  Per **Località** selezionare la località per la data factory.

    Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio HDInsight) usati da Data Factory possono trovarsi in altre aree.
1.  Selezionare **Crea**.


1.  Al termine della creazione verrà visualizzata la pagina **Data factory** . Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Data Factory in una scheda separata.

    ![Avviare l'applicazione dell'interfaccia utente di Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Creare servizi collegati

In questa sezione viene creato un servizio collegato Databricks. Questo servizio collegato contiene le informazioni di connessione al cluster Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Creare un servizio collegato Azure Databricks

1.  Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra.

    ![Modificare il nuovo servizio collegato](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Selezionare **Connessioni** nella parte inferiore della finestra e quindi **+ Nuovo**.
    
    ![Crea una nuova connessione](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Calcolo** \> **Azure Databricks** e quindi fare clic su **Continua**.
    
    ![Specificare un servizio collegato Azure Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Nella finestra **New Linked Service** (Nuovo servizio collegato) completare questa procedura:
    
    1.  In **Name** (Nome) immettere ***AzureDatabricks\_LinkedService***
    
    1.  Selezionare l'**area di lavoro Databricks** appropriata in cui verrà eseguito il notebook.

    1.  In **Select cluster** (Seleziona cluster) selezionare **New job cluster** (Nuovo cluster di processo).
    
    1.  Le informazioni in **Domain/ Region** (Dominio/Area) dovrebbero essere state inserite automaticamente.

    1.  Generare il valore di **Access Token** (Token di accesso) nell'area di lavoro di Azure Databricks. La procedura è disponibile [qui](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  In **Cluster version** (Versione cluster) selezionare **4.2** (con Apache Spark 2.3.1, Scala 2.11)

    1.  In **Cluster node type** (Tipo di nodo cluster) selezionare **Standard\_D3\_v2** nella categoria **General Purpose (HDD)** (Utilizzo generico (HDD)) per questa esercitazione. 
    
    1.  In **Workers** (Ruoli di lavoro) immettere **2**.
    
    1.  Selezionare **Finish** (Fine)

        ![Completare la creazione del servizio collegato](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Creare una pipeline

1.  Selezionare il pulsante **+** (segno più) e quindi selezionare **Pipeline** dal menu.

    ![Pulsanti per la creazione di una nuova pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Creare un **parametro** da usare nella **pipeline**. Questo parametro verrà successivamente passato all'attività dei notebook di Databricks. Nella pipeline vuota fare clic sulla scheda **Parametri**, quindi su **Nuovo** e assegnare il nome '**name**'.

    ![Creare un nuovo parametro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Creare il parametro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Nella casella degli strumenti **Attività** espandere **Databricks**. Trascinare l'attività **Notebook** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline.

    ![Trascinare il notebook nell'area di progettazione](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Nelle proprietà della finestra dell'attività **Databricks** **Notebook** in basso completare questa procedura:

    a. Passare alla scheda **Azure Databricks** .

    b. Selezionare **AzureDatabricks\_LinkedService** (creato nella procedura precedente).

    c. Passare alla scheda **Impostazioni**.

    c. Sfogliare per selezionare un **percorso del notebook**  di Databricks. Creare un notebook e specificare il percorso qui. Ottenere il percorso del notebook seguendo questi passaggi.

       1. Avviare l'area di lavoro di Azure Databricks

       1. Creare una **nuova cartella** nell'area di lavoro e chiamarla **adftutorial**.

          ![Creare una nuova cartella](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Creare un nuovo notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) denominato **mynotebook** nella cartella **adftutorial** e fare clic su **Crea**.

          ![Creare un nuovo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Impostare le proprietà del nuovo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Aggiungere il codice seguente nel notebook "mynotebook" appena creato:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Creare i widget per i parametri](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. In questo caso, il **percorso del notebook** è **adftutorial/mynotebook**

1.  Tornare allo **strumento di creazione dell'interfaccia utente di Data Factory**. Passare alla scheda **Settings** (Impostazioni) nell'**attività Notebook1**.

    a.  **Aggiungere un parametro** all'attività notebook. Usare lo stesso parametro aggiunto in precedenza alla **pipeline**.

       ![Aggiungere un parametro](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Assegnare al parametro il nome **input** e specificare il valore come espressione **\@pipeline().parameters.name**.

1.  Per convalidare la pipeline, selezionare il pulsante **Convalida** sulla barra degli strumenti. Selezionare il pulsante **\>\>** (freccia destra) per chiudere la finestra di convalida.

    ![Convalidare la pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Selezionare **Pubblica tutti**. L'interfaccia utente di Data Factory pubblicherà le entità (servizi collegati e pipeline) nel servizio Azure Data Factory.

    ![Pubblicare le nuove entità di Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline

Selezionare **Trigger** sulla barra degli strumenti e quindi selezionare **Trigger Now**(Attiva adesso).

![Selezionare il comando Trigger Now (Attiva adesso)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

La finestra di dialogo **Pipeline Run** (Esecuzione di pipeline) chiede il parametro **name**. Usare **/path/filename** come parametro qui. Fare clic su **Finish** (Fine).

![Specificare un valore per il parametro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1.  Passare alla scheda **Monitoraggio** . Verificare che venga visualizzata un'esecuzione della pipeline. Sono necessari all'incirca 5-8 minuti per creare un cluster dei processi Databricks in cui viene eseguito il notebook.

    ![Monitorare la pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Selezionare periodicamente **Aggiorna** per controllare lo stato dell'esecuzione della pipeline.

1.  Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni** .

    ![Visualizzare le esecuzioni di attività](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

È possibile tornare alla visualizzazione delle esecuzioni di pipeline selezionando il collegamento **Pipelines** in alto.

## <a name="verify-the-output"></a>Verificare l'output

È possibile accedere all'**area di lavoro di Azure Databricks**, passare ai **cluster** e visualizzare lo stato del **processo** (*esecuzione in sospeso, in esecuzione o terminato*).

![Visualizzare il cluster dei processi e il processo](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

È possibile fare clic sul **nome del processo** ed esaminare altri dettagli. Se l'esecuzione ha esito positivo, è possibile convalidare i parametri passati e l'output del notebook Python.

![Visualizzare i dettagli dell'esecuzione e l'output](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questo esempio avvia un'attività dei notebook di Databricks e le passa un parametro. Si è appreso come:

  - Creare una data factory.

  - Creare una pipeline che usa un'attività dei notebook di Databricks.

  - Attivare un'esecuzione della pipeline.

  - Monitorare l'esecuzione della pipeline.
