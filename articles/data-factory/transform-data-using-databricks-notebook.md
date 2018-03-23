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
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: d1dcec26529c747a209dd10fcefbbadaa40365a3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Eseguire un notebook di Databricks con l'attività dei notebook di Databricks in Azure Data Factory

In questa esercitazione si usa il portale di Azure per creare una pipeline di Azure Data Factory che eseguirà un notebook di Databricks nel cluster dei processi Databricks e passerà i parametri di Azure Data Factory al notebook di Databricks durante l'esecuzione.

In questa esercitazione vengono completati i passaggi seguenti:

  - Creare una data factory.

  - Creare una pipeline che usa l'attività dei notebook di Databricks.

  - Attivare un'esecuzione della pipeline.

  - Monitorare l'esecuzione della pipeline.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

  - **Area di lavoro di Azure Databricks**. [Creare un'area di lavoro di Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) o usarne una esistente. Creare un notebook Python nell'area di lavoro di Azure Databricks. Eseguire quindi il notebook e passare i parametri al notebook stesso usando Azure Data Factory.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1.  Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.

2.  Scegliere **Nuovo** dal menu a sinistra, selezionare **Dati e analisi** e quindi selezionare **Data Factory**.

    ![Creare una nuova data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  Nel riquadro **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

    Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, cambiare il nome della data factory. Usare, ad esempio **\<nomeutente\>ADFTutorialDataFactory**. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](https://docs.microsoft.com/en-us/azure/data-factory/naming-rules).

    ![Specificare un nome per la nuova data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory.

5.  In **Gruppo di risorse** eseguire una di queste operazioni:
    
    - Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.
    
    - Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.

    Alcuni dei passaggi di questa guida introduttiva presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1.  Per **Versione** selezionare **V2 (anteprima)**.

2.  Per **Località** selezionare la località per la data factory.

    Data Factory V2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio HDInsight) usati da Data Factory possono trovarsi in altre aree.

3.  Selezionare **Aggiungi al dashboard**.

4.  Selezionare **Create**.

5.  Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory):

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  Al termine della creazione verrà visualizzata la pagina **Data factory**. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Data Factory in una scheda separata.

    ![Avviare l'applicazione dell'interfaccia utente di Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Creare servizi collegati

In questa sezione viene creato un servizio collegato Databricks. Questo servizio collegato contiene le informazioni di connessione al cluster Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Creare un servizio collegato Azure Databricks

1.  Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra.

    ![Modificare il nuovo servizio collegato](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  Selezionare **Connessioni** nella parte inferiore della finestra e quindi **+ Nuovo**.
    
    ![Crea una nuova connessione](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Data Store** (Archivio dati) \> **Azure Databricks** e quindi fare clic su **Continue** (Continua).
    
    ![Specificare un servizio collegato Azure Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  Nella finestra **New Linked Service** (Nuovo servizio collegato) completare questa procedura:
    
    1.  In **Name** (Nome) immettere ***AzureDatabricks\_LinkedService***
    
    2.  In **Cluster** selezionare un **nuovo cluster**
    
    3.  In **Domain/Region** (Dominio/Area) selezionare l'area in cui si trova l'area di lavoro di Azure Databricks.
    
    4.  In **Cluster node type** (Tipo nodo cluster) selezionare **Standard\_D3\_v2** per questa esercitazione.
    
    5.  Generare il valore di **Access Token** (Token di accesso) nell'area di lavoro di Azure Databricks. La procedura è disponibile [qui](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  In **Cluster version** (Versione cluster) selezionare **4.0 Beta** (versione più recente)
    
    7.  In **Number of worker nodes** (Numero di nodi di lavoro) immettere **2**.
    
    8.  Selezionare **Finish** (Fine)

        ![Completare la creazione del servizio collegato](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Creare una pipeline

1.  Selezionare il pulsante **+** (segno più) e quindi selezionare **Pipeline** dal menu.

    ![Pulsanti per la creazione di una nuova pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Creare un **parametro** da usare nella **pipeline**. Questo parametro verrà successivamente passato all'attività dei notebook di Databricks. Nella pipeline vuota fare clic sulla scheda **Parametri**, quindi su **Nuovo** e assegnare il nome '**name**'.

    ![Creare un nuovo parametro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Creare il parametro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  Nella casella degli strumenti **Attività** espandere **Databricks**. Trascinare l'attività **Notebook** dalla casella degli strumenti **Attività** nell'area di progettazione della pipeline.

    ![Trascinare il notebook nell'area di progettazione](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  Nelle proprietà della finestra dell'attività **Databricks** **Notebook** in basso completare questa procedura:

    a. Passare alla scheda **Impostazioni** .

    b. Selezionare **myAzureDatabricks\_LinkedService** (creato nella procedura precedente).

    c. Selezionare il **percorso del notebook** di Databricks. Creare un notebook e specificare il percorso qui. Ottenere il percorso del notebook seguendo questi passaggi.

       1. Avviare l'area di lavoro di Azure Databricks

       2. Creare una **nuova cartella** nell'area di lavoro e chiamarla **adftutorial**.

          ![Creare una nuova cartella](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Creare un nuovo notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) denominato **mynotebook** nella cartella **adftutorial****,** quindi fare clic su **Crea**.

          ![Creare un nuovo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Impostare le proprietà del nuovo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. Aggiungere il codice seguente nel notebook "mynotebook" appena creato:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Creare i widget per i parametri](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. In questo caso, il **percorso del notebook** è **adftutorial/mynotebook**

5.  Tornare allo **strumento di creazione dell'interfaccia utente di Data Factory**. Passare alla scheda **Settings** (Impostazioni) nell'**attività Notebook1**. 
    
    a.  **Aggiungere un parametro** all'attività notebook. Usare lo stesso parametro aggiunto in precedenza alla **pipeline**.

       ![Aggiungere un parametro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  Assegnare al parametro il nome **input** e specificare il valore come espressione **@pipeline().parameters.name**.

6.  Per convalidare la pipeline, selezionare il pulsante **Convalida** sulla barra degli strumenti. Selezionare il pulsante **\>\>** (freccia destra) per chiudere la finestra di convalida.

    ![Convalidare la pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Selezionare **Pubblica tutti**. L'interfaccia utente di Data Factory pubblicherà le entità (servizi collegati e pipeline) nel servizio Azure Data Factory.

    ![Pubblicare le nuove entità di Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline

Selezionare **Trigger** sulla barra degli strumenti e quindi selezionare **Trigger Now** (Attiva adesso).

![Selezionare il comando Trigger Now (Attiva adesso)](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

La finestra di dialogo **Pipeline Run** (Esecuzione di pipeline) chiede il parametro **name**. Usare **/path/filename** come parametro qui. Fare clic su **Finish** (Fine).

![Specificare un valore per il parametro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1.  Passare alla scheda **Monitoraggio**. Verificare che venga visualizzata un'esecuzione della pipeline. Sono necessari all'incirca 5-8 minuti per creare un cluster dei processi Databricks in cui viene eseguito il notebook.

    ![Monitorare la pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Selezionare periodicamente **Aggiorna** per controllare lo stato dell'esecuzione della pipeline.

3.  Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**.

    ![Visualizzare le esecuzioni di attività](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

È possibile tornare alla visualizzazione delle esecuzioni di pipeline selezionando il collegamento **Pipeline** in alto.

## <a name="verify-the-output"></a>Verificare l'output

È possibile accedere all'**area di lavoro di Azure Databricks**, passare ai **cluster** e visualizzare lo stato del **processo** (*esecuzione in sospeso, in esecuzione o terminato*).

![Visualizzare il cluster dei processi e il processo](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

È possibile fare clic sul **nome del processo** ed esaminare altri dettagli. Se l'esecuzione ha esito positivo, è possibile convalidare i parametri passati e l'output del notebook Python.

![Visualizzare i dettagli dell'esecuzione e l'output](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questo esempio avvia un'attività dei notebook di Databricks e le passa un parametro. Si è appreso come:

  - Creare una data factory.

  - Creare una pipeline che usa un'attività dei notebook di Databricks.

  - Attivare un'esecuzione della pipeline.

  - Monitorare l'esecuzione della pipeline.
