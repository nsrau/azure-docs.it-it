---
title: Data tool to copy new and updated files incrementally
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files based on LastModifiedDate.
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
ms.openlocfilehash: 5c20196bd243d025d58f7cc08e015e1e0038e178
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217795"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Incrementally copy new and changed files based on LastModifiedDate by using the Copy Data tool

In this tutorial, you'll use the Azure portal to create a data factory. Then, you'll use the Copy Data tool to create a pipeline that incrementally copies new and changed files only, based on their **LastModifiedDate** from Azure Blob storage to Azure Blob storage.

By doing so, ADF will scan all the files from the source store, apply the file filter by their LastModifiedDate, and copy the new and updated file only since last time to the destination store.  Please note that if you let ADF scan huge amounts of files but only copy a few files to destination, you would still expect the long duration due to file scanning is time consuming as well.   

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In this tutorial, you will perform the following tasks:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Azure storage account**: Use Blob storage as the _source_ and _sink_ data store. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**. You can use various tools to perform this task, such as [Azure Storage Explorer](https://storageexplorer.com/).

2. Create a container named **destination**. 

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**: 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
 
   Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:
   
   ![Messaggio di errore per nuova data factory](./media/doc-common-process/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_ **ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
3. Select the Azure **subscription** in which you'll create the new data factory. 
4. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    * Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    * Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

5. Under **version**, select **V2**.
6. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. The data stores (for example, Azure Storage and SQL Database) and computes (for example, Azure HDInsight) that your data factory uses can be in other locations and regions.
7. Selezionare **Aggiungi al dashboard**. 
8. Selezionare **Create** (Crea).
9. On the dashboard, refer to the **Deploying Data Factory** tile to see the process status.

    ![Deploying Data Factory Tile](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Al termine della creazione verrà visualizzata la home page **Data factory**.
   
    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)
11. To open the Azure Data Factory user interface (UI) on a separate tab, select the **Author & Monitor** tile. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. On the **Let's get started** page, select the **Copy Data** title to open the Copy Data tool. 

   ![Riquadro dello strumento Copia dati](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    a. Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    b. Under **Task cadence** or **Task schedule**, select **Run regularly on schedule**.

    c. Under **Trigger Type**, select **Tumbling Window**.
    
    d. Under **Recurrence**, enter **15 Minute(s)** . 
    
    e. Selezionare **Avanti**. 
    
    L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato. 

    ![Pagina Proprietà](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Select  **+ Create new connection**, to add a connection.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selezionare **Archiviazione BLOB di Azure** nella raccolta e quindi **Continua**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. On the **New Linked Service** page, select your storage account from the **Storage account name** list and then select **Finish**.
    
    ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Select the newly created linked service and then select **Next**. 
    
   ![Pagina Archivio dati di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:
    
    a. Browse and select the **source** folder, and then select **Choose**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **File loading behavior**, select **Incremental load: LastModifiedDate**.
    
    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Check **Binary copy** and select **Next**.
    
     ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. On the **Destination data store** page, select **AzureBlobStorage**. This is the same storage account as the source data store. Quindi selezionare **Avanti**.

    ![Pagina dell'archivio dati di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) completare questa procedura:
    
    a. Browse and select the **destination** folder, and then select **Choose**.
    
    ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selezionare **Avanti**.
    
     ![Scegliere il file o la cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Nella pagina **Impostazioni** selezionare **Avanti**. 

    ![Pagina Impostazioni](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. On the **Summary** page, review the settings and then select **Next**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline. Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Refresh list and select View Activity Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. There's only one activity (the copy activity) in the pipeline, so you see only one entry. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. 

    ![Copy activity is in pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Because there is no file in the **source** container in your Blob storage account, you will not see any file copied to the **destination** container in your Blob storage account.
    
    ![No file in source container or destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Create an empty text file and name it **file1.txt**. Upload this text file to the **source** container in your storage account. Per eseguire queste attività è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Create file1.txt and upload to source container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. To go back to the **Pipeline Runs** view, select **All Pipeline Runs**, and wait for the same pipeline to be triggered again automatically.  

    ![Select All Pipeline Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Select **View Activity Run** for the second pipeline run when you see it. Then review the details in the same way you did for the first pipeline run.  

    ![Select View Activity Run and review details](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    You will that see one file (file1.txt) has been copied from the **source** container to the **destination** container of your Blob storage account.
    
    ![File1.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Create another empty text file and name it **file2.txt**. Upload this text file to the **source** container in your Blob storage account.   
    
16. Repeat steps 13 and 14 for this second text file. You will see that only the new file (file2.txt) has been copied from the **source** container to the **destination** container of your storage account in the next pipeline run.  
    
    ![File2.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    You can also verify this by using [Azure Storage Explorer](https://storageexplorer.com/) to scan the files.
    
    ![Scan files using Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passaggi successivi
Advance to the following tutorial to learn about transforming data by using an Apache Spark cluster on Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using an Apache Spark cluster](tutorial-transform-data-spark-portal.md)