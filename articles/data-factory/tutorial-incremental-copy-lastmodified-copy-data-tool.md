---
title: Strumento Dati per copiare i file nuovi e aggiornati in modo incrementale
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per caricare in modo incrementale i nuovi file in base a LastModifiedDate.Create an Azure data factory and then use the Copy Data tool to incrementally load new files based on LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131249"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiare in modo incrementale i file nuovi e modificati basati su LastModifiedDate utilizzando lo strumento Copia dati

In questa esercitazione si userà il portale di Azure per creare una data factory. Si userà quindi lo strumento Copia dati per creare una pipeline che copia solo i file nuovi e modificati, in base a LastModifiedDate dall'archiviazione BLOB di Azure all'archiviazione BLOB di Azure.Then, you'll use the Copy Data tool to create a pipeline that incrementally copies new and changed files only, based on their **LastModifiedDate** from Azure Blob storage to Azure Blob storage.

In questo modo, ADF analirà tutti i file dall'archivio di origine, applicherà il filtro di file in base a LastModifiedDate e copierà il file nuovo e aggiornato solo dall'ultima volta nell'archivio di destinazione.  Si prega di notare che se si lascia ADF scansione enormi quantità di file, ma copiare solo alcuni file a destinazione, ci si aspetterebbe ancora la lunga durata a causa della scansione dei file è dispendioso in termini di tempo pure.   

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione verranno effettuate le attività seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure:** se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure:** usare l'archiviazione BLOB come archivio dati di _origine_ e _sink._ Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Creare due contenitori nell'archiviazione BLOBCreate two containers in Blob storage

Preparare l'archiviazione BLOB per l'esercitazione eseguendo questi passaggi.

1. Creare un contenitore denominato **source**. È possibile usare vari strumenti per eseguire questa attività, ad esempio [Azure Storage Explorer.](https://storageexplorer.com/)

2. Creare un contenitore denominato **destination**.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > Dati - Data**Factory****analisi:** > 

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:

   ![Messaggio di errore per nuova data factory](./media/doc-common-process/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
3. Selezionare la **sottoscrizione** di Azure in cui verrà creata la nuova data factory.
4. In **Gruppo di risorse** eseguire una di queste operazioni:

    * Selezionare **Usa esistente** e selezionare un gruppo di risorse esistente dall'elenco a discesa.

    * Selezionare **Crea nuovo** e immettere il nome di un gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md).

5. In **Versione**selezionare **V2**.
6. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e i calcoli (ad esempio, Azure HDInsight) utilizzati dalla data factory possono trovarsi in altre posizioni e aree.
8. Selezionare **Crea**.
9. Al termine della creazione verrà visualizzata la home page **Data factory**.
10. Per aprire l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **Autore & Monitoraggio.To** open the Azure Data Factory user interface (UI) on a separate tab, select the Author & Monitor tile.

    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina **Introduzione** selezionare il titolo **Copia dati** per aprire lo strumento Copia dati.

   ![Riquadro dello strumento Copia dati](./media/doc-common-process/get-started-page.png)

2. Nella pagina **Proprietà** eseguire le operazioni seguenti:

    a. In **Nome attività**immettere **DeltaCopyFromBlobPipeline**.

    b. In **Cadenza attività** o **Pianificazione attività**selezionare Esegui regolarmente in base **alla pianificazione.**

    c. In **Tipo trigger**, selezionare Finestra a **cascata**.

    d. In **Ricorrenza**immettere **15 minuti.**

    e. Fare clic su **Avanti**.

    L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato.

    ![Pagina Proprietà](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Per aggiungere una connessione, selezionare **Crea nuova connessione.**

    b. Selezionare **Archiviazione BLOB di Azure** nella raccolta e quindi **Continua**.

    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Nella pagina **Nuovo servizio collegato (Archiviazione BLOB** di Azure) selezionare l'account di archiviazione nell'elenco Nome account di **archiviazione.** Verifica connessione e quindi selezionare **Crea**.

    d. Selezionare il servizio collegato appena creato e quindi **Avanti**.

   ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:

    a. Individuare e selezionare la cartella **di origine,** quindi scegliere **Scegli**.

    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. In **Comportamento caricamento file**selezionare Caricamento **incrementale: LastModifiedDate**.

    c. Selezionare **Copia binaria** e selezionare **Avanti**.

     ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Nella pagina **Archivio dati** di destinazione selezionare **azureBlobStorage** creato. Si tratta dello stesso account di archiviazione dell'archivio dati di origine. Quindi selezionare **Avanti**.

6. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) completare questa procedura:

    a. Individuare e selezionare la cartella di **destinazione,** quindi scegliere **Scegli**.

    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Fare clic su **Avanti**.

7. Nella pagina **Impostazioni** selezionare **Avanti**.

8. Nella pagina **Riepilogo** rivedere le impostazioni e quindi selezionare **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. L'applicazione passa alla scheda **Monitor.** Viene visualizzato lo stato della pipeline. Selezionare **Aggiorna** per aggiornare l'elenco. Fare clic sul collegamento in **NOME PIPELINE** per visualizzare i dettagli di esecuzione dell'attività o eseguire nuovamente la pipeline. 

    ![Aggiorna e selezionare Visualizza esecuzioni attività](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Nella pipeline è presente una sola attività (l'attività di copia), pertanto viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **NOME ACTIVITY.** Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività Copia](copy-activity-overview.md). 

    ![L'attività di copia è in pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Poiché non è presente alcun file nel contenitore di **origine** nell'account di archiviazione BLOB, non verrà visualizzato alcun file copiato nel contenitore di **destinazione** nell'account di archiviazione BLOB.

    ![Nessun file nel contenitore di origine o di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Creare un file di testo vuoto e denominarlo **file1.txt**. Caricare questo file di testo nel contenitore di **origine** nell'account di archiviazione. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

    ![Creare file1.txt e caricarlo nel contenitore di origineCreate file1.txt and upload to source container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Per tornare alla visualizzazione **Esecuzioni pipeline,** selezionare **Tutte le esecuzioni della pipeline**e attendere che la stessa pipeline venga attivata di nuovo automaticamente.  

    ![Seleziona tutte le esecuzioni della pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Al termine della seconda esecuzione della pipeline, seguire gli stessi passaggi menzionati in precedenza per esaminare i dettagli dell'esecuzione dell'attività.  

    Si noterà che un file (file1.txt) è stato copiato dal contenitore di **origine** al contenitore di **destinazione** dell'account di archiviazione BLOB.

    ![File1.txt è stato copiato dal contenitore di origine al contenitore di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Creare un altro file di testo vuoto e denominarlo **file2.txt**. Caricare questo file di testo nel contenitore di **origine** nell'account di archiviazione BLOB.

16. Ripetere i passaggi 13 e 14 per questo secondo file di testo. Si noterà che solo il nuovo file (file2.txt) è stato copiato dal contenitore di **origine** al contenitore di **destinazione** dell'account di archiviazione nella successiva esecuzione della pipeline.  

    È anche possibile verificarlo usando [Azure Storage Explorer](https://storageexplorer.com/) per eseguire l'analisi dei file.

    ![Eseguire l'analisi dei file usando Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione seguente per informazioni sulla trasformazione dei dati usando un cluster Apache Spark in Azure:Advance to the following tutorial to learn about transforming data by using an Apache Spark cluster on Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati nel cloud usando un cluster Apache SparkTransform data in the cloud by using an Apache Spark cluster](tutorial-transform-data-spark-portal.md)
