---
title: Caricare dati in Azure Data Lake Storage Gen2
description: Usare Azure Data Factory per copiare dati in Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/08/2020
ms.openlocfilehash: 8f8cfef5ed98682a1d03f7d36caa2008f4ff03b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84660386"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data e integrate in [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md). Consente di interagire con i dati approfittando dei paradigmi sia del file system che dell'archiviazione di oggetti.

Azure Data Factory (ADF) è un servizio di integrazione dei dati basato su cloud completamente gestito. È possibile usare il servizio per popolare il lake con i dati provenienti da un set completo di archivi dati locali e basati sul cloud e risparmiare tempo durante la compilazione di soluzioni di analisi. Per un elenco dettagliato di connettori supportati, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre una soluzione di spostamento dei dati gestita e scale-out. ADF, grazie all'architettura scale-out, può inserire i dati a una velocità effettiva elevata. Per informazioni, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per caricare dati dal _servizio Amazon Web Services S3_ in _Azure Data Lake Storage Gen2_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

>[!TIP]
>Per copiare dati da Azure Data Lake Storage Gen1 a Gen2, consultare [questa procedura dettagliata specifica](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account di archiviazione di Azure con Data Lake Storage Gen2 abilitato: se non si ha un account di archiviazione, [creare un account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Account AWS con un bucket S3 contenente dati: questo articolo illustra come copiare i dati da Amazon S3. È possibile usare altri archivi dati seguendo una procedura simile.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**:
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Nella pagina **nuovo data factory** specificare i valori per i campi seguenti:
 
    * **Name**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "il nome della data factory *YourDataFactoryName* non è disponibile", immettere un nome diverso per il data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. 

3. Selezionare **Crea**.

4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Caricare dati in Azure Data Lake Storage Gen2

1. Nella pagina attività **iniziali** selezionare il riquadro **copia dati** per avviare lo strumento copia dati.

2. Nella pagina **Proprietà** specificare **CopyFromAmazonS3ToADLS** per il campo **nome attività** , quindi fare clic su **Avanti**.

    ![Pagina Proprietà](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Nella pagina **archivio dati di origine** fare clic su **+ Crea nuova connessione**. Selezionare **Amazon S3** dalla raccolta di connettori e selezionare **continua**.
    
    ![Pagina Archivio dati di origine s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Nella pagina **New Linked Service (Amazon S3)** seguire questa procedura:

   1. Specificare il valore **ID della chiave di accesso**.
   2. Specificare il valore **Chiave di accesso segreta**.
   3. Fare clic su **Test connessione** per convalidare le impostazioni, quindi selezionare **Crea**.

      ![Specificare l'account di Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Si noterà che viene creata una nuova connessione AmazonS3. Selezionare **Avanti**. 

5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) passare alla cartella e al file da copiare. Selezionare la cartella o il file e quindi **scegliere Scegli**.

    ![Scegliere il file o la cartella di input](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Specificare il comportamento di copia controllando le opzioni di copia **ricorsiva** e **binaria** . Selezionare **Avanti**.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Nella pagina **archivio dati di destinazione** fare clic **su + Crea nuova connessione**, quindi selezionare **Azure Data Lake storage Gen2**e selezionare **continua**.

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Nella pagina **New Linked Service (Azure Data Lake storage Gen2)** seguire questa procedura:

   1. Selezionare l'account per Data Lake Storage Gen2 dall'elenco a discesa dei nomi di account di archiviazione.
   2. Selezionare **Crea** per creare la connessione. Fare quindi clic su **Avanti**.   

        ![Specificare un account di Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Nella pagina **scegliere il file o la cartella** di output, immettere **copyfroms3** come nome della cartella di output e fare clic su **Avanti**. ADF creerà le sottocartelle e file system di ADLS Gen2 corrispondenti durante la copia se non esiste.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Nella pagina **Impostazioni** selezionare **Avanti** per usare le impostazioni predefinite.

    ![Pagina delle impostazioni](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Nella pagina **Riepilogo** verificare le impostazioni e fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività). 
 
13. Quando l'esecuzione della pipeline viene completata correttamente, viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **NOME PIPELINE** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Per visualizzare le esecuzioni attività associate all'esecuzione della pipeline, selezionare il collegamento **CopyFromAmazonS3ToADLS** nella colonna nome pipeline. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna NOME ATTIVITÀ. È possibile monitorare i dettagli, ad esempio il volume di dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e la configurazione usata.
 
    ![Monitorare le esecuzioni delle attività](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Per aggiornare la visualizzazione, selezionare Aggiorna. Per tornare alla visualizzazione Esecuzioni della pipeline, selezionare **Tutte le esecuzioni di pipeline** in alto.

16. Verificare che i dati vengano copiati nell'account Data Lake Storage Gen2.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'attività di copia](copy-activity-overview.md)
* [Connettore di Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
