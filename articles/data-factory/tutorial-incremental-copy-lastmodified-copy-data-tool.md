---
title: Usando Azure Data Factory per la copia incrementale di file nuovi e modificati solo in base LastModifiedDate & lt; | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati da caricare in modo incrementale i nuovi file basati solo sul LastModifiedDate & lt;.
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
ms.openlocfilehash: d79b44d0123d64d6280939767e5df7b5f64a5fcb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445962"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>La copia incrementale di file nuovi e modificati basati LastModifiedDate & lt; tramite lo strumento Copia dati

In questa esercitazione si usa il portale di Azure per creare una data factory. È quindi possibile utilizzare lo strumento Copia dati per creare una pipeline che copia i file nuovi e modificati basati solo sul loro "LastModifiedDate & lt;" dall'archivio Blob di Azure per archiviazione Blob di Azure in modo incrementale. 

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: Usare l'archiviazione Blob come le _origine_ e _sink_ archivio dati. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Creare due contenitori nell'archivio Blob

Preparare l'archivio Blob per l'esercitazione eseguendo questi passaggi.

1. Creare un contenitore denominato **origine**. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

2. Creare un contenitore denominato **destinazione**. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra, selezionare **crea una risorsa** > **dati + Analitica** > **Data Factory**: 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
      
     ![Nuova data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:
   
   ![Messaggio di errore per nuova data factory](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
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
   
    ![Home page di Data factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Per avviare l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **Crea e monitora**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nel **attività iniziali** pagina, selezionare la **copia dati** title per avviare lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Nel **proprietà** pagina, procedere come segue:

    a. Sotto **nome dell'attività**, immettere **DeltaCopyFromBlobPipeline**.

    b. Sotto **cadenza attività o utilità di pianificazione**, selezionare **regolarmente in esecuzione su pianificazione**.

    c. Sotto **tipo di Trigger**, selezionare **finestra a cascata**.
    
    d. Sotto **ricorrenza**, immettere **minuto/15 i**. 
    
    e. Selezionare **Avanti**. 
    
    L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato. 

    ![Pagina Proprietà](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione**, per aggiungere una connessione.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selezionare **archiviazione Blob di Azure** dalla raccolta e quindi fare clic su **continua**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Nel **nuovo servizio collegato** pagina, selezionare l'account di archiviazione il **nome account di archiviazione** elenco e quindi fare clic su **fine**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selezionare il servizio collegato appena creato, quindi fare clic su **successivo**. 
    
   ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) seguire questa procedura:
    
    a. Individuare e selezionare il **origine** cartella, quindi fare clic su **Scegli**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sotto **File, il comportamento di caricamento**, selezionare **caricamento incrementale: LastModifiedDate**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Controllare **copia binaria** e fare clic su **successivo**.
    
     ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Nel **archivio dati di destinazione** pagina, selezionare la **AzureBlobStorage** che è la stessa risorsa di archiviazione dell'account come archivio dell'origine dati e quindi fare clic su **Avanti**.

    ![Pagina dell'archivio dati di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Nel **scegliere il file di output o la cartella** pagina, effettuare i passaggi seguenti:
    
    a. Individuare e selezionare il **destinazione** cartella, quindi fare clic su **Scegli**.
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Fare clic su **Avanti**.
    
     ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Nella pagina **Impostazioni** selezionare **Avanti**. 

    ![Pagina Impostazioni](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline. Selezionare **Refresh** per aggiornare l'elenco e selezionare il **esecuzioni di attività di visualizzazione** clic sul collegamento nel **azioni** colonna. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Dato che è presente alcun file in **origine** contenitore nell'account di archiviazione blob, pertanto non verrà visualizzato alcun file di cui è stato copiato in **destinazione** contenitore nell'account di archiviazione blob.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Creare un file di testo vuota e denominarla come file1. txt. Caricare il file File1. txt per il **origine** contenitore nell'account di archiviazione. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Per tornare al **esecuzioni di Pipeline** visualizzazione, selezionare **tutte le esecuzioni di pipeline**e attendere che la stessa pipeline viene attivata automaticamente.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selezionare **vista esecuzione attività** per il secondo se esecuzione di pipeline vedere viene fornito ed eseguirla la stessa operazione per esaminare i dettagli.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Verrà visualizzato un file (file1) è stato copiato dal **origine** contenitore per il **destinazione** contenitore dell'account di archiviazione.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Creare un altro file di testo vuota e denominarla come file2.txt. Caricare il file file2.txt il **origine** contenitore nell'account di archiviazione. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).  
    
16. Eseguire la stessa come passaggio 13 e 14, e verrà visualizzato solo il nuovo file (file2.txt) è stato copiato dal **origine** contenitore per il **destinazione** contenitore dell'account di archiviazione nell'esecuzione della pipeline successiva.  
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    È anche possibile verificare lo stesso usando Azure Storage Explorer (https://storageexplorer.com/) per analizzare i file.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati usando un cluster Spark nel cloud](tutorial-transform-data-spark-portal.md)