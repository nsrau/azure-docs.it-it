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
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399479"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiare in modo incrementale i file nuovi e modificati basati su LastModifiedDate utilizzando lo strumento Copia dati

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si userà il portale di Azure per creare una data factory. Si userà quindi lo strumento Copia dati per creare una pipeline che copia solo i file nuovi e modificati, dall'archiviazione BLOB di Azure all'archiviazione BLOB di Azure.You'll then use the Copy Data tool to create a pipeline that incrementally copies new and changed files only, from Azure Blob storage to Azure Blob storage. Utilizza `LastModifiedDate` per determinare quali file copiare.

Dopo aver completato i passaggi qui, Azure Data Factory analirà tutti `LastModifiedDate`i file nell'archivio di origine, applicherà il filtro di file per e copierà nell'archivio di destinazione solo i file nuovi o aggiornati dall'ultima volta. Si noti che se Data Factory analizza un numero elevato di file, è comunque necessario prevedere durate lunghe. La scansione dei file richiede molto tempo, anche quando la quantità di dati copiati viene ridotta.

> [!NOTE]
> Se non si ha familiarità con Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si completeranno le attività seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di Archiviazione di Azure:** usare l'archiviazione BLOB per gli archivi dati di origine e sink. Se non si ha un account di archiviazione di Azure, seguire le istruzioni in [Creare un account di archiviazione.](../storage/common/storage-account-create.md)

## <a name="create-two-containers-in-blob-storage"></a>Creare due contenitori nell'archiviazione BLOBCreate two containers in Blob storage

Preparare l'archiviazione BLOB per l'esercitazione completando questi passaggi:Prepare your Blob storage for the tutorial by completing these steps:

1. Creare un contenitore denominato **source**. È possibile usare vari strumenti per eseguire questa attività, ad esempio Esplora archivi di [Azure.](https://storageexplorer.com/)

2. Creare un contenitore denominato **destination**.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel riquadro a sinistra selezionare **Crea risorsa**. Seleziona**Data Factory** **di Analytics** > :

   ![Seleziona data factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory deve essere univoco a livello globale. È possibile che venga visualizzato questo messaggio di errore:You might receive this error message:

   ![Messaggio di errore Nome non disponibile](./media/doc-common-process/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
3. In **Sottoscrizione**selezionare la sottoscrizione di Azure in cui verrà creata la nuova data factory.
4. In Gruppo di risorse eseguire una delle operazioni seguenti:Under **Resource Group**, take one of these steps:

    * Selezionare **Usa esistente** e quindi selezionare un gruppo di risorse esistente nell'elenco.

    * Selezionare **Crea nuovo** e quindi immettere un nome per il gruppo di risorse.
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md).

5. In **Versione** selezionare **V2**.
6. In **Posizione**selezionare il percorso per la data factory. Nell'elenco vengono visualizzate solo le posizioni supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL di Azure) e i calcoli (ad esempio, Azure HDInsight) utilizzati dalla data factory possono trovarsi in altre posizioni e aree.
8. Selezionare **Create** (Crea).
9. Dopo aver creato la data factory, viene visualizzata la home page della data factory.
10. Per aprire l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **Autore & Monitoraggio:To** open the Azure Data Factory user interface (UI) on a separate tab, select the Author & Monitor tile:

    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina Introduzione selezionare il riquadro Copia dati per aprire lo strumento Copia dati:On the **Let's started** page, select the **Copy Data** tile to open the Copy Data tool:

   ![Copia riquadro Dati](./media/doc-common-process/get-started-page.png)

2. Nella pagina **Proprietà** eseguire le operazioni seguenti:

    a. In **Nome attività**immettere **DeltaCopyFromBlobPipeline**.

    b. In **Cadenza attività o Pianificazione attività**selezionare Esegui regolarmente in base alla **pianificazione.**

    c. In **Tipo di trigger**selezionare Finestra a **cascata**.

    d. In **Ricorrenza**immettere **15 minuti.**

    e. Selezionare **Avanti**.

    Data Factory crea una pipeline con il nome dell'attività specificato.

    ![Pagina delle proprietà Copia dati](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Nella pagina **Archivio dati di origine** completare i passaggi seguenti:On the Source data store page, complete these steps:

    a. Selezionare **Crea nuova connessione** per aggiungere una connessione.

    b. Selezionare **Archiviazione BLOB di Azure** dalla raccolta e quindi selezionare Continua:Select Azure Blob Storage from the gallery, and then select **Continue:**

    ![Selezionare Archiviazione blog di AzureSelect Azure Blog Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Nella pagina **Nuovo servizio collegato (Archiviazione BLOB** di Azure) selezionare l'account di archiviazione nell'elenco Nome account di **archiviazione.** Verificare la connessione e quindi selezionare **Crea**.

    d. Selezionare il nuovo servizio collegato, quindi **scegliere Avanti**:

   ![Selezionare il nuovo servizio collegato](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:

    a. Cercare e selezionare la cartella **di origine,** quindi selezionare **Scegli**.

    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. In **Comportamento caricamento file**selezionare Caricamento **incrementale: LastModifiedDate**.

    c. Selezionare **Copia binaria** e quindi **Avanti**:

     ![Scegliere il file di input o la pagina della cartella](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Nella pagina **Archivio dati** di destinazione selezionare il servizio **AzureBlobStorage** creato. Si tratta dello stesso account di archiviazione dell'archivio dati di origine. Fare quindi clic su **Avanti**.

6. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) completare questa procedura:

    a. Cercare e selezionare la cartella di **destinazione,** quindi selezionare **Scegli:**

    ![Scegliere la pagina del file o della cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selezionare **Avanti**.

7. Nella pagina **Impostazioni** selezionare **Avanti**.

8. Nella pagina **Riepilogo** rivedere le impostazioni e quindi selezionare **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. L'applicazione passa alla scheda **Monitor.** Viene visualizzato lo stato della pipeline. Selezionare **Aggiorna** per aggiornare l'elenco. Selezionare il collegamento in **NOME PIPELINE** per visualizzare i dettagli di esecuzione dell'attività o per eseguire nuovamente la pipeline.

    ![Aggiornare l'elenco e visualizzare i dettagli dell'esecuzione dell'attività](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Nella pipeline è presente una sola attività (l'attività di copia), pertanto viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (l'icona degli occhiali) nella colonna **NOME ACTIVITY.** Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività di copia](copy-activity-overview.md).

    ![Attività di copia nella pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Poiché non sono presenti file nel contenitore di origine nell'account di archiviazione BLOB, i file copiati nel contenitore di destinazione nell'account non verranno visualizzati nel contenitore di destinazione:Because there are no files in the source container in your Blob storage account, you won't see any files copied to the destination container in the account:

    ![Nessun file nel contenitore di origine o di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Creare un file di testo vuoto e denominarlo **file1.txt**. Caricare questo file di testo nel contenitore di origine nell'account di archiviazione. È possibile usare vari strumenti per eseguire queste attività, ad esempio Esplora archivi di Azure.You can use various tools to perform these tasks, like [Azure Storage Explorer](https://storageexplorer.com/).

    ![Creare file1.txt e caricarlo nel contenitore di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Per tornare alla visualizzazione **Esecuzione pipeline,** selezionare **Tutte le esecuzioni della pipeline**e attendere che la stessa pipeline venga attivata di nuovo automaticamente.  

    ![Seleziona tutte le esecuzioni della pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Al termine della seconda esecuzione della pipeline, seguire gli stessi passaggi menzionati in precedenza per esaminare i dettagli dell'esecuzione dell'attività.  

    Si noterà che un file (file1.txt) è stato copiato dal contenitore di origine al contenitore di destinazione dell'account di archiviazione BLOB:

    ![file1.txt è stato copiato dal contenitore di origine nel contenitore di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Creare un altro file di testo vuoto e denominarlo **file2.txt**. Caricare questo file di testo nel contenitore di origine nell'account di archiviazione BLOB.

16. Ripetere i passaggi 13 e 14 per il secondo file di testo. Si noterà che solo il nuovo file (file2.txt) è stato copiato dal contenitore di origine nel contenitore di destinazione dell'account di archiviazione durante l'esecuzione della pipeline.  

    È anche possibile verificare che sia stato copiato un solo file usando Azure Storage Explorer per eseguire l'analisi dei file:You can also verify that only one file has been copied by using [Azure Storage Explorer](https://storageexplorer.com/) to scan the files:

    ![Eseguire l'analisi dei file usando Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione seguente per informazioni su come trasformare i dati usando un cluster Apache Spark in Azure:Go to the following tutorial to learn how to transform data by using an Apache Spark cluster on Azure:

> [!div class="nextstepaction"]
>[Trasformare i dati nel cloud usando un cluster Apache SparkTransform data in the cloud by using an Apache Spark cluster](tutorial-transform-data-spark-portal.md)
