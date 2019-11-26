---
title: Copia i nuovi file in modo incrementale in base al nome del file partizionato ora
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per caricare in modo incrementale i nuovi file in base al nome del file partizionato ora.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 746b5cbcc58f6c722623446227417e6c94dd0a80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217457"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Consente di copiare in modo incrementale i nuovi file in base al nome del file partizionato ora utilizzando lo strumento Copia dati

In questa esercitazione si usa il portale di Azure per creare una data factory. Si usa quindi lo strumento Copia dati per creare una pipeline che copia in modo incrementale i nuovi file in base al nome file partizionato dell'ora dall'archiviazione BLOB di Azure all'archivio BLOB di Azure. 

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: usare l'archiviazione BLOB come archivio dati di _origine_ e _sink_ . Se non si ha un account di archiviazione di Azure, vedere le istruzioni riportate in [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Creare due contenitori nell'archivio BLOB

Preparare l'archiviazione BLOB per l'esercitazione eseguendo questi passaggi.

1. Creare un contenitore denominato **source**.  Creare un percorso di cartella come **2019/02/26/14** nel contenitore. Creare un file di testo vuoto e denominarlo **file1. txt**. Caricare il codice file1. txt nell'origine percorso cartella **/2019/02/26/14** nell'account di archiviazione.  Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).
    
    ![caricamento dei file](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Modificare il nome della cartella con l'ora UTC.  Ad esempio, se l'ora UTC corrente è 2:03 PM il 26 febbraio 2019, è possibile creare il percorso della cartella come **origine/2019/02/26/14/** dalla regola di **origine/{year}/{month}/{day}/{hour}** /.

2. Creare un contenitore denominato **Destination**. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**: 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
    
    Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:
   
   ![Messaggio di errore per nuova data factory](./media/doc-common-process/name-not-available-error.png)
   
   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_ **ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
3. Selezionare la **sottoscrizione** di Azure in cui creare la nuova data factory. 
4. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

5. In **Versione** selezionare la versione **V2**.
6. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre località e aree.
7. Selezionare **Aggiungi al dashboard**. 
8. Selezionare **Create**.
9. Il riquadro **Deploying Data Factory** (Distribuzione della data factory) nel dashboard mostra lo stato del processo.

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Al termine della creazione verrà visualizzata la home page **Data factory**.
   
    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)
11. Per avviare l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **Crea e monitora**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina attività **iniziali** selezionare il titolo della **copia dati** per avviare lo strumento copia dati. 

   ![Riquadro dello strumento Copia dati](./media/doc-common-process/get-started-page.png)
   
2. Nella pagina **Proprietà** seguire questa procedura:

    a. In **nome attività**immettere **DeltaCopyFromBlobPipeline**.

    b. In **cadenza attività o pianificazione attività**selezionare **Esegui regolarmente in base alla pianificazione**.

    C. In **tipo di trigger**selezionare **finestra a cascata**.
    
    d. In **ricorrenza**immettere **1 ora**/e. 
    
    e. Selezionare **Avanti**. 
    
    L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato. 

    ![Pagina Proprietà](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione**per aggiungere una connessione.

    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Selezionare **archiviazione BLOB di Azure** dalla raccolta e quindi fare clic su **continua**.

    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    C. Nella pagina **nuovo servizio collegato** selezionare l'account di archiviazione dall'elenco **nome account di archiviazione** e quindi fare clic su **fine**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selezionare il servizio collegato appena creato, quindi fare clic su **Avanti**. 
    
   ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) seguire questa procedura:
    
    a. Individuare e selezionare il contenitore di **origine** , quindi **scegliere Scegli**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. In **comportamento caricamento file**selezionare **caricamento incrementale: cartella/nomi file partizionati in base al tempo**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    C. Scrivere il percorso della cartella dinamica come **origine/{year}/{month}/{day}/{hour}** /e modificare il formato come segue:
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Controllare la **copia binaria** e fare clic su **Avanti**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Nella pagina **archivio dati di destinazione** selezionare il **AzureBlobStorage**, che è lo stesso account di archiviazione dell'archivio dell'origine dati, quindi fare clic su **Avanti**.

    ![Pagina dell'archivio dati di destinazione](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Nella pagina **scegliere il file o la cartella di output** seguire questa procedura:
    
    a. Individuare e selezionare la cartella di **destinazione** , quindi fare clic su **Scegli**.
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Scrivere il percorso della cartella dinamica come **origine/{year}/{month}/{day}/{hour}** /e modificare il formato come segue:
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    C. Fare clic su **Next** (Avanti).
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Nella pagina **Impostazioni** selezionare **Avanti**. 

    ![Pagina Impostazioni](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).
    ![pagina di distribuzione](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente.  È necessario attendere l'esecuzione della pipeline quando viene attivata automaticamente (circa dopo un'ora).  Quando viene eseguito, nella colonna **azioni** sono inclusi collegamenti per visualizzare i dettagli dell'esecuzione dell'attività e per eseguire di nuovo la pipeline. Selezionare **Aggiorna** per aggiornare l'elenco e selezionare il collegamento **Visualizza esecuzioni attività** nella colonna **azioni** . 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. È possibile visualizzare il file di origine (file1. txt) copiato da **origine/2019/02/26/14/** a **destinazione/2019/02/26/14/** con lo stesso nome file.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    È anche possibile verificare lo stesso usando Azure Storage Explorer (https://storageexplorer.com/) per analizzare i file.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Creare un altro file di testo vuoto con il nuovo nome come **file2. txt**. Caricare il file file2. txt nell'origine percorso cartella **/2019/02/26/15** nell'account di archiviazione.   Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).   
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > È possibile che si sia consapevoli che è necessario creare un nuovo percorso della cartella. Modificare il nome della cartella con l'ora UTC.  Ad esempio, se l'ora UTC corrente è 3:20 PM il 26 febbraio 2019, è possibile creare il percorso della cartella come **origine/2019/02/26/15/** dalla regola **{year}/{month}/{day}/{hour}/** .
    
13. Per tornare alla visualizzazione delle **esecuzioni di pipeline** , selezionare **tutte le esecuzioni**di pipeline e attendere che la stessa pipeline venga nuovamente attivata automaticamente dopo un'altra ora.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selezionare **Visualizza esecuzione attività** per la seconda esecuzione della pipeline, quindi eseguire la stessa operazione per esaminare i dettagli.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    È possibile visualizzare il file di origine (file2. txt) copiato dall' **origine/2019/02/26/15/** a **destinazione/2019/02/26/15/** con lo stesso nome file.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    È anche possibile verificare lo stesso usando Azure Storage Explorer (https://storageexplorer.com/) per eseguire la scansione dei file nel contenitore di **destinazione**
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati usando un cluster Spark nel cloud](tutorial-transform-data-spark-portal.md)