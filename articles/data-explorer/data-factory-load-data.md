---
title: Copiare dati da Azure Data Factory ad Azure Esplora dati
description: In questo argomento si apprenderà come inserire (caricare) i dati in Azure Esplora dati usando Azure Data Factory strumento di copia
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb05df7ed97839ef80798a752565234d180f0e2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268833"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Copiare i dati in Esplora dati di Azure usando Azure Data Factory 

Azure Esplora dati è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale su grandi volumi di flussi di dati da molte origini, ad esempio applicazioni, siti Web e dispositivi Internet. Esplorare i dati in modo iterativo e identificare modelli e anomalie per migliorare i prodotti, migliorare l'esperienza dei clienti, monitorare i dispositivi e potenziare le operazioni. Esplora nuove domande e ottieni risposte in pochi minuti. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il database di Esplora dati di Azure con i dati del sistema esistente e risparmiare tempo durante la creazione delle soluzioni di analisi.

Azure Data Factory offre i vantaggi seguenti per il caricamento di dati in Azure Esplora dati:

* **Facilità di configurazione**: Procedura guidata intuitiva in cinque passaggi senza necessità di script.
* **Supporto per gli archivi dati avanzati**: Supporto incorporato per un set completo di archivi dati locali e basati sul cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sicuro e conforme**: I dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni elevate**: Velocità di caricamento dei dati fino a 1 GB/s in Esplora dati di Azure. Per informazioni, vedere [Prestazioni dell'attività di copia](/azure/data-factory/copy-activity-performance).

Questo articolo illustra come usare lo strumento Data Factory Copia dati per caricare dati da Amazon S3 in Azure Esplora dati. È possibile seguire una procedura simile per copiare dati da altri archivi dati, ad esempio [archiviazione BLOB di Azure](/azure/data-factory/connector-azure-blob-storage), [database SQL di Azure](/azure/data-factory/connector-azure-sql-database), [Azure SQL data warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)e [file System](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Azure Esplora dati](create-cluster-database-portal.md)
* Origine dati.

## <a name="create-a-data-factory"></a>Crea una data factory

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale >**Data Factory**di **analisi** > .

   ![Creare una nuova data factory](media/data-factory-load-data/create-adf.png)

1. Nella pagina **nuovo data factory** specificare i valori per i campi seguenti e quindi selezionare **Crea**.

    ![Pagina Nuova data factory](media/data-factory-load-data/my-new-data-factory.png)

    **Impostazione**  | **Descrizione campo**
    |---|---|
    | **Name** | Immettere un nome univoco globale per la data factory. Se viene visualizzato l'errore *"il nome \"della data\" Factory LoadADXDemo non è disponibile"* , immettere un nome diverso per il data factory. Per le regole di denominazione degli elementi di Data Factory, vedere [Data Factory regole di denominazione](/azure/data-factory/naming-rules).|
    | **Sottoscrizione** | selezionare la sottoscrizione di Azure in cui creare la data factory. |
    | **Gruppo di risorse** | Selezionare **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. Selezionare **Usa esistente**, se si dispone di un gruppo di risorse esistente. |
    | **Versione** | Selezionare **V2**. |
    | **Location** | Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati utilizzati da data factory possono trovarsi in altre località o aree. |
    | | |

1. Selezionare notifiche sulla barra degli strumenti per monitorare il processo di creazione. Al termine della creazione, passare al data factory creato. Verrà visualizzata la home page **Data Factory** .

   ![Home page di Data factory](media/data-factory-load-data/data-factory-home-page.png)

1. Selezionare il riquadro **crea & monitoraggio** per avviare l'applicazione in una scheda separata.

## <a name="load-data-into-azure-data-explorer"></a>Caricare i dati in Azure Esplora dati

I dati possono essere caricati da molti tipi di [archivi dati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Esplora dati di Azure. In questo argomento vengono illustrati in dettaglio il caricamento di dati da Amazon S3.

Esistono due modi per caricare i dati in Azure Esplora dati usando Azure Data Factory:

* Interfaccia utente Azure Data Factory- [scheda **autore**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory strumento **copia dati** ](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) usato in questo articolo.

### <a name="copy-data-from-amazon-s3-source"></a>Copiare dati da Amazon S3 (origine)

1. Nella pagina attività **iniziali** selezionare il riquadro **copia dati** per avviare lo strumento copia dati.

   ![Riquadro dello strumento copia dati](media/data-factory-load-data/copy-data-tool-tile.png)

1. Nella pagina **Proprietà** specificare il **nome dell'attività** e selezionare **Avanti**.

    ![Copia dalle proprietà di origine](media/data-factory-load-data/copy-from-source.png)

1. Nella pagina **archivio dati di origine** fare clic su **+ Crea nuova connessione**.

    ![Creazione connessione dei dati di origine](media/data-factory-load-data/source-create-connection.png)

1. Selezionare **Amazon S3**, quindi selezionare **continua** .

    ![Nuovo servizio collegato](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Nella pagina **New Linked Service (Amazon S3)** seguire questa procedura:

    ![Specificare il servizio collegato Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Specificare il **nome** del nuovo servizio collegato.
    * Selezionare **Connetti tramite** il valore di Integration runtime dall'elenco a discesa.
    * Specificare il valore **ID della chiave di accesso**.
    * Specificare il valore **Chiave di accesso segreta**.
    * Selezionare **Test connessione** per testare la connessione al servizio collegato creata.
    * Selezionare **Fine**.
    
    > [!NOTE]
    > In Amazon S3 selezionare il nome utente Amazon nella barra di spostamento e quindi selezionare le **credenziali di sicurezza personali** per individuare la **chiave di accesso**. 

1. Nella pagina **archivio dati di origine** verrà visualizzata la nuova connessione AmazonS31. Selezionare **Avanti**.

   ![Connessione creata dall'archivio dati di origine](media/data-factory-load-data/source-data-store-created-connection.png)

1. Nella pagina **scegliere il file o la cartella di input** :

    1. Individuare la cartella o il file che si desidera copiare. Selezionare la cartella o il file.
    1. Selezionare il comportamento di copia secondo le necessità. Mantieni **copia binaria** deselezionata.
    1. Selezionare **Avanti**.

    ![Scegliere il file o la cartella di input](media/data-factory-load-data/source-choose-input-file.png)

1. Nella pagina **Impostazioni formati file** selezionare le impostazioni rilevanti per il file e fare clic su **Avanti**.

   ![Scegliere il file o la cartella di input](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiare i dati in Esplora dati di Azure (destinazione)

Viene creato il nuovo servizio collegato di Azure Esplora dati per copiare i dati nella tabella di destinazione Azure Esplora dati (sink) specificata di seguito.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Creare il servizio collegato di Azure Esplora dati

1. Nella pagina **archivio dati di destinazione** è possibile usare una connessione all'archivio dati esistente o specificare un nuovo archivio dati facendo clic su **+ Crea nuova connessione**.

    ![Pagina dell'archivio dati di destinazione](media/data-factory-load-data/destination-create-connection.png)

1. Nella pagina **New Linked Service (nuovo servizio collegato** ) selezionare **Azure Esplora dati**e quindi selezionare **continue (continua** ).

    ![Selezionare Esplora dati di Azure-nuovo servizio collegato](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Nella pagina **New Linked Service (Azure Esplora dati)** seguire questa procedura:

    ![ADX nuovo servizio collegato](media/data-factory-load-data/adx-new-linked-service.png)

   * Selezionare il **nome** per il servizio collegato Azure Esplora dati.
   * In **metodo di selezione dell'account**: 
        * Selezionare il pulsante **di opzione dalla sottoscrizione di Azure** e selezionare l'account della **sottoscrizione di Azure** . Selezionare quindi il **cluster**. Si noti che nell'elenco a discesa vengono elencati solo i cluster che appartengono all'utente.
        * In alternativa, selezionare il pulsante di opzione **Immetti manualmente** e immettere l' **endpoint**.
    * Specificare il **tenant**.
    * Immettere l' **ID dell'entità servizio**.
    * Selezionare il pulsante **chiave entità servizio** e immettere la **chiave dell'entità servizio**.
    * Selezionare il **database** dal menu a discesa. In alternativa, selezionare **modifica** casella di controllo e immettere il nome del database.
    * Selezionare **Test connessione** per testare la connessione al servizio collegato creata. Se è possibile connettersi alla configurazione, verrà visualizzata una connessione di segno di spunta verde con **esito positivo** .
    * Selezionare **fine** per completare la creazione del servizio collegato.

    > [!NOTE]
    > L'entità servizio viene usata da Azure Data Factory per accedere al servizio Esplora dati di Azure. Per l'entità servizio, [creare un'entità servizio Azure Active Directory (Azure ad)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Non usare il metodo **Azure Key Vault** .

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configurare la connessione dati di Azure Esplora dati

1. Verrà aperto l' **archivio dati di destinazione** . La connessione dati di Azure Esplora dati creata è disponibile per l'uso. Selezionare **Avanti** per configurare la connessione.

    ![Archivio dati di destinazione ADX](media/data-factory-load-data/destination-data-store.png)

1. In **mapping tabella**impostare il nome della tabella di destinazione e selezionare **Avanti**.

    ![Mapping tabella del set di dati di destinazione](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Nella pagina **Mapping colonne** :
    * Il primo mapping viene eseguito da ADF in base al [mapping dello schema ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Impostare i **mapping delle colonne** per la tabella di destinazione Azure Data Factory. Il mapping predefinito viene visualizzato dalla tabella di destinazione origine a ADF.
        * Deselezionare le colonne che non sono necessarie per definire il mapping delle colonne.
    * Il secondo mapping si verifica quando questi dati tabulari vengono inseriti in Esplora dati di Azure. Il mapping viene eseguito in base alle [regole di mapping CSV](/azure/kusto/management/mappings#csv-mapping). Si noti che anche se i dati di origine non sono in formato CSV, ADF ha convertito i dati in un formato tabulare, pertanto il mapping CSV è l'unico mapping pertinente in questa fase.
        * In **Proprietà sink Esplora dati (kusto) di Azure** aggiungere il **nome del mapping** di inserimento pertinente (facoltativo) in modo da poter utilizzare il mapping delle colonne.
        * Se non si specifica il **nome del mapping** di inserimento, si verificherà l'ordine di mapping "per nome" definito nella sezione **Mapping colonne** . Se il mapping "per nome" ha esito negativo, Azure Esplora dati tenterà di inserire i dati in un ordine "per colonna" (per impostazione predefinita esegue il mapping in base alla posizione).
    * Selezionare **Avanti**

    ![Mapping delle colonne del set di dati di destinazione](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Nella pagina **Impostazioni** :
    * Impostare le impostazioni relative alla **tolleranza di errore**.
    * **Impostazioni delle prestazioni**: L'abilitazione della gestione temporanea non è applicabile. **Le impostazioni avanzate** includono considerazioni sui costi. Lasciare invariata se non sono presenti esigenze specifiche.
    * Selezionare **Avanti**.

    ![Copiare le impostazioni dei dati](media/data-factory-load-data/copy-data-settings.png)

1. In **Riepilogo**esaminare le impostazioni e fare clic su **Avanti**.

    ![Copia riepilogo dati](media/data-factory-load-data/copy-data-summary.png)

1. Nella **pagina distribuzione**:
    * Selezionare **monitoraggio** per passare alla scheda **monitoraggio** e visualizzare lo stato della pipeline (stato, errori e flusso di dati).
    * Selezionare **modifica pipeline** per modificare servizi collegati, set di impostazioni e pipeline.
    * Selezionare **fine** per completare l'attività copia dati

    ![Pagina Distribuzione](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [connettore Azure Esplora dati](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.

* Altre informazioni sulla modifica di servizi collegati, set di impostazioni e pipeline nell' [interfaccia utente di data factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Informazioni sulle [query di Azure Esplora dati](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.
