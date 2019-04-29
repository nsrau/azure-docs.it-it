---
title: La copia incrementale di file nuovi e modificati basati LastModifiedDate & lt; tramite lo strumento Copia dati | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati da caricare in modo incrementale i nuovi file basati LastModifiedDate & lt;.
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
ms.date: 1/24/2019
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61098738"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>La copia incrementale di file nuovi e modificati basati LastModifiedDate & lt; tramite lo strumento Copia dati

In questa esercitazione si userà il portale di Azure per creare una data factory. Quindi, si userà lo strumento Copia dati per creare una pipeline che copia in modo incrementale, solo i file nuovi e modificati in base loro **LastModifiedDate & lt;** dall'archivio Blob di Azure per archiviazione Blob di Azure.

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si eseguirà le attività seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: Usare l'archiviazione Blob come le _origine_ e _sink_ archivio dati. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Creare due contenitori nell'archivio Blob

Preparare l'archivio Blob per l'esercitazione eseguendo questi passaggi.

1. Creare un contenitore denominato **origine**. È possibile usare vari strumenti per eseguire questa attività, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

2. Creare un contenitore denominato **destinazione**. 

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**: 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
      
     ![Nuova data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:
   
   ![Messaggio di errore per nuova data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
3. Selezionare di Azure **sottoscrizione** in cui si creerà la nuova data factory. 
4. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    * Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    * Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

5. Sotto **versione**, selezionare **V2**.
6. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi di dati (ad esempio, archiviazione di Azure e Database SQL) e risorse di calcolo (ad esempio, Azure HDInsight) che Usa data factory può essere in altre località e aree.
7. Selezionare **Aggiungi al dashboard**. 
8. Selezionare **Create**.
9. Nel dashboard, vedere la **distribuzione di Data Factory** riquadro per visualizzare lo stato del processo.

    ![Riquadro Deploying Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Al termine della creazione verrà visualizzata la home page **Data factory**.
   
    ![Home page di Data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Per aprire l'interfaccia di utente (UI) di Azure Data Factory in una scheda separata, selezionare la **crea e monitora** riquadro. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nel **attività iniziali** pagina, selezionare la **copia dati** title per aprire lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Nel **proprietà** pagina, procedere come segue:

    a. Sotto **nome dell'attività**, immettere **DeltaCopyFromBlobPipeline**.

    b. Sotto **cadenza attività** oppure **utilità di pianificazione**, selezionare **regolarmente in esecuzione su pianificazione**.

    c. Sotto **tipo di Trigger**, selezionare **finestra a cascata**.
    
    d. Sotto **ricorrenza**, immettere **minuto/15 i**. 
    
    e. Selezionare **Avanti**. 
    
    L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato. 

    ![Pagina Proprietà](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Selezionare **+ Crea nuova connessione**, per aggiungere una connessione.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selezionare **archiviazione Blob di Azure** dalla raccolta e quindi selezionare **continua**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Nel **nuovo servizio collegato** pagina, selezionare l'account di archiviazione il **nome account di archiviazione** elenco e quindi selezionare **fine**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selezionare il servizio collegato appena creato e quindi selezionare **successivo**. 
    
   ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:
    
    a. Individuare e selezionare il **origine** cartella e quindi selezionare **Scegli**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sotto **File, il comportamento di caricamento**, selezionare **caricamento incrementale: LastModifiedDate**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Controllare **copia binaria** e selezionare **successivo**.
    
     ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Nel **archivio dati di destinazione** pagina, selezionare **AzureBlobStorage**. Questo è lo stesso account di archiviazione come archivio dati di origine. Quindi selezionare **Avanti**.

    ![Pagina dell'archivio dati di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) completare questa procedura:
    
    a. Individuare e selezionare il **destinazione** cartella e quindi selezionare **Scegli**.
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selezionare **Avanti**.
    
     ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Nella pagina **Impostazioni** selezionare **Avanti**. 

    ![Pagina Impostazioni](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Nel **Summary** pagina, esaminare le impostazioni e quindi selezionare **successivo**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline. Selezionare **Refresh** per aggiornare l'elenco e selezionare il **esecuzioni di attività di visualizzazione** clic sul collegamento nel **azioni** colonna. 

    ![Aggiornare l'elenco e selezionare Visualizza esecuzioni attività](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. È presente una sola attività (attività di copia) nella pipeline, viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. 

    ![Attività di copia è nella pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Poiché è presente alcun file nei **origine** contenitore nell'account di archiviazione Blob, non verrà visualizzato alcun file copiati il **destinazione** contenitore nell'account di archiviazione Blob.
    
    ![Nessun file nel contenitore di origine o destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Creare un file di testo vuota e denominarla **file1.txt**. Caricare il file di testo per il **origine** contenitore nell'account di archiviazione. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Creazione di File1. txt e caricare il contenitore di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Per tornare al **esecuzioni di Pipeline** visualizzazione, selezionare **tutte le esecuzioni di Pipeline**e attendere che la stessa pipeline venga attivato automaticamente.  

    ![Selezionare tutte le esecuzioni di Pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selezionare **vista esecuzione attività** per la seconda pipeline vengono eseguita quando viene visualizzato. Esaminare quindi i dettagli nello stesso modo che è stato fatto per la prima esecuzione della pipeline.  

    ![Selezionare la visualizzazione attività di esecuzione ed esaminare i dettagli](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Si apprenderà come argomento, vedere uno file (file1) è stato copiato dal **origine** contenitore per il **destinazione** contenitore dell'account di archiviazione Blob.
    
    ![File1.txt siano stati copiati dal contenitore di origine al contenitore di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Creare un altro file di testo vuoto e denominarla **file2.txt**. Caricare il file di testo per il **origine** contenitore nell'account di archiviazione Blob.   
    
16. Ripetere i passaggi da 13 e 14 per il secondo file di testo. Si noterà che solo il nuovo file (file2.txt) siano stato copiato dal **origine** contenitore per il **destinazione** contenitore dell'account di archiviazione nell'esecuzione della pipeline successiva.  
    
    ![File2.txt siano stati copiati dal contenitore di origine al contenitore di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    È inoltre possibile verificarlo utilizzando [Azure Storage Explorer](https://storageexplorer.com/) per analizzare i file.
    
    ![Analizzare i file usando Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Apache Spark in Azure:

> [!div class="nextstepaction"]
>[Trasformare dati nel cloud usando un cluster Apache Spark](tutorial-transform-data-spark-portal.md)