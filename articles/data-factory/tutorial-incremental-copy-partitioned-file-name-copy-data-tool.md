---
title: Usando Azure Data Factory per copiare in modo incrementale i nuovi file basati solo sul nome di file partizionati ora | Microsoft Docs
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati da caricare in modo incrementale i nuovi file basati solo sul nome di file partizionati di tempo.
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
ms.openlocfilehash: df1542d6d20120a9b1e087fadf3743479ecebf07
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533834"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>La copia incrementale di nuovi file basati sul nome di file partizionati tempo usando lo strumento Copia dati

In questa esercitazione si usa il portale di Azure per creare una data factory. È quindi possibile utilizzare lo strumento Copia dati per creare una pipeline che copia i nuovi file in base al nome del file partizionata tempo dall'archivio Blob di Azure da archiviazione Blob di Azure in modo incrementale. 

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

1. Creare un contenitore denominato **origine**.  Creare un percorso della cartella come **2019/02/26/14** nel contenitore. Creare un file di testo vuoto e denominarlo **file1.txt**. Caricare il file1. txt al percorso della cartella **origine/2019/02/26/14** nell'account di archiviazione.  Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).
    
    ![caricamento dei file](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Modificare il nome della cartella con l'ora UTC.  Ad esempio, se l'ora UTC corrente è 2 dalle 22.03 del 26 febbraio 2019, è possibile creare il percorso della cartella come **origine/2019/02/26/14/** dalla regola di **origine / {Year} / {Month} / {Day} / {Hour} /**.

2. Creare un contenitore denominato **destinazione**. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **+ Nuovo** > **Dati e analisi** > **Data factory**: 
   
   ![Creazione di una nuova data factory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
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
    
    d. Sotto **ricorrenza**, immettere **ora/1 e**. 
    
    e. Selezionare **Avanti**. 
    
    L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato. 

    ![Pagina Proprietà](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione**, per aggiungere una connessione.

    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Selezionare **archiviazione Blob di Azure** dalla raccolta e quindi fare clic su **continua**.

    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Nel **nuovo servizio collegato** pagina, selezionare l'account di archiviazione il **nome account di archiviazione** elenco e quindi fare clic su **fine**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selezionare il servizio collegato appena creato, quindi fare clic su **successivo**. 
    
   ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) seguire questa procedura:
    
    a. Individuare e selezionare il **origine** contenitore, quindi selezionare **Scegli**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Sotto **File, il comportamento di caricamento**, selezionare **caricamento incrementale: i nomi di file/cartella partizionata ora**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Il percorso della cartella dinamica come scrivere **origine / {year} / {month} / {day} / {hour} /** e modificare il formato come indicato di seguito:
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Controllare **copia binaria** e fare clic su **successivo**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Nel **archivio dati di destinazione** pagina, selezionare la **AzureBlobStorage**, che è la stessa risorsa di archiviazione dell'account come archivio dell'origine dati e quindi fare clic su **Avanti**.

    ![Pagina dell'archivio dati di destinazione](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Nel **scegliere il file di output o la cartella** pagina, effettuare i passaggi seguenti:
    
    a. Individuare e selezionare il **destinazione** cartella, quindi fare clic su **Scegli**.
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Il percorso della cartella dinamica come scrivere **origine / {year} / {month} / {day} / {hour} /** e modificare il formato come indicato di seguito:
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Fare clic su **Avanti**.
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Nella pagina **Impostazioni** selezionare **Avanti**. 

    ![Pagina Impostazioni](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).
    ![Pagina di distribuzione](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente.  È necessario attendere l'esecuzione quando viene attivata automaticamente della pipeline (sulle dopo un'ora).  Quando è in esecuzione, il **azioni** colonna include i collegamenti per visualizzare i dettagli dell'esecuzione dell'attività ed eseguire di nuovo la pipeline. Selezionare **Refresh** per aggiornare l'elenco e selezionare il **esecuzioni di attività di visualizzazione** clic sul collegamento nel **azioni** colonna. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. È possibile visualizzare il file di origine (file1) è stato copiato dalla **origine/2019/02/26/14/** al **destinazione/2019/02/26/14/** con lo stesso nome di file.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    È anche possibile verificare lo stesso usando Azure Storage Explorer (https://storageexplorer.com/) per analizzare i file.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Creare un altro file di testo vuota con il nuovo nome come **file2.txt**. Caricare il file file2.txt al percorso della cartella **origine/2019/02/26 o 15** nell'account di archiviazione.   Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).   
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > È possibile sapere che un nuovo percorso della cartella è necessario la creazione. Modificare il nome della cartella con l'ora UTC.  Ad esempio, se l'ora UTC corrente è 3 20 PM 26 febbraio 2019, è possibile creare il percorso della cartella come **origine/2019/02/26 o 15/** dalla regola di **{Year} / {Month} / {Day} / {Hour} /**.
    
13. Per tornare al **esecuzioni di Pipeline** visualizzazione, selezionare **tutte le esecuzioni di pipeline**e attendere che la stessa pipeline nuovamente attivata automaticamente dopo un'ora un'altra.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selezionare **vista esecuzione attività** per la seconda pipeline vengono eseguita quando viene fornito ed eseguire la stessa operazione per esaminare i dettagli.  

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    È possibile visualizzare il file di origine (file2.txt) è stato copiato dalla **origine/2019/02/26 o 15/** al **destinazione/2019/02/26 o15/** con lo stesso nome di file.
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    È anche possibile verificare lo stesso usando Azure Storage Explorer (https://storageexplorer.com/) per analizzare i file in **destinazione** contenitore
    
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati usando un cluster Spark nel cloud](tutorial-transform-data-spark-portal.md)