---
title: Caricare i dati in Azure SQL Data Warehouse
description: Usare Azure Data Factory per copiare dati in Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2020
ms.openlocfilehash: 1a764f392402acf9aa405468470d0fb6f680d755
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461109"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Caricare dati in Azure SQL Data Warehouse tramite Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) è un database basato su cloud con scalabilità orizzontale che può elaborare ingenti volumi di dati relazionali e non relazionali. Basato sull'architettura Massively Parallel Processing (MPP), SQL Data Warehouse è ottimizzato per i carichi di lavoro dei data warehouse aziendali. Offre l'elasticità del cloud con la flessibilità per ridimensionare la capacità di archiviazione e di calcolo in modo indipendente.

Iniziare a usare Azure SQL Data Warehouse è oggi più semplice che mai con Azure Data Factory. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare SQL Data Warehouse con dati provenienti dal sistema esistente e risparmiare tempo durante la compilazione di soluzioni di analisi.

Azure Data Factory offre i vantaggi seguenti per il caricamento di dati in Azure SQL Data Warehouse:

* **Semplicità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto avanzato dell'archivio dati:** supporto integrato per un set completo di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni ineguagliabili con PolyBase**: PolyBase è lo strumento più efficiente per spostare dati in Azure SQL Data Warehouse. Usare la funzionalità del BLOB di staging per ottenere velocità di carico elevate da tutti i tipi di archivi dati, tra cui l'archiviazione BLOB di Azure e Data Lake Store. Polybase supporta l'archiviazione BLOB di Azure e l'archivio dati di Azure per impostazione predefinita. Per informazioni dettagliate, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo mostra come usare lo strumento Copia dati di Data Factory per _caricare dati dal database SQL di Azure in Azure SQL Data Warehouse_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
> Per altre informazioni, vedere [Copiare dati da o in Azure SQL Data Warehouse usando Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Azure SQL Data Warehouse: il data warehouse contiene i dati copiati dal database SQL. Se non è disponibile un'istanza di Azure SQL Data Warehouse, vedere le istruzioni fornite in [Creare un'istanza di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Database SQL di Azure: questa esercitazione copia i dati da un database SQL di Azure con dati di esempio di Adventure Works LT. È possibile creare un database SQL seguendo le istruzioni fornite in [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* Account di archiviazione di Azure: Archiviazione di Azure viene usato come BLOB di _staging_ nell'operazione di copia in blocco. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > Dati - Data**Factory****analisi:** > 

2. Nella pagina Nuova data factory specificare i valori per gli elementi seguenti:On the **New data factory** page, provide values for following items:

    * **Nome**: Immettere *LoadSQLDWDemo* come nome. Il nome della data factory deve essere univoco a livello globale. Se viene visualizzato l'errore "Nome data factory 'LoadSQLDWDemo' non è disponibile", immettere un nome diverso per la data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Create** (Crea).
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:

   ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati in Azure SQL Data Warehouse

1. Nella pagina **Guida introduttiva** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati.

1. Nella pagina **Proprietà** specificare **CopyFromSQLToSQLDW** per il campo **Nome attività** e selezionare **Avanti**.

    ![Pagina Proprietà](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Nella pagina **Archivio dati di origine** completare la procedura seguente:
    >[!TIP]
    >In questa esercitazione si usa *l'autenticazione SQL* come tipo di autenticazione per l'archivio dati di origine, ma è possibile scegliere altri metodi di autenticazione supportati:*Entità servizio* e *Identità gestita,* se necessario. Fare riferimento alle sezioni corrispondenti in [questo articolo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) per i dettagli.
    >To store secrets for data stores securely, it's also recommended to use an Azure Key Vault. Fare riferimento a [questo articolo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) per informazioni dettagliate.

    a. fare clic su **Crea nuova connessione**.

    b. Selezionare **Database SQL di Azure** dalla raccolta e selezionare **Continua**. È possibile digitare "SQL" nella casella di ricerca per filtrare i connettori.

    ![Selezionare il database SQL di Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Nella pagina **Nuovo servizio collegato** selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e la password. Fare clic su **Test connessione** per convalidare le impostazioni, quindi selezionare **Crea**.

    ![Configurare il database SQL di Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selezionare il servizio collegato appena creato come origine, quindi fare clic su **Avanti**.

1. Nella pagina **Select tables from which to copy the data or use a custom query** (Selezionare le tabelle dalle quali copiare i dati oppure usare una query personalizzata) immettere **SalesLT** per filtrare le tabelle. Scegliere la casella **(Seleziona tutto)** per utilizzare tutte le tabelle per la copia e quindi selezionare **Avanti**.

    ![Selezionare le tabelle di origine](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Nella pagina **Applica filtro** specificare le impostazioni o selezionare **Avanti**.

1. Nella pagina **Archivio dati di destinazione** completare la procedura seguente:
    >[!TIP]
    >In questa esercitazione si usa *l'autenticazione SQL* come tipo di autenticazione per l'archivio dati di destinazione, ma è possibile scegliere altri metodi di autenticazione supportati:*Entità servizio* e *Identità gestita,* se necessario. Fare riferimento alle sezioni corrispondenti in [questo articolo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties) per i dettagli.
    >To store secrets for data stores securely, it's also recommended to use an Azure Key Vault. Fare riferimento a [questo articolo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) per informazioni dettagliate.

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    b. Selezionare **Azure Synapse Analytics (in precedenza SQL DW)** dalla raccolta e **selezionare Continua**. È possibile digitare "SQL" nella casella di ricerca per filtrare i connettori.

    ![Selezionare Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Nella pagina **Nuovo servizio collegato** selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e la password. Fare clic su **Test connessione** per convalidare le impostazioni, quindi selezionare **Crea**.

    ![Configurare Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selezionare il servizio collegato appena creato come sink e quindi fare clic su **Avanti**.

1. Nella pagina **Mapping tabella** esaminare il contenuto e selezionare **Avanti**. Viene visualizzato un mapping intelligente delle tabelle. Viene eseguito il mapping delle tabelle di origine alle tabelle di destinazione in base ai nomi di tabella. Se una tabella di origine non esiste nella destinazione, per impostazione predefinita Azure Data Factory crea una tabella di destinazione con lo stesso nome. È anche possibile eseguire il mapping di una tabella di origine a una tabella di destinazione esistente.

   > [!NOTE]
   > La creazione automatica di tabelle per il sink SQL Data Warehouse viene applicata quando l'origine è SQL Server o il database SQL di Azure. Se si copiano i dati da un altro archivio dati di origine, è necessario aver creato lo schema nel sink Azure SQL Data Warehouse prima di eseguire la copia dei dati.

   ![Pagina Mapping tabella](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Nella pagina **Mapping colonne** esaminare il contenuto e selezionare **Avanti**. Il mapping intelligente delle tabelle è basato sul nome di colonna. Se si sceglie di consentire a Data Factory di creare automaticamente le tabelle, può essere applicata la conversione del tipo di dati in caso di incompatibilità tra gli archivi di origine e di destinazione. Se viene applicata una conversione del tipo di dati non supportata tra la colonna di origine e quella di destinazione, viene visualizzato un messaggio di errore accanto alla tabella corrispondente.

    ![Pagina Mapping colonne](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Nella pagina **Impostazioni** completare la procedura seguente:

    a. In **Impostazioni di gestione temporanea** fare clic su **+ Nuovo** per creare una nuova risorsa di archiviazione di gestione temporanea. La risorsa di archiviazione viene usata per eseguire lo staging dei dati prima di caricarli in SQL Data Warehouse tramite PolyBase. Al termine della copia, i dati intermedi in Archiviazione BLOB di Azure vengono puliti automaticamente.

    b. Nella pagina **Nuovo servizio collegato** selezionare l'account di archiviazione e quindi **selezionare Crea** per distribuire il servizio collegato.

    c. Nella sezione **Impostazioni avanzate**, deselezionare l’opzione **Usa tipo predefinito**, quindi selezionare **Avanti**.

    ![Configurare PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Nella pagina **Riepilogo** rivedere le impostazioni e selezionare **Avanti**.

    ![Pagina Riepilogo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Nella **pagina Distribuzione**selezionare **Monitoraggio** per monitorare la pipeline (attività).

1. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. Al termine dell'esecuzione della pipeline, selezionare il collegamento **CopyFromSQLToSQLDW** nella colonna **PIPELINE NAME** per visualizzare i dettagli di esecuzione dell'attività e rieseguire la pipeline.

    [![Monitorare le esecuzioni di pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)
1. Per tornare alla visualizzazione delle esecuzioni della pipeline, selezionare il collegamento **Tutte le esecuzioni della pipeline** nella parte superiore. Selezionare **Aggiorna** per aggiornare l'elenco.

    ![Monitorare le esecuzioni delle attività](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) in **NOME ACTIVITY** nella visualizzazione delle esecuzioni dell'attività. È possibile monitorare dettagli quali il volume di dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate.
    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure SQL Data Warehouse:

> [!div class="nextstepaction"]
>[Connettore sql Data Warehouse di AzureAzure SQL Data Warehouse connector](connector-azure-sql-data-warehouse.md)
