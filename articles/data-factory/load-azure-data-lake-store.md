---
title: Caricare dati in Azure Data Lake Store tramite Azure Data Factory | Microsoft Docs
description: Usare Azure Data Factory per copiare dati in Azure Data Lake Store
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Caricare dati in Azure Data Lake Store tramite Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Azure Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

Azure Data Factory è un servizio di integrazione di dati basato su cloud completamente gestito, che può essere usato per popolare Azure Data Lake con i dati del sistema esistente e che consente di risparmiare tempo prezioso durante la compilazione di soluzioni di analisi. Di seguito sono elencati i vantaggi principali del caricamento di dati in Azure Data Lake Store mediante Azure Data Factory:

* **Semplicità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto completo per archivi dati**: supporto integrato per una vasta gamma di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute e la presenza di un servizio globale garantisce che i dati non superino mai il confine geografico.
* **Prestazioni elevate**: fino a 1 Gbps di velocità di caricamento dati in Azure Data Lake Store. Per informazioni, vedere l'articolo sulle [prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per **caricare dati da Amazon S3 in Azure Data Lake Store**. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
>  Per informazioni generali sulle funzionalità di Data Factory per la copia di dati da e verso Azure Data Lake Store, vedere [Copiare dati da e verso Azure Data Lake Store mediante Azure Data Factory](connector-azure-data-lake-store.md).
>
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Azure Data Lake Store**. Se non si ha un account Data Lake Store, vedere l'articolo [Creare un account Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) per informazioni su come crearne uno.
* **Amazon S3**. In questo articolo viene illustrato come copiare i dati da Amazon S3. È possibile usare altri archivi dati seguendo una procedura simile.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Fare clic su **NUOVO** nel menu a sinistra e quindi su **Dati e analisi** e **Data factory**. 
   
   ![Nuovo->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** inserire i valori come illustrato nella schermata seguente: 
      
     ![Pagina Nuova data factory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **Nome.** Immettere un nome univoco globale per la data factory. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
            `Data factory name "LoadADLSDemo" is not available`

    * **Sottoscrizione.** Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
    * **Gruppo di risorse.** Selezionare un gruppo di risorse esistente dall'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione.** Selezionare **V2 (anteprima)**.
    * **Località.** Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Azure Data Lake Store, Archiviazione di Azure, database SQL di Azure e così via) usati dalla data factory possono trovarsi in altre località/aree.

3. Fare clic su **Crea**.
4. Al termine della creazione, accedendo alla data factory verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata. 
   
   ![Home page di Data factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Caricare i dati in Azure Data Lake Store

1. Nella pagina delle attività iniziali fare clic sul riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** dello strumento Copia dati specificare **CopyFromAmazonS3ToADLS** per **Nome attività**, quindi fare clic su **Avanti**. 

    ![Strumento Copia dati - Pagina Proprietà](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** selezionare **Amazon S3** e fare clic su **Avanti**.

    ![Pagina Archivio dati di origine](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Nella pagina **Specificare la connessione ad Amazon S3**, effettuare i passaggi seguenti: 
    1. Specificare l'**ID della chiave di accesso**.
    2. Specificare la **chiave di accesso segreta**.
    3. Fare clic su **Avanti**. 

        ![Specificare l'account di Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Nella pagina **Scegliere il file o la cartella di input**, passare al file o alla cartella che si desidera copiare, selezionarli e fare clic su **Scegli**, quindi fare clic su **Avanti**. 

    ![Scegliere il file o la cartella di input](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Nella pagina **Archivio dati di destinazione**, selezionare **Azure Data Lake Store**, quindi fare clic su **Avanti**. 

    ![Pagina dell'archivio dati di destinazione](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. In questa pagina, scegliere il comportamento di copia spuntando le opzioni **Copia i file in modo ricorsivo** e **Copia binaria** (copia i file così come sono). Fare clic su **Avanti**.

    ![Specificare la cartella di output](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Nella pagina **Specifica la connessione a Data Lake Store**, effettuare i passaggi seguenti: 

    1. Selezionare il Data Lake Store per il **nome dell'account Data Lake Store**.
    2. Specificare l'entità servizio, incluse le informazioni relative a **Tenant**, **ID dell'entità servizio** e **chiave dell'entità servizio**.
    3. Fare clic su **Avanti**. 

    > [!IMPORTANT]
    > In questa procedura dettagliata, si userà un'**entità servizio** per autenticare il Data Lake Store. Seguire le indicazioni fornite [qui](connector-azure-data-lake-store.md#using-service-principal-authentication) e assicurarsi di concedere all'entità servizio un'autorizzazione appropriata in Azure Data Lake Store.

    ![Specificare un account di Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. Nella pagina **Choose the output file or folder** (Scegliere il file o la pagina di output), specificare **copyfroms3**, quindi fare clic su **Avanti**. 

    ![Specificare la cartella di output](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. Nella pagina **Impostazioni** fare clic su **Avanti**. 

    ![Pagina Impostazioni](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Nella pagina **Riepilogo** esaminare le impostazioni e fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Nella pagina **Distribuzione** fare clic su **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. Nella colonna **Azioni** sono disponibili i collegamenti per visualizzare i dettagli delle esecuzioni dell'attività e per eseguire di nuovo la pipeline. 

    ![Monitorare le esecuzioni di pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, fare clic sul collegamento **Pipeline** in alto. Fare clic su **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. È possibile monitorare ulteriormente i dettagli di esecuzione di ogni attività di copia facendo clic sul collegamento **Dettagli** in **Azioni** nella visualizzazione di monitoraggio delle attività. Vengono visualizzate informazioni come il volume dei dati copiati dall'origine al sink, la velocità effettiva, i passaggi eseguiti (con la relativa durata) e le configurazioni usate.

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verificare che i dati vengano copiati in Azure Data Lake Store. 

    ![Verificare l'output di Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Connettore di Azure Data Lake Store](connector-azure-data-lake-store.md)