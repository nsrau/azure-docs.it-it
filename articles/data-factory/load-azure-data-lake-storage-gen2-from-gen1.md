---
title: Copiare i dati da Azure Data Lake Storage Gen1 a Gen2Copy data from Azure Data Lake Storage Gen1 to Gen2
description: Usare Azure Data Factory per copiare dati da Azure Data Lake Storage Gen1 a Gen2
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
ms.date: 05/13/2019
ms.openlocfilehash: 81f8577592f1d53627bc09a2f9ace8c060ad4660
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668860"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory

Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data integrata nell'archiviazione BLOB di [Azure.](../storage/blobs/storage-blobs-introduction.md) È possibile utilizzarlo per interfacciarsi con i dati utilizzando sia i paradigmi di archiviazione del file system che quello di archiviazione degli oggetti.

Se attualmente si usa Azure Data Lake Storage Gen1, è possibile valutare Azure Data Lake Storage Gen2 copiando i dati da Data Lake Storage Gen1 a Gen2 usando Azure Data Factory.If you currently use Azure Data Lake Storage Gen1, you can evaluate Azure Data Lake Storage Gen2 by copying data from Data Lake Storage Gen1 to Gen2 by using Azure Data Factory.

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il lake con i dati di un set completo di archivi dati locali e basati su cloud e risparmiare tempo quando si creano le soluzioni di analisi. Per un elenco dei connettori supportati, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre una soluzione di spostamento dei dati gestita e scale-out. Grazie all'architettura con scalabilità orizzontale di Data Factory, è possibile inserire dati a una velocità effettiva elevata. Per ulteriori informazioni, consultate [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento dati di copia di Data Factory per copiare i dati da Azure Data Lake Storage Gen1 in Azure Data Lake Storage Gen2. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account Data Lake Storage Gen1 contenente dati.
* Account di archiviazione di Azure con Data Lake Storage Gen2 abilitato. Se non si dispone di un account di archiviazione, [creare un account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati - Data** > **Factory**analisi .
   
   ![Selezione di Data Factory nel riquadro Nuovo](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** specificare i valori per i campi visualizzati nell'immagine seguente: 
      
   ![Pagina Nuova factory dati](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadADLSDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Creare di nuovo la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse:** selezionare un gruppo di risorse esistente dall'elenco a discesa. È anche possibile selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
    * **Versione**: selezionare **V2**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. 

3. Selezionare **Create** (Crea).
4. Al termine della creazione, passare alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selezionare il riquadro **Autore & Monitoraggio** per avviare l'applicazione di integrazione dei dati in una scheda separata.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Caricare dati in Azure Data Lake Storage Gen2

1. Nella pagina **Guida introduttiva** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Copia riquadro dello strumento Copia dati](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** specificare **CopyFromADLSGen1ToGen2** per il campo **Nome attività.** Selezionare **Avanti**.

    ![Pagina Proprietà](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** selezionare **Crea nuova connessione**.

    ![Pagina Archivio dati di origine](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selezionare **Azure Data Lake Storage Gen1** dalla raccolta connettori e selezionare **Continua**.
    
    ![Pagina Archivio dati di origine per Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Nella pagina Specificare la connessione Gen1 di Archiviazione dati di Azure eseguire la procedura seguente:On the **Specify Azure Data Lake Storage Gen1 connection,** follow these steps:

   a. Selezionare la propria istanza di Data Lake Storage Gen1 per il nome dell'account e specificare o convalidare il **Tenant**.
  
   b. Selezionare **Test connessione** per convalidare le impostazioni. Quindi selezionare **Fine**.
  
   c. Si nota che è stata creata una nuova connessione. Selezionare **Avanti**.
   
   > [!IMPORTANT]
   > In this walk-through, you use a managed identity for Azure resources to authenticate your Azure Data Lake Storage Gen1. Per concedere all'identità gestita le autorizzazioni appropriate in Azure Data Lake Storage Gen1, seguire [queste istruzioni.](connector-azure-data-lake-store.md#managed-identity)
   
   ![Specificare un account di Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Nella pagina **Scegliere il file o la cartella** di input individuare la cartella e il file di cui si desidera copiare. Selezionare la cartella o il file e scegliere **Scegli**.

    ![Scegliere il file o la cartella di input](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Specificare il comportamento di copia selezionando le opzioni Copia file in **modo ricorsivo** e **Copia binaria.** Selezionare **Avanti**.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Nella pagina **Archivio dati** di destinazione selezionare **Crea nuova connessione** > **Azure Data Lake Storage Gen2** > **continue**.

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Nella pagina Specificare la connessione Gen2 di Archiviazione dati di Azure eseguire la procedura seguente:On the **Specify Azure Data Lake Storage Gen2 connection,** follow these steps:

   a. Selezionare l'account Data Lake Storage Gen2 in grado di accedere all'elenco a discesa **Nome account di archiviazione.**
   
   b. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.
   
   ![Specificare un account di Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Nella pagina **Scegliere il file o la cartella** di output immettere **copyfromadlsgen1** come nome della cartella di output e selezionare **Avanti**. Data Factory crea il file system Gen2 di Archiviazione di Azure Data Lake

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Nella pagina **Settings** (Impostazioni) selezionare **Next** (Avanti) per usare le impostazioni predefinite.

12. Nella pagina **Riepilogo** rivedere le impostazioni e selezionare **Avanti**.

    ![Pagina Riepilogo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Nella **pagina Distribuzione**selezionare **Monitoraggio** per monitorare la pipeline.

    ![Pagina Distribuzione](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline.

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli quali il volume di dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate.

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verificare che i dati vengano copiati nell'account Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Procedure consigliate

Per valutare l'aggiornamento da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2 in generale, vedere [Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) Nelle sezioni seguenti vengono presentate le procedure consigliate per l'utilizzo di Data Factory per un aggiornamento dei dati da Data Lake Storage Gen1 a Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partizione dati per la copia dei dati cronologiciData partition for historical data copy

- Se le dimensioni totali dei dati in Data Lake Storage Gen1 sono inferiori a 30 TB e il numero di file è inferiore a 1 milione, è possibile copiare tutti i dati in una singola esecuzione dell'attività di copia.
- Se si dispone di una maggiore quantità di dati da copiare o si desidera la flessibilità necessaria per gestire la migrazione dei dati in batch e rendere ognuno di essi completo entro un intervallo di tempo specifico, partizionare i dati. Il partizionamento riduce inoltre il rischio di problemi imprevisti.

Usare una prova di concetto per verificare la soluzione end-to-end e testare la velocità effettiva di copia nell'ambiente. Principali passaggi di proof-of-concept: 

1. Creare una pipeline di Data Factory con una singola attività di copia per copiare diversi TB di dati da Data Lake Storage Gen1 a Data Lake Storage Gen2 per ottenere una linea di base delle prestazioni di copia. Inizia con [le unità di integrazione dei dati (DIUs)](copy-activity-performance-features.md#data-integration-units) come 128. 
2. In base alla velocità effettiva di copia ottenuta nel passaggio 1, calcolare il tempo stimato necessario per l'intera migrazione dei dati. 
3. (Facoltativo) Creare una tabella di controllo e definire il filtro di file per partizionare i file di cui eseguire la migrazione. Il modo per partizionare i file è quello di: 

    - Partizionare per nome cartella o cartella con un filtro con caratteri jolly. È consigliabile adottare questa soluzione.
    - Partizione in base all'ora dell'ultima modifica di un file.

### <a name="network-bandwidth-and-storage-io"></a>Larghezza di banda di rete e I/O di archiviazioneNetwork bandwidth and storage I/O 

È possibile controllare la concorrenza dei processi di copia di Data Factory che leggono i dati da Data Lake Storage Gen1 e scrivono i dati in Data Lake Storage Gen2. In questo modo, è possibile gestire l'utilizzo di tale I/O di archiviazione per evitare di influire sul normale lavoro aziendale su Data Lake Storage Gen1 durante la migrazione.

### <a name="permissions"></a>Autorizzazioni 

In Data Factory, il [connettore Gen1](connector-azure-data-lake-store.md) di Archiviazione data lake supporta l'entità servizio e l'identità gestita per le autenticazioni delle risorse di Azure.In Data Factory, the Data Lake Storage Gen1 connector supports service principal and managed identity for Azure resource authentications. Il [connettore Gen2](connector-azure-data-lake-storage.md) di Archiviazione dati supporta la chiave dell'account, l'entità servizio e l'identità gestita per le autenticazioni delle risorse di Azure.The Data Lake Storage Gen2 connector supports account key, service principal, and managed identity for Azure resource authentications. Per rendere Data Factory in grado di esplorare e copiare tutti i file o gli elenchi di controllo di accesso (ACL) necessari, concedere autorizzazioni sufficienti per l'account fornito per accedere, leggere o scrivere tutti i file e impostare gli ACL, se lo si desidera. Concedere un ruolo di utente con privilegi avanzati o proprietario durante il periodo di migrazione. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Conservazione degli ACL da Data Lake Storage Gen1

Se si desidera replicare gli ACL insieme ai file di dati quando si esegue l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2, vedere [Conservare gli ACL da Data Lake Storage Gen1.](connector-azure-data-lake-storage.md#preserve-acls) 

### <a name="incremental-copy"></a>Copia incrementale 

È possibile utilizzare diversi approcci per caricare solo i file nuovi o aggiornati da Data Lake Storage Gen1:You can use several approaches to load only the new or updated files from Data Lake Storage Gen1:

- Caricare i file nuovi o aggiornati in base alla cartella partizionata temporale o al nome del file. Un esempio è /2019/05/13/z.
- Caricare i file nuovi o aggiornati da LastModifiedDate.Load new or updated files by LastModifiedDate.
- Identificare i file nuovi o aggiornati da qualsiasi strumento o soluzione di terze parti. Passare quindi il nome del file o della cartella alla pipeline di Data Factory tramite parametro o una tabella o un file. 

La frequenza corretta per eseguire il caricamento incrementale dipende dal numero totale di file in Azure Data Lake Storage Gen1 e dal volume di file nuovi o aggiornati da caricare ogni volta. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica dell'attività di copia Azure](copy-activity-overview.md)
> [Data Lake Storage Gen1 connettore](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)