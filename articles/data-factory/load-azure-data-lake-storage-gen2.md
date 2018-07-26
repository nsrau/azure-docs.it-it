---
title: Caricare dati in Azure Data Lake Storage Gen2 (anteprima) con Azure Data Factory
description: Usare Azure Data Factory per copiare dati in Azure Data Lake Storage Gen2 (anteprima)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: jingwang
ms.openlocfilehash: 558b426ea85decb0309390e36910eb18719e6e99
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002528"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-preview-with-azure-data-factory"></a>Caricare dati in Azure Data Lake Storage Gen2 (anteprima) con Azure Data Factory

La [versione di anteprima di Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md) aggiunge ad Archiviazione BLOB di Azure un protocollo con spazio dei nomi del file system gerarchico e funzionalità di sicurezza, semplificando la connessione dei framework di analisi a un livello di archiviazione durevole. Data Lake Storage Gen2 (anteprima) offre non solo tutte le qualità dell'archiviazione di oggetti, ma anche i vantaggi di un'interfaccia di file system.

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il lake con i dati provenienti da un set completo di archivi dati locali e basati sul cloud e risparmiare tempo durante la compilazione di soluzioni di analisi. Per un elenco dettagliato di connettori supportati, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre una soluzione di spostamento dei dati gestita e scale-out. ADF, grazie all'architettura scale-out, può inserire i dati a una velocità effettiva elevata. Per informazioni, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per caricare dati dal _servizio Amazon Web Services S3_ in _Azure Data Lake Storage Gen2_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

>[!TIP]
>Per copiare dati da Azure Data Lake Storage Gen1 a Gen2, consultare [questa procedura dettagliata specifica](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account di archiviazione di Azure con Data Lake Storage Gen2 abilitato: se non si ha un account di archiviazione, fare clic [qui](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) per crearne uno.
* Account AWS con un bucket S3 contenente dati: questo articolo illustra come copiare i dati da Amazon S3. È possibile usare altri archivi dati seguendo una procedura simile.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra scegliere **Nuovo** > **Dati e analisi** > **Data factory**:
   
   ![Creare una nuova data factory](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente: 
      
   ![Pagina Nuova data factory](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadADLSDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. 

3. Selezionare **Crea**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Caricare dati in Azure Data Lake Storage Gen2

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati: 

   ![Riquadro dello strumento Copia dati](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** specificare **CopyFromAmazonS3ToADLS** per il campo **Nome attività**, quindi selezionare **Avanti**:

    ![Pagina Proprietà](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** fare clic su **+ Crea nuova connessione**:

    ![Pagina Archivio dati di origine](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selezionare **Amazon S3** dalla raccolta di connettori e selezionare **Continua**
    
    ![Pagina Archivio dati di origine s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Nella pagina **Specificare la connessione ad Amazon S3**, effettuare i passaggi seguenti:
   1. Specificare il valore **ID della chiave di accesso**.
   2. Specificare il valore **Chiave di accesso segreta**.
   3. Fare clic su **Connessione di test** per convalidare le impostazioni, quindi selezionare **Fine**.
   
   ![Specificare l'account di Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   
   4. Si noterà che viene creata una nuova connessione. Selezionare **Avanti**.
   
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) passare alla cartella e al file da copiare. Selezionare la cartella o il file e selezionare **Scegli**:

    ![Scegliere il file o la cartella di input](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Specificare il comportamento di copia selezionando le opzioni **Copia i file in modo ricorsivo** e **Copia binaria**. Selezionare **Avanti**:

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Nella pagina **Archivio dati di destinazione** fare clic su **+ Crea nuova connessione**, quindi selezionare **Azure Data Lake Storage Gen2 (Preview)** (Azure Data Lake Storage Gen2 - Anteprima) e **Continua**:

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Nella pagina **Specify Azure Data Lake Storage connection** (Specifica la connessione ad Azure Data Lake Storage) seguire questa procedura:

   1. Selezionare l'account con supporto di Data Lake Storage Gen2 nell'elenco a discesa "Nome account di archiviazione".
   2. Selezionare **Avanti**.
   
   ![Specificare un account di Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) immettere **copyfroms3** come nome della cartella di output, quindi selezionare **Avanti**: 

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Nella pagina **Impostazioni** selezionare **Avanti** per usare le impostazioni predefinite:

    ![Pagina Impostazioni](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**:

    ![Pagina Riepilogo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline:

    ![Pagina Distribuzione](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline:

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate:

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Verificare che i dati vengano copiati nell'account Data Lake Storage Gen2.

## <a name="best-practices"></a>Procedure consigliate

Quando si copia un volume considerevole di dati dall'archivio dati basato su file, è consigliabile:

- Partizionare i file in set di file da 10 TB a 30 TB l'uno.
- Non attivare troppe esecuzioni di copia simultanee per evitare la limitazione dagli archivi di dati di origine o sink. È possibile iniziare con un'esecuzione di copia e monitorare la velocità effettiva, quindi aggiungerne gradualmente altre in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'attività di copia](copy-activity-overview.md)
* [Connettore di Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)