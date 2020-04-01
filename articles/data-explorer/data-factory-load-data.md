---
title: Copiare dati da Azure Data Factory in Azure Data Explorer
description: In questo articolo viene illustrato come inserire (caricare) i dati in Azure Data Explorer usando lo strumento di copia di Azure Data Factory.In this article, you learn how to ingest (load) data into Azure Data Explorer by using the Azure Data Factory copy tool.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 8e17a004ff866f3915000fb72b6770757062cf83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422906"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Copiare dati in Azure Data Explorer tramite Azure Data FactoryCopy data to Azure Data Explorer by using Azure Data Factory 

Azure Data Explorer è un servizio di analisi dei dati veloce e completamente gestito. Offre analisi in tempo reale su grandi volumi di dati provenienti da molte origini, ad esempio applicazioni, siti Web e dispositivi IoT. Con Azure Data Explorer è possibile esplorare in modo iterativo i dati e identificare modelli e anomalie per migliorare i prodotti, migliorare le esperienze dei clienti, monitorare i dispositivi e incrementare le operazioni. Ti aiuta a esplorare nuove domande e ottenere risposte in pochi minuti. 

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato su cloud. È possibile usarlo per popolare il database di Azure Data Explorer con i dati del sistema esistente. Può aiutarti a risparmiare tempo quando costruisci soluzioni di analisi.

Quando si caricano dati in Azure Data Explorer, Data Factory offre i vantaggi seguenti:When you load data into Azure Data Explorer, Data Factory provides the following benefits:

* **Facile configurazione**: Ottenere una procedura guidata intuitiva in cinque passaggi senza necessità di script.
* **Supporto avanzato per l'archivio dati:** supporto integrato per un set completo di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sicuro e conforme:** i dati vengono trasferiti tramite HTTPS o Azure ExpressRoute.Secure and compliant : Data is transferred over HTTPS or Azure ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni elevate:** la velocità di caricamento dei dati è fino a 1 gigabyte al secondo (GBps) in Azure Data Explorer.High performance : The data-loading speed is up to 1 gigabyte al secondo (GBps) into Azure Data Explorer. Per ulteriori informazioni, consultate [Prestazioni dell'attività di copia](/azure/data-factory/copy-activity-performance).

In this article, you use the Data Factory Copy Data tool to load data from Amazon Simple Storage Service (S3) into Azure Data Explorer. È possibile seguire un processo simile per copiare dati da altri archivi dati, ad esempio:You can follow a similar process to copy data from other data stores, such as:
* [Archiviazione BLOB di Azure](/azure/data-factory/connector-azure-blob-storage)
* [Database SQL di Azure](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [File system](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Esplora dati di Azure](create-cluster-database-portal.md).
* Un'origine dati.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Accedere al [portale di Azure](https://ms.portal.azure.com).

1. Nel riquadro sinistro selezionare **Crea una risorsa** > **Analytics** > **Data Factory**.

   ![Creare una factory di dati nel portale di AzureCreate a data factory in the Azure portal](media/data-factory-load-data/create-adf.png)

1. Nel riquadro **Nuova data factory** specificare i valori per i campi della tabella seguente:

   ![Riquadro "Nuova data factory"](media/data-factory-load-data/my-new-data-factory.png)  

   | Impostazione  | Valore da immettere  |
   |---|---|
   | **Nome** | Nella casella immettere un nome univoco globale per la data factory. Se viene visualizzato un errore, *il \"\" nome della data factory LoadADXDemo non è disponibile,* immettere un nome diverso per la data factory. Per le regole relative alla denominazione degli elementi di Data Factory, vedere Regole di [denominazione di Data Factory](/azure/data-factory/naming-rules).|
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione di Azure in cui creare la data factory. |
   | **Gruppo di risorse** | Selezionare **Crea nuovo**, quindi immettere il nome di un nuovo gruppo di risorse. Se si dispone già di un gruppo di risorse, selezionare **Usa esistente**. |
   | **Version** | Nell'elenco a discesa selezionare **V2**. |    
   | **Percorso** | Nell'elenco a discesa selezionare il percorso per la data factory. Nell'elenco vengono visualizzate solo le posizioni supportate. Gli archivi dati utilizzati dalla data factory possono esistere in altre posizioni o aree. |

1. Selezionare **Create** (Crea).

1. Per monitorare il processo di creazione, selezionare **Notifiche** sulla barra degli strumenti. Dopo aver creato la data factory, selezionarla.
   
   Viene visualizzato il riquadro **Data Factory.The Data Factory** pane opens.

   ![Riquadro Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Per aprire l'applicazione in un riquadro separato, selezionare il riquadro **Autore & Monitor.**

## <a name="load-data-into-azure-data-explorer"></a>Caricare dati in Esplora dati di AzureLoad data into Azure Data Explorer

È possibile caricare dati da molti tipi di archivi dati in Esplora dati di Azure.You can load data from many types of [data stores](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) into Azure Data Explorer. Questo articolo illustra come caricare dati da Amazon S3.

È possibile caricare i dati in uno dei modi seguenti:

* Nel riquadro sinistro dell'interfaccia utente di Azure Data Factory selezionare l'icona **Autore.In** the Azure Data Factory user interface, in the left pane, select the Author icon. Ciò è illustrato nella sezione "Creare una data factory" di [Creare una data factory usando l'interfaccia utente](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)di Azure Data Factory.
* Nello strumento Copia dati di Azure Data Factory, come illustrato in [Usare lo strumento Copia dati per copiare](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)i dati.

### <a name="copy-data-from-amazon-s3-source"></a>Copiare i dati da Amazon S3 (origine)

1. Nel riquadro **Iniziamo** aprire lo strumento Copia dati selezionando **Copia dati**.

   ![Pulsante dello strumento Copia dati](media/data-factory-load-data/copy-data-tool-tile.png)

1. Nella casella **Nome attività** del riquadro **Proprietà** immettere un nome e quindi selezionare **Avanti**.

    ![Riquadro Copia proprietà dati](media/data-factory-load-data/copy-from-source.png)

1. Nel riquadro **Archivio dati di origine** selezionare **Crea nuova connessione**.

    ![Il riquadro Copia dati "Archivio dati di origine"](media/data-factory-load-data/source-create-connection.png)

1. Selezionare **Amazon S3**, quindi **Continua**.

    ![Riquadro Nuovo servizio collegato](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Nel riquadro **Nuovo servizio collegato (Amazon S3),** eseguire le operazioni seguenti:

    ![Specificare il servizio collegato Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Nella casella **Nome** immettere il nome del nuovo servizio collegato.

    b. Nell'elenco a discesa **Connetti tramite runtime** di integrazione selezionare il valore.

    c. Nella casella **ID chiave** di accesso immettere il valore.
    
    > [!NOTE]
    > In Amazon S3, per individuare la chiave di accesso, seleziona il tuo nome utente Amazon sulla barra di navigazione, quindi seleziona **Le mie credenziali**di sicurezza .
    
    d. Nella casella Chiave di **accesso segreta** immettere un valore.

    e. Per testare la connessione al servizio collegato creata, selezionare **Test connessione**.

    f. Fare clic su **Fine**.
    
      Nel riquadro **Archivio dati di origine** viene visualizzata la nuova connessione AmazonS31. 

1. Fare clic su **Avanti**.

   ![Connessione creata dall'archivio dati di origine](media/data-factory-load-data/source-data-store-created-connection.png)

1. Nel riquadro **Scegliere il file o la cartella** di input eseguire le operazioni seguenti:

    a. Individuare il file o la cartella che si desidera copiare e quindi selezionarlo.

    b. Selezionare il comportamento di copia desiderato. Assicurarsi che la casella di controllo **Copia binaria** sia deselezionata.

    c. Fare clic su **Avanti**.

    ![Scegliere il file o la cartella di input](media/data-factory-load-data/source-choose-input-file.png)

1. Nel riquadro **Impostazioni formato file** selezionare le impostazioni rilevanti per il file. , quindi selezionare **Avanti**.

   ![Il riquadro "Impostazioni formato file"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiare i dati in Azure Data Explorer (destinazione)Copy data into Azure Data Explorer (destination)

Il nuovo servizio collegato di Azure Data Explorer viene creato per copiare i dati nella tabella di destinazione (sink) di Azure Data Explorer specificata in questa sezione.

> [!NOTE]
> Usare [l'attività](data-factory-command-activity.md) dei comandi di Azure Data Factory per eseguire i comandi di `.set-or-replace`controllo di Azure Data Explorer e usare l'inserimento dai comandi di [query,](/azure/kusto/management/data-ingestion/ingest-from-query)ad esempio .

#### <a name="create-the-azure-data-explorer-linked-service"></a>Creare il servizio collegato Azure Data ExplorerCreate the Azure Data Explorer linked service

Per creare il servizio collegato Esplora dati di Azure, eseguire la procedura seguente:To create the Azure Data Explorer linked service, do the following steps:

1. Per utilizzare una connessione all'archivio dati esistente o specificare un nuovo archivio dati, nel riquadro **Archivio dati** di destinazione selezionare **Crea nuova connessione**.

    ![Riquadro Dell'archivio dati di destinazione](media/data-factory-load-data/destination-create-connection.png)

1. Nel riquadro **Nuovo servizio collegato** selezionare Azure Data **Explorer**e quindi **Continua**.

    ![Riquadro Nuovo servizio collegato](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Nel riquadro **Nuovo servizio collegato (Azure Data Explorer)** eseguire la procedura seguente:In the New Linked Service (Azure Data Explorer) pane, do the following steps:

    ![Riquadro Nuovo servizio collegato di Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   a. Nella casella Nome immettere un nome per il servizio collegato Azure Data Explorer.In the **Name** box, enter a name for the Azure Data Explorer linked service.

   b. In **Metodo di selezione account**scegliere una delle opzioni seguenti: 

    * Selezionare **Da sottoscrizione di Azure** e quindi selezionare la sottoscrizione di **Azure** e il **cluster**negli elenchi a discesa. 

        > [!NOTE]
        > Il controllo a discesa **Cluster** elenca solo i cluster associati alla sottoscrizione.

    * Selezionare **Invio manualmente**, quindi immettere **l'endpoint**.

   c. Nella casella **Tenant** immettere il nome del tenant.

   d. Nella casella **ID entità servizio** immettere l'ID dell'entità servizio.

   e. Selezionare **Chiave principale** servizio e quindi immettere il valore per la chiave nella casella **Chiave entità servizio.**

   f. Nell'elenco a discesa **Database** selezionare il nome del database. In alternativa, selezionare la casella di controllo **Modifica** e quindi immettere il nome del database.

   g. Per testare la connessione al servizio collegato creata, selezionare **Test connessione**. Se è possibile connettersi al servizio collegato, nel riquadro viene visualizzato un segno di spunta verde e un messaggio **Connessione completata.**

   h. Selezionare **Fine** per completare la creazione del servizio collegato.

    > [!NOTE]
    > L'entità servizio viene usata da Azure Data Factory per accedere al servizio Azure Data Explorer.The service principal is used by Azure Data Factory to access the Azure Data Explorer service. Per creare un'entità servizio, passare alla creazione di [un'entità servizio di Azure Active Directory (Azure AD).](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Non usare il metodo dell'insieme di credenziali delle chiavi di Azure.Do not use the Azure Key Vault method.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configurare la connessione dati di Azure Data ExplorerConfigure the Azure Data Explorer data connection

Dopo aver creato la connessione al servizio collegato, viene aperto il riquadro **Archivio dati** di destinazione e la connessione creata è disponibile per l'utilizzo. Per configurare la connessione, attenersi alla seguente procedura:

1. Fare clic su **Avanti**.

    ![Riquadro "Archivio dati di destinazione" di Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. Nel **riquadro Mapping tabella** impostare il nome della tabella di destinazione e quindi selezionare **Avanti**.

    ![Riquadro "Mapping tabelle" del set di dati di destinazione](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Nel riquadro Mapping colonne vengono eseguiti i mapping seguenti:In the **Column mapping** pane, the following mappings have place:

    a. Il primo mapping viene eseguito da Azure Data Factory in base al mapping dello schema di [Azure Data Factory.](/azure/data-factory/copy-activity-schema-and-type-mapping) Eseguire le operazioni seguenti:

    * Impostare i mapping di colonna per la tabella di destinazione di Azure Data Factory.Set the **Column mappings** for the Azure Data Factory destination table. Il mapping predefinito viene visualizzato dall'origine alla tabella di destinazione di Azure Data Factory.The default mapping is displayed from source to the Azure Data Factory destination table.

    * Annullare la selezione delle colonne che non è necessario definire il mapping delle colonne.

    b. Il secondo mapping si verifica quando questi dati tabulari vengono inseriti in Azure Data Explorer.The second mapping occurs when this tabular data is ingested into Azure Data Explorer. Il mapping viene eseguito in base alle regole di [mapping CSV](/azure/kusto/management/mappings#csv-mapping). Anche se i dati di origine non sono in formato CSV, Azure Data Factory converte i dati in un formato tabulare. Pertanto, la mappatura CSV è l'unica mappatura rilevante in questa fase. Eseguire le operazioni seguenti:

    * (Facoltativo) In **Proprietà sink di Azure Data Explorer (Kusto)** aggiungere il nome del mapping di **inserimento** pertinente in modo che sia possibile usare il mapping delle colonne.

    * Se il nome del **mapping di inserimento** non è specificato, verrà utilizzato *l'ordine* di mapping per nome definito nella sezione **Mapping** di colonne. Se il mapping per *nome* ha esito negativo, Azure Data Explorer tenta di immettere i dati in un ordine di posizione per *colonna,* ovvero viene eseguito il mapping in base alla posizione come impostazione predefinita.

    * Fare clic su **Avanti**.

    ![Riquadro "Mapping colonne" del set di dati di destinazione](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Nel riquadro Impostazioni eseguire le operazioni seguenti:In the **Settings** pane, do the following steps:

    a. In **Impostazioni tolleranza di errore**immettere le impostazioni pertinenti.

    b. In **Impostazioni prestazioni**, Abilita gestione **temporanea** non applicabile e Impostazioni **avanzate** include considerazioni sui costi. Se non si dispone di requisiti specifici, lasciare queste impostazioni così come sono.

    c. Fare clic su **Avanti**.

    ![Il riquadro "Impostazioni" dei dati di copia](media/data-factory-load-data/copy-data-settings.png)

1. Nel riquadro **Riepilogo** rivedere le impostazioni e quindi selezionare **Avanti**.

    ![Il riquadro "Riepilogo" dei dati di copia](media/data-factory-load-data/copy-data-summary.png)

1. Nel riquadro **Distribuzione completata** eseguire le operazioni seguenti:In the Deployment complete pane, do the following:

    a. Per passare alla scheda **Monitoraggio** e visualizzare lo stato della pipeline, ovvero stato, errori e flusso di dati, selezionare **Monitor**.

    b. Per modificare servizi, dataset e pipeline collegati, selezionare **Modifica pipeline**.

    c. Selezionare **Fine** per completare l'attività Copia dati.

    ![Riquadro "Distribuzione completata"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [connettore](/azure/data-factory/connector-azure-data-explorer) di Azure Data Explorer in Azure Data Factory.Learn about the Azure Data Explorer connector in Azure Data Factory.
* Ulteriori informazioni sulla modifica di servizi collegati, set di dati e pipeline nell'interfaccia utente di [Data Factory.](/azure/data-factory/quickstart-create-data-factory-portal)
* Informazioni sulle query di [Azure Data Explorer](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.
