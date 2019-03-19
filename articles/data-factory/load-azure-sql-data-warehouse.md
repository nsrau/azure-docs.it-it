---
title: Caricare dati in Azure SQL Data Warehouse tramite Azure Data Factory | Microsoft Docs
description: Usare Azure Data Factory per copiare dati in Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 7a478a9f73edae463a5dace1b1a28180e5d09bdc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437735"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Caricare dati in Azure SQL Data Warehouse tramite Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) è un database basato su cloud con scalabilità orizzontale che può elaborare ingenti volumi di dati relazionali e non relazionali. Basato sull'architettura Massively Parallel Processing (MPP), SQL Data Warehouse è ottimizzato per i carichi di lavoro dei data warehouse aziendali. Offre l'elasticità del cloud con la flessibilità per ridimensionare la capacità di archiviazione e di calcolo in modo indipendente.

Iniziare a usare Azure SQL Data Warehouse è oggi più semplice che mai con Azure Data Factory. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare SQL Data Warehouse con dati provenienti dal sistema esistente e risparmiare tempo durante la compilazione di soluzioni di analisi.

Azure Data Factory offre i vantaggi seguenti per il caricamento di dati in Azure SQL Data Warehouse:

* **Facilità di configurazione**: Una passaggio 5 procedura guidata intuitiva senza necessità di script.
* **Supporto di archiviare i dati dettagliati**: Supporto incorporato per una vasta gamma di archivi dati basati sul cloud e locali. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: I dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni ineguagliabili tramite PolyBase**: Polybase è il modo più efficiente per spostare i dati in Azure SQL Data Warehouse. Usare la funzionalità del BLOB di staging per ottenere velocità di carico elevate da tutti i tipi di archivi dati, tra cui l'archiviazione BLOB di Azure e Data Lake Store. Polybase supporta l'archiviazione BLOB di Azure e Azure Data Lake Store per impostazione predefinita. Per informazioni, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo mostra come usare lo strumento Copia dati di Data Factory per _caricare dati dal database SQL di Azure in Azure SQL Data Warehouse_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
> Per altre informazioni, vedere [Copiare dati da o in Azure SQL Data Warehouse usando Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Azure SQL Data Warehouse: Il data warehouse contiene i dati copiati dal database SQL. Se non è disponibile un'istanza di Azure SQL Data Warehouse, vedere le istruzioni fornite in [Creare un'istanza di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Database SQL di Azure: Questa esercitazione copia i dati da un database SQL di Azure con i dati di esempio Adventure Works LT. È possibile creare un database SQL seguendo le istruzioni fornite in [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md). 
* Account di archiviazione Azure: Archiviazione di Azure viene usato come le _staging_ blob nell'operazione di copia bulk. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra scegliere **Nuovo** > **Dati e analisi** > **Data factory**: 
   
   ![Creare una nuova data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
1. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente:
      
   ![Pagina Nuova data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadSQLDWDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

1. Selezionare **Create**.
1. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:
   
   ![Home page di Data factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati in Azure SQL Data Warehouse

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati:

   ![Riquadro dello strumento Copia dati](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Nella pagina **Proprietà** specificare **CopyFromSQLToSQLDW** per il campo **Nome attività** e quindi selezionare **Avanti**:

    ![Pagina Proprietà](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. fare clic su **+ Crea nuova connessione**:

    ![Pagina Archivio dati di origine](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Selezionare **Database SQL di Azure** dalla raccolta e selezionare **Continua**. È possibile digitare "SQL" nella casella di ricerca per filtrare i connettori.

    ![Selezionare il database SQL di Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Nel **nuovo servizio collegato** pagina, selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e password. Fare clic su **Connessione di test** per convalidare le impostazioni, quindi selezionare **Fine**.
   
    ![Configurare il database SQL di Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selezionare il servizio collegato appena creato come origine, quindi fare clic su **Avanti**.

    ![Selezionare il servizio collegato di origine](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Nella pagina **Select tables from which to copy the data or use a custom query** (Selezionare le tabelle dalle quali copiare i dati oppure usare una query personalizzata) immettere **SalesLT** per filtrare le tabelle. Scegliere la casella **(Seleziona tutto)** per usare tutte le tabelle per la copia e quindi selezionare **Avanti**: 

    ![Selezionare le tabelle di origine](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Nella pagina **Archivio dati di destinazione** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    ![Pagina archivio dati del sink](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Selezionare **Azure SQL Data Warehouse** dalla raccolta e selezionare **Avanti**.

    ![Selezionare Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Nel **nuovo servizio collegato** pagina, selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e password. Fare clic su **Connessione di test** per convalidare le impostazioni, quindi selezionare **Fine**.
   
    ![Configurare Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selezionare il servizio collegato appena creato come sink e quindi fare clic su **Avanti**.

    ![Selezionare il servizio collegato sink](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Nella pagina **Mapping tabella** esaminare il contenuto e selezionare **Avanti**. Viene visualizzato un mapping intelligente delle tabelle. Viene eseguito il mapping delle tabelle di origine alle tabelle di destinazione in base ai nomi di tabella. Se una tabella di origine non esiste nella destinazione, per impostazione predefinita Azure Data Factory crea una tabella di destinazione con lo stesso nome. È anche possibile eseguire il mapping di una tabella di origine a una tabella di destinazione esistente. 

   > [!NOTE]
   > La creazione automatica di tabelle per il sink SQL Data Warehouse viene applicata quando l'origine è SQL Server o il database SQL di Azure. Se si copiano i dati da un altro archivio dati di origine, è necessario aver creato lo schema nel sink Azure SQL Data Warehouse prima di eseguire la copia dei dati.

   ![Pagina Mapping tabella](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Nella pagina **Mapping dello schema** esaminare il contenuto e selezionare **Avanti**. Il mapping intelligente delle tabelle è basato sul nome di colonna. Se si sceglie di consentire a Data Factory di creare automaticamente le tabelle, può essere applicata la conversione del tipo di dati in caso di incompatibilità tra gli archivi di origine e di destinazione. Se viene applicata una conversione del tipo di dati non supportata tra la colonna di origine e quella di destinazione, viene visualizzato un messaggio di errore accanto alla tabella corrispondente.

    ![Pagina Mapping dello schema](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Nella pagina **Impostazioni** completare la procedura seguente:

    a. In **Impostazioni di gestione temporanea** fare clic su **+ Nuovo** per creare una nuova risorsa di archiviazione di gestione temporanea. La risorsa di archiviazione viene usata per eseguire lo staging dei dati prima di caricarli in SQL Data Warehouse tramite PolyBase. Al termine della copia, viene eseguita la pulizia automatica dei dati provvisori in Archiviazione di Azure. 

    ![Configurare la gestione temporanea](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Nella pagina **Nuovi servizi collegati**, selezionare l'account di archiviazione e selezionare **Fine**.
   
    ![Configurare l'archiviazione di Azure](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Nella sezione **Impostazioni avanzate**, deselezionare l’opzione **Usa tipo predefinito**, quindi selezionare **Avanti**.

    ![Configurare PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**:

    ![Pagina Riepilogo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività):

    ![Pagina Distribuzione](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline: 

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate:

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Connettore Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
