---
title: Copiare dati da Azure Data Factory in Esplora dati di Azure
description: In questo argomento viene illustrato come inserire (caricare) i dati in Esplora dati di Azure usando lo strumento di copia di Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 2142fbf03daa6667b20db43f9212a2b5e6d7dd44
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657524"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Copiare i dati in Esplora dati di Azure usando Azure Data Factory 

Esplora i dati di Azure è un servizio di analitica dei dati rapida e completamente gestito per l'analisi in tempo reale su grandi volumi di dati di streaming da molte origini, ad esempio applicazioni, siti Web e dispositivi IoT. Esplorare i dati in modo iterativo e identificare modelli e anomalie per migliorare i prodotti, migliora le esperienze dei clienti, monitorare i dispositivi, potrai migliorare e operazioni. Esplora nuove domande e ottieni risposte in pochi minuti. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il database di Esplora dati di Azure con i dati dal sistema esistente e risparmiare tempo durante la compilazione di soluzioni di analitica.

Azure Data Factory offre i vantaggi seguenti per il caricamento dei dati in Esplora dati di Azure:

* **Facilità di configurazione**: Una cinque passaggi procedura guidata intuitiva senza necessità di script.
* **Supporto di archiviare i dati dettagliati**: Supporto incorporato per una vasta gamma di archivi dati basati sul cloud e locali. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sicurezza e conformità**: I dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni elevate**: Fino a velocità di caricamento dei dati di 1 GB/s in Esplora dati di Azure. Per informazioni, vedere [Prestazioni dell'attività di copia](/azure/data-factory/copy-activity-performance).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per caricare i dati da Amazon S3 in Esplora dati di Azure. È possibile seguire la procedura per copiare dati da altri archivi dati, ad esempio [archiviazione Blob di Azure](/azure/data-factory/connector-azure-blob-storage), [Database SQL di Azure](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), e [File system](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster di Esplora dati di Azure e un database](create-cluster-database-portal.md)
* Origine dati.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Selezionare il **crea una risorsa** pulsante (+) nell'angolo superiore sinistro del portale > **Analitica** > **Data Factory**.

   ![Creare una nuova data factory](media/data-factory-load-data/create-adf.png)

1. Nel **nuova data factory** pagina, specificare i valori per i campi seguenti e quindi selezionare **crea**.

    ![Pagina Nuova data factory](media/data-factory-load-data/my-new-data-factory.png)

    **Impostazione**  | **Descrizione campo**
    |---|---|
    | **Nome** | Immettere un nome univoco globale per la data factory. Se viene visualizzato l'errore *"nome della Data factory \"LoadADXDemo\" non è disponibile"* , immettere un nome diverso per la data factory. Per le regole di denominazione di elementi di Data Factory, vedere [regole di denominazione di Data Factory](/azure/data-factory/naming-rules).|
    | **Sottoscrizione** | selezionare la sottoscrizione di Azure in cui creare la data factory. |
    | **Gruppo di risorse** | Selezionare **Crea nuovo** e immettere il nome del nuovo gruppo di risorse. Selezionare **Usa esistente**, se si dispone di un gruppo di risorse. |
    | **Versione** | Selezionare **V2**. |
    | **Location** | Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati utilizzati da data factory possono essere in altre posizioni o aree geografiche. |
    | | |

1. Selezionare le notifiche nella barra degli strumenti per monitorare il processo di creazione. Dopo aver completata la creazione, passare alla data factory creata. Il **Data Factory** verrà visualizzata la pagina iniziale.

   ![Home page di Data factory](media/data-factory-load-data/data-factory-home-page.png)

1. Selezionare il **crea e monitora** riquadro per avviare l'applicazione in una scheda separata.

## <a name="load-data-into-azure-data-explorer"></a>Caricare i dati in Esplora dati di Azure

I dati possono essere caricati da molti tipi di [archivi dati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Esplora dati di Azure. In questo argomento illustra in dettaglio il caricamento dei dati da Amazon S3.

Esistono due modi per caricare dati in Esplora dati di Azure usando Azure Data Factory:

* Interfaccia utente di Azure Data Factory - [ **autore** scheda](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **copia dati** strumento](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) usato in questo articolo.

### <a name="copy-data-from-amazon-s3-source"></a>Copiare dati da Amazon S3 (origine)

1. Nel **attività iniziali** pagina, selezionare la **copia dati** riquadro per avviare lo strumento Copia dati.

   ![Riquadro dello strumento di copia dei dati](media/data-factory-load-data/copy-data-tool-tile.png)

1. Nel **delle proprietà** , specificare **Task name** e selezionare **successivo**.

    ![Copiare da proprietà di origine](media/data-factory-load-data/copy-from-source.png)

1. Nel **archivio dati di origine** pagina, fare clic su **+ Crea nuova connessione**.

    ![I dati di origine creare una connessione](media/data-factory-load-data/source-create-connection.png)

1. Selezionare **Amazon S3**, quindi selezionare **continua**

    ![Nuovo servizio collegato](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Nel **nuovo servizio collegato (Amazon S3)** pagina, effettuare i passaggi seguenti:

    ![Specificare il servizio collegato Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Specificare **nome** del nuovo servizio collegato.
    * Selezionare **Connetti tramite runtime di integrazione** valore dall'elenco a discesa.
    * Specificare il valore **ID della chiave di accesso**.
    * Specificare il valore **Chiave di accesso segreta**.
    * Selezionare **Test connessione** per testare la connessione del servizio collegato è stato creato.
    * Selezionare **Fine**.

1. Nel **archivio dati di origine** pagina, si noterà una nuova connessione AmazonS31. Selezionare **Avanti**.

   ![Archivio dati di origine connessione creata](media/data-factory-load-data/source-data-store-created-connection.png)

1. Nel **scegliere il file di input o la cartella** pagina:

    1. Individuare la cartella o il file che si desidera copiare. Selezionare la cartella o il file.
    1. Selezionare il comportamento di copia in base alle esigenze. Mantieni **copia binaria** deselezionata.
    1. Selezionare **Avanti**.

    ![Scegliere il file o la cartella di input](media/data-factory-load-data/source-choose-input-file.png)

1. Nel **formati di file impostazioni** pagina selezionare le impostazioni rilevanti per i file e fare clic su **successivo**.

   ![Scegliere il file o la cartella di input](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiare i dati in Esplora dati di Azure (destinazione)

Azure Esplora dati nuovo servizio collegato viene creato per copiare i dati nella tabella di destinazione di Esplora dati di Azure (sink) specificati di seguito.

1. Nel **archivio dati di destinazione** pagina, è possibile usare i dati esistenti della connessione dell'archivio o specificare un nuovo archivio dati facendo **+ Crea nuova connessione**.

    ![Pagina dell'archivio dati di destinazione](media/data-factory-load-data/destination-create-connection.png)

1. Nel **nuovo servizio collegato** pagina, selezionare **Esplora dati di Azure**, quindi selezionare **continua**

    ![Selezionare Azure Esplora dati - nuovo servizio collegato](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Nel **nuovo servizio collegato (Esplora dati di Azure)** pagina, effettuare i passaggi seguenti:

    ![Nuovo servizio collegato ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Selezionare **nome** per Esplora dati di Azure di servizio collegato.
   * Nelle **metodo di selezione dell'Account**: 
        * Selezionare il **sottoscrizione di Azure** pulsante di opzione e selezionare il **sottoscrizione di Azure** account. Selezionare quindi il **Cluster**. Si noti l'elenco a discesa verranno elencare solo i cluster che appartengono all'utente.
        * In alternativa, selezionare **Immetti manualmente** pulsante di opzione e immettere il **Endpoint**.
    * Specificare il **Tenant**.
    * Immettere **ID dell'entità servizio**.
    * Selezionare **chiave dell'entità servizio** pulsante e immettere **chiave dell'entità servizio**.
    * Selezionare i **Database** nel menu a discesa. In alternativa, selezionare **modifica** casella di controllo e immettere il nome del database.
    * Selezionare **Test connessione** per testare la connessione del servizio collegato è stato creato. Se è possibile connettersi alla propria configurazione, un segno di spunta verde **connessione riuscita** verranno visualizzati.
    * Selezionare **fine** per completare la creazione del servizio collegato.

    > [!NOTE]
    > L'entità servizio è usato da Azure Data Factory per accedere al servizio di Esplora dati di Azure. Per l'entità servizio [creare un Azure Active Directory (Azure AD) dell'entità servizio](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Non usare la **Azure Key Vault** (metodo).

1. Il **archivio dati di destinazione** apre. La connessione dati di Esplora dati di Azure che è stato creato è disponibile per l'uso. Selezionare **successivo** per configurare la connessione.

    ![Archivio dati di destinazione ADX](media/data-factory-load-data/destination-data-store.png)

1. Nelle **mapping di tabelle**, impostare il nome di tabella di destinazione e selezionare **successivo**.

    ![Mapping di tabelle di set di dati di destinazione](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Nel **mapping di colonne** pagina:
    * Il primo mapping viene eseguito da Azure Data factory in base alla [mapping dello schema di Azure Data factory](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Impostare il **mapping delle colonne** per la tabella di destinazione di Azure Data Factory. Il mapping predefinito viene visualizzato dall'origine alla tabella di destinazione di Azure Data factory.
        * Deselezionare le colonne che non è necessario definire il mapping di colonne.
    * Il secondo mapping si verifica quando vengono inseriti dati tabulari in Esplora dati di Azure. Viene eseguito il mapping in base alla [regole di mapping CSV](/azure/kusto/management/mappings#csv-mapping). Si noti che anche se i dati di origine non è in formato CSV, Azure Data Factory ha convertito i dati in un formato tabulare, di conseguenza, il mapping di CSV è il mapping solo rilevante in questa fase.
        * Sotto **proprietà del sink Azure Data Explorer (Kusto)** aggiungere il relativo **nome mapping inserimento** (facoltativo) il mapping di tale colonna può essere pertanto utilizzato.
        * Se **nome mapping inserimento** non è specificato, l'ordine "base al nome" mapping definito nel **mapping colonne** sezione verrà eseguita. Se il mapping di "base al nome" non riesce, Esplora dati di Azure tenterà di inserire i dati in un ordine "position dalla colonna" (esegue il mapping dalla posizione come valore predefinito).
    * Selezionare **Avanti**

    ![Mapping di colonne di set di dati di destinazione](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Nella pagina **Impostazioni** :
    * Impostare il relativo **relative alla tolleranza di errore**.
    * **Impostazioni relative alle prestazioni**: Abilita gestione temporanea non è applicabile. **Impostazioni avanzate** includere considerazioni sui costi. Lasciare invariato se non necessita di alcuna specifica.
    * Selezionare **Avanti**.

    ![Copiare le impostazioni di dati](media/data-factory-load-data/copy-data-settings.png)

1. Nelle **Summary**, rivedere le impostazioni e selezionare **successivo**.

    ![Copiare i dati di riepiloghi](media/data-factory-load-data/copy-data-summary.png)

1. Nel **nella pagina distribuzione**:
    * Selezionare **Monitor** per passare alle **Monitor** scheda e visualizzare lo stato della pipeline (flusso di dati, errori e lo stato di avanzamento).
    * Selezionare **Pipeline modificare** per modificare i servizi collegati, set di dati e pipeline.
    * Selezionare **fine** all'attività di copia completa dei dati

    ![Pagina Distribuzione](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passaggi successivi

* Scopri le [connettore di Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.

* Altre informazioni sulla modifica di servizi collegati, set di dati e pipeline nel [interfaccia utente di Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Scopri [le query di Esplora dati di Azure](/azure/data-explorer/web-query-data) per eseguire query sui dati.
