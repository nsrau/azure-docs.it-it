---
title: Copiare dati da Azure Data Lake Storage Gen1 a Gen2
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
ms.openlocfilehash: 19b1b345c72361ae1fcdad5221dcd0e877b32fe7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926297"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory

Azure Data Lake Storage Gen2 è un set di funzionalità dedicate a Big Data Analytics integrato nell' [Archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md). È possibile usarlo per interfacciarsi con i dati usando i paradigmi di archiviazione di file system e di oggetti.

Se attualmente si usa Azure Data Lake Storage Gen1, è possibile valutare Azure Data Lake Storage Gen2 copiando i dati da Data Lake Storage Gen1 a Gen2 usando Azure Data Factory.

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il Lake con i dati di un set completo di archivi dati locali e basati sul cloud e risparmiare tempo quando si creano le soluzioni di analisi. Per un elenco dei connettori supportati, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre una soluzione di spostamento dei dati gestita e scale-out. A causa dell'architettura con scalabilità orizzontale di Data Factory, può inserire dati a una velocità effettiva elevata. Per altre informazioni, vedere [prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento copia dati Data Factory per copiare dati da Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account Data Lake Storage Gen1 contenente dati.
* Account di archiviazione di Azure con Data Lake Storage Gen2 abilitata. Se non si ha un account di archiviazione, [creare un account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **dati e analisi** > **Data Factory**.
   
   ![Selezione Data Factory nel nuovo riquadro](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **nuovo data factory** specificare i valori per i campi mostrati nell'immagine seguente: 
      
   ![Pagina nuova data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadADLSDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_ **ADFTutorialDataFactory**. Creare nuovamente il data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente dall'elenco a discesa. È anche possibile selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md). 
    * **Versione**: selezionare **V2**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. 

3. Selezionare **Create** (Crea).
4. Al termine della creazione, passare alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selezionare il riquadro **crea & monitoraggio** per avviare l'applicazione integrazione dati in una scheda separata.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Caricare dati in Azure Data Lake Storage Gen2

1. Nella pagina attività **iniziali** selezionare il riquadro **copia dati** per avviare lo strumento copia dati. 

   ![Riquadro dello strumento copia dati](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** specificare **CopyFromADLSGen1ToGen2** per il campo **nome attività** . Selezionare **Avanti**.

    ![Pagina Proprietà](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Nella pagina **archivio dati di origine** selezionare **+ Crea nuova connessione**.

    ![Pagina Archivio dati di origine](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selezionare **Azure Data Lake Storage Gen1** dalla raccolta di connettori e selezionare **Continue** (Continua).
    
    ![Pagina Archivio dati di origine per Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Nella pagina **specifica Azure Data Lake storage Gen1 connessione** eseguire le operazioni seguenti:

   a. Selezionare la propria istanza di Data Lake Storage Gen1 per il nome dell'account e specificare o convalidare il **Tenant**.
  
   b. Selezionare **Test connessione** per convalidare le impostazioni. Selezionare quindi **Fine**.
  
   c. Si noterà che è stata creata una nuova connessione. Selezionare **Avanti**.
   
   > [!IMPORTANT]
   > In questa procedura dettagliata si usa un'identità gestita per le risorse di Azure per autenticare i Azure Data Lake Storage Gen1. Per concedere all'identità gestita le autorizzazioni appropriate in Azure Data Lake Storage Gen1, seguire [queste istruzioni](connector-azure-data-lake-store.md#managed-identity).
   
   ![Specificare un account di Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Nella pagina **scegliere il file o la cartella di input** individuare la cartella e il file che si desidera copiare. Selezionare la cartella o il file e **scegliere Scegli**.

    ![Scegliere il file o la cartella di input](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Specificare il comportamento di copia selezionando le opzioni Copia i file in modo **ricorsivo** e **copia binaria** . Selezionare **Avanti**.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Nella pagina **archivio dati di destinazione** selezionare **+ crea nuova connessione** > **Azure Data Lake storage Gen2** > **continua**.

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Nella pagina **specifica Azure Data Lake storage Gen2 connessione** eseguire le operazioni seguenti:

   a. Selezionare l'account Data Lake Storage Gen2 idoneo nell'elenco a discesa **nome account di archiviazione** .
   
   b. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.
   
   ![Specificare un account di Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Nella pagina **scegliere il file o la cartella** di output, immettere **copyfromadlsgen1** come nome della cartella di output e fare clic su **Avanti**. Data Factory crea le sottocartelle e file system di Azure Data Lake Storage Gen2 corrispondenti durante la copia se non esistono.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Nella pagina **Settings** (Impostazioni) selezionare **Next** (Avanti) per usare le impostazioni predefinite.

12. Nella pagina **Riepilogo** , rivedere le impostazioni e fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Nella **pagina distribuzione**selezionare **monitoraggio** per monitorare la pipeline.

    ![Pagina Distribuzione](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline.

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare i dettagli, ad esempio il volume di dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate.

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verificare che i dati vengano copiati nell'account Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Procedure consigliate

Per valutare l'aggiornamento da Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2 in generale, vedere [aggiornare le soluzioni di analisi di Big data da Azure Data Lake storage Gen1 a Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). Le sezioni seguenti illustrano le procedure consigliate per l'uso di Data Factory per un aggiornamento dei dati da Data Lake Storage Gen1 a Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partizione dati per la copia dei dati cronologici

- Se le dimensioni totali dei dati in Data Lake Storage Gen1 sono inferiori a 30 TB e il numero di file è inferiore a 1 milione, è possibile copiare tutti i dati in una singola esecuzione dell'attività di copia.
- Se si dispone di una quantità maggiore di dati da copiare o si desidera la flessibilità necessaria per gestire la migrazione dei dati in batch e renderli tutti completi entro un intervallo di tempo specifico, partizionare i dati. Il partizionamento riduce anche il rischio di eventuali problemi imprevisti.

Usare un modello di prova per verificare la soluzione end-to-end e testare la velocità effettiva di copia nell'ambiente in uso. Passaggi principali del modello di prova: 

1. Creare una pipeline Data Factory con una singola attività di copia per copiare diversi TBs di dati da Data Lake Storage Gen1 a Data Lake Storage Gen2 per ottenere una linea di base per le prestazioni di copia. Iniziare a usare [Data Integration Unit (DIUs)](copy-activity-performance.md#data-integration-units) come 128. 
2. In base alla velocità effettiva di copia ottenibile nel passaggio 1, calcolare il tempo stimato necessario per l'intera migrazione dei dati. 
3. Opzionale Creare una tabella di controllo e definire il filtro file per partizionare i file da migrare. Per partizionare i file, è possibile: 

    - Partizione per nome cartella o nome cartella con un filtro con caratteri jolly. È consigliabile adottare questa soluzione.
    - Partizionamento in base all'ora dell'Ultima modifica di un file.

### <a name="network-bandwidth-and-storage-io"></a>Larghezza di banda di rete e I/O di archiviazione 

È possibile controllare la concorrenza dei processi di copia Data Factory che leggono i dati da Data Lake Storage Gen1 e scrivono i dati in Data Lake Storage Gen2. In questo modo, è possibile gestire l'utilizzo di tale I/O di archiviazione per evitare di influire sul normale lavoro aziendale in Data Lake Storage Gen1 durante la migrazione.

### <a name="permissions"></a>autorizzazioni 

In Data Factory, il [connettore data Lake storage Gen1](connector-azure-data-lake-store.md) supporta l'entità servizio e l'identità gestita per le autenticazioni di risorse di Azure. Il [connettore data Lake storage Gen2](connector-azure-data-lake-storage.md) supporta la chiave dell'account, l'entità servizio e l'identità gestita per le autenticazioni di risorse di Azure. Per rendere Data Factory in grado di esplorare e copiare tutti i file o gli elenchi di controllo di accesso (ACL) necessari, concedere autorizzazioni sufficienti per l'account fornito per accedere, leggere o scrivere tutti i file e impostare gli ACL se si sceglie di. Concedere a tale utente un ruolo di proprietario o utente con privilegi avanzati durante il periodo di migrazione. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Mantieni ACL da Data Lake Storage Gen1

Se si desidera replicare gli ACL insieme ai file di dati quando si esegue l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2, vedere [mantenere gli ACL da data Lake storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Copia incrementale 

È possibile usare diversi approcci per caricare solo i file nuovi o aggiornati da Data Lake Storage Gen1:

- Carica file nuovi o aggiornati per nome file o cartella partizionata in base all'ora. Un esempio è/2019/05/13/*.
- Caricare file nuovi o aggiornati da LastModifiedDate.
- Identificare i file nuovi o aggiornati da qualsiasi strumento o soluzione di terze parti. Passare quindi il nome del file o della cartella alla pipeline Data Factory tramite il parametro o una tabella o un file. 

La frequenza appropriata per il caricamento incrementale dipende dal numero totale di file in Azure Data Lake Storage Gen1 e dal volume dei file nuovi o aggiornati da caricare ogni volta. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica dell'attività di copia](copy-activity-overview.md)
> connettore di [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md)
> [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md) Connector