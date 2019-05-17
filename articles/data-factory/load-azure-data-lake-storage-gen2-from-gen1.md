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
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560645"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiare dati da Azure Data Lake Storage Gen1 a Gen2 con Azure Data Factory

Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data e integrate in [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md). Consente di interagire con i dati approfittando dei paradigmi sia del file system che dell'archiviazione di oggetti.

Se attualmente si usa Azure Data Lake Storage Gen1, è possibile valutare la nuova funzionalità Gen2 copiando i dati da Data Lake Storage Gen1 a Gen2 usando Azure Data Factory.

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il lake con i dati provenienti da un set completo di archivi dati locali e basati sul cloud e risparmiare tempo durante la compilazione di soluzioni di analisi. Per un elenco dettagliato di connettori supportati, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre una soluzione di spostamento dei dati gestita e scale-out. ADF, grazie all'architettura scale-out, può inserire i dati a una velocità effettiva elevata. Per informazioni, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per copiare dati da _Azure Data Lake Storage Gen1_ in _Azure Data Lake Storage Gen2_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account Data Lake Storage Gen1 contenente dati.
* Account di Archiviazione di Azure con Data Lake Storage Gen2 abilitato: Se non hai un account di archiviazione [creare un account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**:
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente: 
      
   ![Pagina Nuova data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadADLSDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. 

3. Selezionare **Create**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Caricare dati in Azure Data Lake Storage Gen2

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati: 

   ![Riquadro dello strumento Copia dati](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** specificare **CopyFromADLSGen1ToGen2** per il campo **Nome attività**, quindi selezionare **Avanti**:

    ![Pagina Proprietà](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** fare clic su **+ Crea nuova connessione**:

    ![Pagina Archivio dati di origine](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    Selezionare **Azure Data Lake Storage Gen1** dalla raccolta di connettori e selezionare **Continua**
    
    ![Pagina Archivio dati di origine per Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. Nella pagina delle **connessioni per Azure Data Lake Storage Gen1** seguire questa procedura:
   1. Selezionare la propria istanza di Data Lake Storage Gen1 per il nome dell'account e specificare o convalidare il **Tenant**.
   2. Fare clic su **Connessione di test** per convalidare le impostazioni, quindi selezionare **Fine**.
   3. Si noterà che viene creata una nuova connessione. Selezionare **Avanti**.
   
   > [!IMPORTANT]
   > In questa procedura dettagliata si usa un'identità gestita per le risorse di Azure per autenticare l'istanza di Data Lake Storage Gen1. Assicurarsi di concedere le autorizzazioni appropriate all'entità del servizio gestita in Azure Data Lake Storage Gen1 seguendo [queste istruzioni](connector-azure-data-lake-store.md#managed-identity).
   
   ![Specificare un account di Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) passare alla cartella e al file da copiare. Selezionare la cartella o il file e selezionare **Scegli**:

    ![Scegliere il file o la cartella di input](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Specificare il comportamento di copia selezionando le opzioni **Copia i file in modo ricorsivo** e **Copia binaria**. Selezionare **Avanti**:

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. Nella pagina **Archivio dati di destinazione** fare clic su **+ Crea nuova connessione**, quindi selezionare **Data Lake Storage Gen2** e **Continua**:

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. Nella pagina delle **connessioni per Azure Data Lake Storage Gen2** seguire questa procedura:

   1. Selezionare l'account con supporto di Data Lake Storage Gen2 nell'elenco a discesa "Nome account di archiviazione".
   2. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.
   
   ![Specificare un account di Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. Nel **scegliere il file di output o la cartella** pagina, immettere **copyfromadlsgen1** come nome di cartella di output, selezionare **Avanti**. Azure Data factory creerà il sistema di file di Azure Data Lake Store Gen2 corrispondente e le sottocartelle durante la copia, se non esiste.

    ![Specificare la cartella di output](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. Nella pagina **Impostazioni** selezionare **Avanti** per usare le impostazioni predefinite.

11. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**:

    ![Pagina Riepilogo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline:

    ![Pagina Distribuzione](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline:

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate:

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Verificare che i dati vengano copiati nell'account Data Lake Storage Gen2.

## <a name="best-practices"></a>Procedure consigliate

Per valutare l'aggiornamento da Azure Data Lake Storage (ADLS) Gen1 a Gen2 in generale, fare riferimento a [esegue l'aggiornamento alle soluzioni di analitica di big data da Azure Data Lake archiviazione Gen1 per Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). Le sezioni seguenti presentano le procedure consigliate dell'uso di Azure Data factory per l'aggiornamento dei dati da Gen1 a Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partizione di dati per la copia dei dati cronologici

- Se le dimensioni totali dei dati in Azure Data Lake Store Gen1 minore **30TB** e il numero di file è minore di **1 milione**, è possibile copiare tutti i dati in esecuzione singola attività di copia.
- Se si dispone di dimensioni maggiori di dati da copiare, o si vuole che la flessibilità necessaria per gestire la migrazione dei dati in batch e rendere ognuno di essi completata all'interno di windows un intervallo specifico, vengono suggeriti per partizionare i dati, nel qual caso può anche ridurre il rischio di qualsiasi iss imprevisto UE.

Un PoC (Proof of Concept) è fortemente consigliato per verificare la soluzione end-to-end e la velocità effettiva di copia di test nell'ambiente in uso. Passaggi principali di questo modello di verifica: 

1. Creare una pipeline di Azure Data factory con una singola attività di copia per copiare i vari terabyte di dati da ADLS Gen1 a Gen2 Azure Data Lake Store per ottenere una linea di base delle prestazioni di copia, a partire [unità di integrazione di dati (DIUs)](copy-activity-performance.md#data-integration-units) come 128. 
2. In base alla velocità effettiva di copia che si verifica nel passaggio #1, calcolare il tempo stimato necessario per la migrazione di tutti i dati. 
3. (Facoltativo) Creare una tabella di controllo e definire il filtro del file per partizionare i file per eseguire la migrazione. Il modo migliore per partizionare i file come indicato di seguito: 

    - Partizionati dal nome della cartella o il nome di cartella con il filtro con caratteri jolly (consigliato) 
    - Partizionata in base l'ora dell'ultima modifica del file 

### <a name="network-bandwidth-and-storage-io"></a>/ O archiviazione e larghezza di banda di rete 

È possibile controllare la concorrenza di processi di copia ADF che leggere i dati da ADLS Gen1 e Gen2 Azure Data Lake Store, scrivere i dati in modo che è possibile gestire l'utilizzo in archiviazione dei / o per non compromettere le operazioni aziendali normale in Azure Data Lake Store Gen1 durante la migrazione.

### <a name="permissions"></a>Autorizzazioni 

In Data Factory [connettore di Azure Data Lake Store Gen1](connector-azure-data-lake-store.md) supporta entità servizio e identità gestita per le autenticazioni di risorse di Azure; [Connettore di Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) supporta, chiave dell'account dell'entità servizio e l'identità gestita per le autenticazioni di risorse di Azure. Per rendere Data Factory in grado di passare e copia che tutti i file/gli ACL in base alle esigenze, assicurarsi di che concedere alto delle autorizzazioni sufficienti per l'account fornire a accesso/lettura/scrittura a tutti i file e impostare gli ACL se si sceglie di. Suggerisci per concedere a tale ruolo super utenti/proprietario durante il periodo di migrazione. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Mantenere gli ACL da Data Lake Store Gen1

Se si vuole replicare gli ACL con file di dati durante l'aggiornamento da Data Lake archiviazione Gen1 a Gen2, fare riferimento a [conservare gli ACL da Data Lake archiviazione Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Copia incrementale 

Diversi approcci sono utilizzabile per caricare solo i file nuovi o aggiornati da Azure Data Lake Store Gen1:

- Caricare i file nuovi o aggiornati da tempo partizionato nome file o cartella, ad esempio/2019/05/13 / *;
- Caricare i file nuovi o aggiornati da LastModifiedDate & gt;
- Identificare i file nuovi o aggiornati in qualsiasi strumento o soluzione di terze parti 3rd, quindi passare il nome file o cartella alla pipeline di Azure Data factory tramite parametro o un tabella/file.  

La frequenza appropriata per eseguire operazioni di caricamento incrementale varia a seconda il numero totale di file in Azure Data Lake Store Gen1 e il volume del file nuovi o aggiornati per essere caricato ogni volta.  

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica dell'attività di copia](copy-activity-overview.md)
> [connettore di Azure Data Lake archiviazione Gen1](connector-azure-data-lake-store.md)
> [connettore Gen2 di archiviazione di Azure Data Lake](connector-azure-data-lake-storage.md)