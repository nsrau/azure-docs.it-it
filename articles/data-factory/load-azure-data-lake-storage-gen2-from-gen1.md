---
title: Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory
description: Usare Azure Data Factory per copiare dati da Azure Data Lake Storage Gen1 a Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068985"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory

Gen2 di archiviazione di Azure Data Lake è un set di capacità dedicata a analitica dei big data che viene compilato in [archiviazione Blob di Azure](../storage/blobs/storage-blobs-introduction.md). È possibile usarlo per interfacciarsi con i dati utilizzando sia paradigmi di archiviazione file system e l'oggetto.

Se attualmente si usa Azure Data Lake archiviazione Gen1, è possibile valutare Azure Data Lake Storage Gen2 copiando i dati da Data Lake archiviazione Gen1 a Gen2 usando Azure Data Factory.

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il lake con i dati da un set completo di un'istanza locale e archivi dati basati sul cloud e di risparmiare tempo quando si compilano le soluzioni di analitica. Per un elenco di connettori supportati, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre una soluzione di spostamento dei dati gestita e scale-out. A causa dell'architettura di scalabilità orizzontale di Data Factory, è possibile inserire i dati a una velocità effettiva elevata. Per altre informazioni, vedere [prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento di Data Factory copia dati per copiare dati da Azure Data Lake archiviazione Gen1 in Azure Data Lake Storage Gen2. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account Data Lake Storage Gen1 contenente dati.
* Account di archiviazione Azure con Data Lake Storage Gen2 abilitata. Se non hai un account di archiviazione [creare un account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra, selezionare **crea una risorsa** > **dati + Analitica** > **Data Factory**.
   
   ![Selezione di Data Factory nel nuovo riquadro](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nel **nuova data factory** fornire valori per i campi che vengono visualizzati nell'immagine seguente: 
      
   ![Pagina nuova Data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadADLSDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_ **ADFTutorialDataFactory**. Creare di nuovo la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: Selezionare un gruppo di risorse esistente dall'elenco a discesa. È anche possibile selezionare i **Crea nuovo** opzione e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md). 
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati da data factory possono trovarsi in altre località e aree. 

3. Selezionare **Create**.
4. Dopo aver completata la creazione, passare alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selezionare il **crea e monitora** riquadro per avviare l'applicazione di integrazione dei dati in una scheda separata.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Caricare dati in Azure Data Lake Storage Gen2

1. Nel **iniziare a usare** pagina, selezionare la **copia dati** riquadro per avviare lo strumento di copia dei dati. 

   ![Riquadro dello strumento di copia dei dati](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Nel **delle proprietà** , specificare **CopyFromADLSGen1ToGen2** per il **nome attività** campo. Selezionare **Avanti**.

    ![Pagina Proprietà](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Nel **archivio dati di origine** pagina, selezionare **+ Crea nuova connessione**.

    ![Pagina Archivio dati di origine](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selezionare **Azure Data Lake archiviazione Gen1** dalla raccolta di connettore e selezionare **continua**.
    
    ![Pagina Archivio dati di origine per Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Nel **connessione specificare Azure Data Lake archiviazione Gen1** pagina, seguire questa procedura:

   a. Selezionare la propria istanza di Data Lake Storage Gen1 per il nome dell'account e specificare o convalidare il **Tenant**.
  
   b. Selezionare **Test connessione** per convalidare le impostazioni. Selezionare quindi **Fine**.
  
   c. Noterete che è stata creata una nuova connessione. Selezionare **Avanti**.
   
   > [!IMPORTANT]
   > In questa procedura dettagliata, si usa un'identità gestita per le risorse di Azure per autenticare il Gen1 di archiviazione di Azure Data Lake. Per concedere le autorizzazioni appropriate in Azure Data Lake archiviazione Gen1 l'identità gestita, seguire [queste istruzioni](connector-azure-data-lake-store.md#managed-identity).
   
   ![Specificare un account di Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Nel **scegliere il file di input o la cartella** pagina, passare alla cartella e file che si vuole copiare. Selezionare la cartella o il file e selezionare **Choose**.

    ![Scegliere il file o la cartella di input](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Specificare il comportamento di copia selezionando le **copia i file in modo ricorsivo** e **copia binaria** opzioni. Selezionare **Avanti**.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Nel **archivio dati di destinazione** pagina, selezionare **+ Crea nuova connessione** > **Gen2 di archiviazione di Azure Data Lake**  >   **Continuare**.

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Nel **connessione specificare Azure Data Lake Storage Gen2** pagina, seguire questa procedura:

   a. Selezionare il proprio account in grado di supportare Data Lake Storage Gen2 dal **nome account di archiviazione** elenco a discesa.
   
   b. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.
   
   ![Specificare un account di Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Nel **scegliere il file di output o la cartella** pagina, immettere **copyfromadlsgen1** come nome di cartella di output, selezionare **Avanti**. Data Factory crea le sottocartelle e il sistema di file di Azure Data Lake Storage Gen2 corrispondente durante la copia se non sono presenti.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Nel **le impostazioni** pagina, selezionare **successivo** per usare le impostazioni predefinite.

12. Nel **Summary** pagina, esaminare le impostazioni e selezionare **successivo**.

    ![Pagina Riepilogo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Nel **pagina di distribuzione**, selezionare **Monitor** per monitorare la pipeline.

    ![Pagina Distribuzione](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline.

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate.

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verificare che i dati vengono copiati nell'account di archiviazione Gen2 di Azure Data Lake.

## <a name="best-practices"></a>Procedure consigliate

Per valutare l'aggiornamento da Azure Data Lake archiviazione Gen1 a Gen2 di archiviazione di Azure Data Lake in generale, vedere [esegue l'aggiornamento alle soluzioni di analitica di big data da Azure Data Lake archiviazione Gen1 per Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). Le sezioni seguenti presentano le procedure consigliate per l'uso di Data Factory per un aggiornamento di dati da Data Lake archiviazione Gen1 a Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partizione di dati per la copia dei dati cronologici

- Se le dimensioni totali dei dati in Data Lake archiviazione Gen1 sono inferiore a 30 TB e il numero di file è minore di 1 milione, è possibile copiare tutti i dati in un'attività di copia singola esecuzione.
- Se si dispone di una maggiore quantità di dati da copiare, o è necessario disporre della flessibilità per gestire la migrazione dei dati in batch e creare ciascuno di essi completato entro un intervallo di tempo specifico, è possibile partizionare i dati. Partizionamento riduce anche il rischio di eventuali problemi imprevisti.

Usare un modello di prova per verificare la soluzione end-to-end e la velocità effettiva di copia di test nell'ambiente in uso. Passaggi di proof-of-concept principali: 

1. Creare una pipeline di Data Factory con una singola attività di copia per copiare i vari terabyte di dati da Data Lake archiviazione Gen1 a Data Lake archiviazione Gen2 per ottenere una baseline delle prestazioni di copia. Per iniziare [unità di integrazione di dati (DIUs)](copy-activity-performance.md#data-integration-units) come 128. 
2. In base alla velocità effettiva di copia che si verifica nel passaggio 1, calcolare il tempo previsto necessario per la migrazione di tutti i dati. 
3. (Facoltativo) Creare una tabella di controllo e definire il filtro del file per partizionare i file per eseguire la migrazione. Il modo migliore per partizionare i file è a: 

    - Partizione dal nome della cartella o il nome di cartella con un filtro con caratteri jolly. Questo metodo è consigliato.
    - Ora dell'ultima modifica partizione da un file.

### <a name="network-bandwidth-and-storage-io"></a>/ O archiviazione e larghezza di banda di rete 

È possibile controllare la concorrenza di processi di copia di Data Factory che leggere i dati da Data Lake archiviazione Gen1 e scrivere dati in Data Lake Storage Gen2. In questo modo, è possibile gestire l'uso nel che è possibile evitare che le operazioni aziendali normale in Data Lake archiviazione Gen1 durante la migrazione dei / o l'archiviazione.

### <a name="permissions"></a>Autorizzazioni 

In Data Factory, il [connettore di Data Lake archiviazione Gen1](connector-azure-data-lake-store.md) supporta l'identità dell'entità e gestiti per le autenticazioni di risorse di Azure al servizio. Il [connettore di Data Lake Storage Gen2](connector-azure-data-lake-storage.md) supporta account key, entità servizio e identità gestita per le autenticazioni di risorse di Azure. Per rendere Data Factory in grado di passare e copia tutti i file o elenchi di controllo (ACL) è necessario, garantire alto autorizzazioni sufficienti per l'account specificato per accedere, leggere, o scrivere tutti i file e impostare gli ACL se si sceglie di accesso. Concedere un ruolo utente con privilegi avanzati o proprietario durante il periodo di migrazione. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Mantenere gli ACL da Data Lake Store Gen1

Se si vuole replicare gli ACL con file di dati durante l'aggiornamento da Data Lake archiviazione Gen1 a Data Lake Storage Gen2, vedere [conservare gli ACL da Data Lake archiviazione Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Copia incrementale 

È possibile usare diversi approcci per caricare solo i file nuovi o aggiornati da Data Lake archiviazione Gen1:

- Caricare i file nuovi o aggiornati in base al nome di file o cartella partizionato ora. Un esempio è/2019/05/13 / *.
- Caricare i file nuovi o aggiornati da LastModifiedDate & lt;.
- Identificare i file nuovi o aggiornati tramite qualsiasi strumento di terze parti o una soluzione. Passare quindi il nome file o cartella alla pipeline di Data Factory tramite parametro o una tabella o file. 

La frequenza appropriata per eseguire operazioni di caricamento incrementale varia a seconda il numero totale di file in Azure Data Lake archiviazione Gen1 e il volume di file nuovi o aggiornati per essere caricato ogni volta. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica dell'attività di copia](copy-activity-overview.md)
> [connettore di Azure Data Lake archiviazione Gen1](connector-azure-data-lake-store.md)
> [connettore Gen2 di archiviazione di Azure Data Lake](connector-azure-data-lake-storage.md)