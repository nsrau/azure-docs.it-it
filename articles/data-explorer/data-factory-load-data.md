---
title: Copiare dati da Azure Data Factory ad Azure Esplora dati
description: Questo articolo illustra come inserire (caricare) i dati in Azure Esplora dati usando lo strumento Azure Data Factory Copy.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300590"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Copiare i dati in Esplora dati di Azure usando Azure Data Factory 

Azure Esplora dati è un servizio di analisi dei dati veloce e completamente gestito. Offre analisi in tempo reale su grandi volumi di dati trasmessi da numerose origini, ad esempio applicazioni, siti Web e dispositivi Internet. Con Azure Esplora dati è possibile esplorare in modo iterativo i dati e identificare modelli e anomalie per migliorare i prodotti, migliorare l'esperienza dei clienti, monitorare i dispositivi e aumentare le operazioni. Consente di esplorare le nuove domande e ottenere risposte in pochi minuti. 

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usarlo per popolare il database Esplora dati di Azure con i dati del sistema esistente. Può aiutarti a risparmiare tempo durante la compilazione di soluzioni di analisi.

Quando si caricano dati in Esplora dati di Azure, Data Factory offre i seguenti vantaggi:

* **Facile installazione**: Consente di ottenere una procedura guidata intuitiva in cinque passaggi senza richiedere script.
* **Supporto per gli archivi dati avanzati**: Ottieni supporto incorporato per un set completo di archivi dati locali e basati sul cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sicuro e conforme**: I dati vengono trasferiti tramite HTTPS o Azure ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni elevate**: La velocità di caricamento dei dati è fino a 1 gigabyte al secondo (GBps) in Azure Esplora dati. Per altre informazioni, vedere [prestazioni dell'attività di copia](/azure/data-factory/copy-activity-performance).

In questo articolo si usa lo strumento Data Factory Copia dati per caricare i dati dal servizio di archiviazione semplice di Amazon (S3) in Azure Esplora dati. È possibile seguire un processo simile per copiare dati da altri archivi dati, ad esempio:
* [Archivio BLOB di Azure](/azure/data-factory/connector-azure-blob-storage)
* [Database SQL di Azure](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [File system](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Esplora dati di Azure](create-cluster-database-portal.md).
* Origine dati.

## <a name="create-a-data-factory"></a>Crea una data factory

1. Accedere al [portale di Azure](https://ms.portal.azure.com).

1. Nel riquadro sinistro selezionare **Crea una risorsa** > **analisi** > **Data Factory**.

   ![Creare una data factory nel portale di Azure](media/data-factory-load-data/create-adf.png)

1. Nel riquadro **nuovo data factory** specificare i valori per i campi nella tabella seguente:

   ![Riquadro "nuovo data factory"](media/data-factory-load-data/my-new-data-factory.png)  

   | Impostazione  | Valore da immettere  |
   |---|---|
   | **Name** | Nella casella Immettere un nome univoco globale per la data factory. Se viene visualizzato un errore, il *nome \"della data factory LoadADXDemo\" non è disponibile*, immettere un nome diverso per il data factory. Per le regole sulla denominazione di elementi Data Factory, vedere [Data Factory regole di denominazione](/azure/data-factory/naming-rules).|
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione di Azure in cui creare la data factory. |
   | **Gruppo di risorse** | Selezionare **Crea nuovo**e quindi immettere il nome di un nuovo gruppo di risorse. Se si dispone già di un gruppo di risorse, selezionare **Usa esistente**. |
   | **Versione** | Nell'elenco a discesa selezionare **v2**. |  
   | **Location** | Nell'elenco a discesa selezionare il percorso per il data factory. Nell'elenco vengono visualizzati solo i percorsi supportati. Gli archivi dati utilizzati dalla data factory possono esistere in altre posizioni o aree. |

1. Selezionare **Create**.

1. Per monitorare il processo di creazione, selezionare **notifiche** sulla barra degli strumenti. Dopo aver creato la data factory, selezionarla.
   
   Viene visualizzato il riquadro **Data Factory** .

   ![Riquadro Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Per aprire l'applicazione in un riquadro separato, selezionare il riquadro **autore & monitoraggio** .

## <a name="load-data-into-azure-data-explorer"></a>Caricare i dati in Azure Esplora dati

È possibile caricare dati da molti tipi di [archivi dati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Esplora dati di Azure. Questo articolo illustra come caricare dati da Amazon S3.

È possibile caricare i dati in uno dei modi seguenti:

* Nel riquadro sinistro dell'interfaccia utente di Azure Data Factory selezionare l'icona **autore** , come illustrato nella sezione "creare una data factory" di [creare una data factory usando l'interfaccia utente di Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* Nello strumento Azure Data Factory Copia dati, come illustrato in [usare lo strumento copia dati per copiare i dati](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Copiare dati da Amazon S3 (origine)

1. Nel riquadro attività **iniziali** aprire lo strumento copia dati selezionando **copia dati**.

   ![Pulsante strumento Copia dati](media/data-factory-load-data/copy-data-tool-tile.png)

1. Nella casella **nome attività** del riquadro **Proprietà** immettere un nome e quindi fare clic su **Avanti**.

    ![Riquadro proprietà Copia dati](media/data-factory-load-data/copy-from-source.png)

1. Nel riquadro **archivio dati di origine** selezionare **Crea nuova connessione**.

    ![Il Copia dati riquadro "archivio dati di origine"](media/data-factory-load-data/source-create-connection.png)

1. Selezionare **Amazon S3**, quindi selezionare **continua**.

    ![Il nuovo riquadro servizio collegato](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Nel riquadro **nuovo servizio collegato (Amazon S3)** eseguire le operazioni seguenti:

    ![Specificare il servizio collegato Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Nella casella **nome** immettere il nome del nuovo servizio collegato.

    b. Nell'elenco a discesa **Connetti tramite Integration Runtime** selezionare il valore.

    c. Nella casella **ID chiave di accesso** immettere il valore.
    
    > [!NOTE]
    > In Amazon S3, per individuare la chiave di accesso, selezionare il nome utente Amazon nella barra di spostamento e quindi selezionare le **credenziali di sicurezza personali**.
    
    d. Nella casella **chiave di accesso Secret** immettere un valore.

    e. Per testare la connessione al servizio collegato creata, selezionare **Test connessione**.

    f. Selezionare **Fine**.
    
      Il riquadro **archivio dati di origine** Visualizza la nuova connessione AmazonS31. 

1. Selezionare **Avanti**.

   ![Connessione creata dall'archivio dati di origine](media/data-factory-load-data/source-data-store-created-connection.png)

1. Nel riquadro **scegliere il file o la cartella di input** eseguire le operazioni seguenti:

    a. Individuare il file o la cartella che si desidera copiare e quindi selezionarlo.

    b. Selezionare il comportamento di copia desiderato. Verificare che la casella di controllo **copia binaria** sia deselezionata.

    c. Selezionare **Avanti**.

    ![Scegliere il file o la cartella di input](media/data-factory-load-data/source-choose-input-file.png)

1. Nel riquadro **Impostazioni formato file** selezionare le impostazioni appropriate per il file. quindi selezionare **Avanti**.

   ![Riquadro "Impostazioni formato file"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiare i dati in Esplora dati di Azure (destinazione)

Viene creato il nuovo servizio collegato di Azure Esplora dati per copiare i dati nella tabella di destinazione Azure Esplora dati (sink) specificata in questa sezione.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Creare il servizio collegato di Azure Esplora dati

Per creare il servizio collegato di Azure Esplora dati, eseguire le operazioni seguenti:

1. Per usare una connessione all'archivio dati esistente o specificare un nuovo archivio dati, nel riquadro **archivio dati di destinazione** selezionare **Crea nuova connessione**.

    ![Riquadro archivio dati di destinazione](media/data-factory-load-data/destination-create-connection.png)

1. Nel riquadro **nuovo servizio collegato** selezionare **Azure Esplora dati**e quindi fare clic su **continua**.

    ![Il nuovo riquadro servizio collegato](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Nel riquadro **nuovo servizio collegato (Azure Esplora dati)** eseguire le operazioni seguenti:

    ![Riquadro nuovo servizio collegato di Azure Esplora dati](media/data-factory-load-data/adx-new-linked-service.png)

   a. Nella casella **nome** immettere un nome per il servizio collegato Azure Esplora dati.

   b. In **metodo di selezione account**effettuare una delle operazioni seguenti: 

    * Selezionare una **sottoscrizione di Azure** e quindi negli elenchi a discesa selezionare la **sottoscrizione di Azure** e il **cluster**. 

        > [!NOTE]
        > Il controllo a discesa **cluster** elenca solo i cluster associati alla sottoscrizione.

    * Selezionare **invio manualmente**, quindi immettere l' **endpoint**.

   c. Nella casella **tenant** immettere il nome del tenant.

   d. Nella casella **ID entità servizio** immettere l'ID dell'entità servizio.

   e. Selezionare **chiave entità servizio** e quindi immettere il valore della chiave nella casella **chiave entità servizio** .

   f. Nell'elenco a discesa **database** selezionare il nome del database. In alternativa, selezionare la casella di controllo **modifica** , quindi immettere il nome del database.

   g. Per testare la connessione al servizio collegato creata, selezionare **Test connessione**. Se è possibile connettersi al servizio collegato, nel riquadro viene visualizzato un segno di spunta verde e un messaggio di **connessione riuscito** .

   h. Selezionare **fine** per completare la creazione del servizio collegato.

    > [!NOTE]
    > L'entità servizio viene usata da Azure Data Factory per accedere al servizio Esplora dati di Azure. Per creare un'entità servizio, passare a [creare un'entità servizio Azure Active Directory (Azure ad)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Non usare il metodo Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configurare la connessione dati di Azure Esplora dati

Dopo aver creato la connessione al servizio collegato, viene aperto il riquadro **archivio dati di destinazione** e la connessione creata è disponibile per l'uso. Per configurare la connessione, eseguire le operazioni seguenti:

1. Selezionare **Avanti**.

    ![Riquadro "archivio dati di destinazione" di Azure Esplora dati](media/data-factory-load-data/destination-data-store.png)

1. Nel riquadro **mapping tabella** impostare il nome della tabella di destinazione e quindi fare clic su **Avanti**.

    ![Riquadro "mapping tabella" del set di dati di destinazione](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Nel riquadro **Mapping colonne** viene eseguita la mappatura seguente:

    a. Il primo mapping viene eseguito dal Azure Data Factory in base al [mapping dello schema di Azure Data Factory](/azure/data-factory/copy-activity-schema-and-type-mapping). Eseguire le operazioni seguenti:

    * Impostare i **mapping delle colonne** per la tabella di destinazione Azure Data Factory. Il mapping predefinito viene visualizzato dall'origine alla tabella di destinazione Azure Data Factory.

    * Annulla la selezione delle colonne non necessarie per definire il mapping della colonna.

    b. Il secondo mapping si verifica quando questi dati tabulari vengono inseriti in Esplora dati di Azure. Il mapping viene eseguito in base alle [regole di mapping CSV](/azure/kusto/management/mappings#csv-mapping). Anche se i dati di origine non sono in formato CSV, Azure Data Factory converte i dati in un formato tabulare. Il mapping CSV è pertanto l'unico mapping pertinente in questa fase. Eseguire le operazioni seguenti:

    * Opzionale In **Proprietà sink Esplora dati (kusto) di Azure**aggiungere il **nome del mapping** di inserimento pertinente in modo che il mapping delle colonne possa essere utilizzato.

    * Se non si specifica il **nome del mapping** di inserimento, verrà utilizzato l'ordine *di mapping per nome* definito nella sezione **Mapping colonne** . Se il mapping *in base al nome* ha esito negativo, Azure Esplora dati tenta di inserire i dati in un ordine *di posizione per colonna* , ovvero viene mappato per posizione come impostazione predefinita.

    * Selezionare **Avanti**.

    ![Riquadro "mapping colonne" del set di dati di destinazione](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Nel riquadro **Impostazioni** eseguire le operazioni seguenti:

    a. In **Impostazioni tolleranza di errore**immettere le impostazioni appropriate.

    b. In **Impostazioni prestazioni**, **Abilita gestione temporanea** non si applica e **le impostazioni avanzate** includono considerazioni sui costi. Se non si dispone di requisiti specifici, lasciare invariate le impostazioni.

    c. Selezionare **Avanti**.

    ![Riquadro "Impostazioni" copia dati](media/data-factory-load-data/copy-data-settings.png)

1. Nel riquadro **Riepilogo** esaminare le impostazioni e quindi fare clic su **Avanti**.

    ![Riquadro "Riepilogo" della copia dati](media/data-factory-load-data/copy-data-summary.png)

1. Nel riquadro **distribuzione completata** eseguire le operazioni seguenti:

    a. Per passare alla scheda **monitoraggio** e visualizzare lo stato della pipeline, ovvero avanzamento, errori e flusso di dati, selezionare **monitoraggio**.

    b. Per modificare servizi collegati, set di impostazioni e pipeline, selezionare **modifica pipeline**.

    c. Selezionare **fine** per completare l'attività copia dati.

    ![Riquadro "distribuzione completata"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [connettore Azure Esplora dati](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.
* Altre informazioni sulla modifica di servizi collegati, set di impostazioni e pipeline nell' [interfaccia utente di data factory](/azure/data-factory/quickstart-create-data-factory-portal).
* Informazioni sulle [query di Azure Esplora dati](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.
