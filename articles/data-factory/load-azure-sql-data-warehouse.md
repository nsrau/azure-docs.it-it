---
title: Caricare i dati in Azure Synapse Analytics
description: Usare Azure Data Factory per copiare dati in Azure sinapsi Analytics
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: dcf3db33818448116da53d8a01d0c62aca7bc1af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000080"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Caricare i dati in Azure sinapsi Analytics usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure sinapsi Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) è un database basato sul cloud e con scalabilità orizzontale in grado di elaborare grandi volumi di dati, sia relazionali che non relazionali. Azure sinapsi Analytics è basato sull'architettura MPP (Massive Parallel Processing) ottimizzata per carichi di lavoro aziendali data warehouse. Offre l'elasticità del cloud con la flessibilità per ridimensionare la capacità di archiviazione e di calcolo in modo indipendente.

La Guida introduttiva ad Azure sinapsi Analytics è ora più semplice che mai quando si usa Azure Data Factory. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare un'analisi di sinapsi di Azure con i dati del sistema esistente e risparmiare tempo durante la creazione delle soluzioni di analisi.

Azure Data Factory offre i vantaggi seguenti per il caricamento dei dati in Azure sinapsi Analytics:

* **Facilità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto completo per archivi dati**: supporto integrato per una vasta gamma di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni ineguagliabili tramite polibase**: la polibase è il modo più efficiente per spostare i dati in Azure sinapsi Analytics. Usare la funzionalità del BLOB di staging per ottenere velocità di carico elevate da tutti i tipi di archivi dati, tra cui l'archiviazione BLOB di Azure e Data Lake Store. (Polibase supporta l'archiviazione BLOB di Azure e Azure Data Lake Store per impostazione predefinita). Per informazioni dettagliate, vedere [prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Data Factory Copia dati per _caricare dati dal database SQL di Azure in Azure sinapsi Analytics_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
> Per altre informazioni, vedere [copiare dati da o verso Azure sinapsi Analytics usando Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Analisi delle sinapsi di Azure: il data warehouse include i dati copiati dal database SQL. Se non si dispone di un'analisi delle sinapsi di Azure, vedere le istruzioni in [creare un'analisi di sinapsi di Azure](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md).
* Database SQL di Azure: questa esercitazione copia i dati del set di dati di esempio di Adventure Works LT nel database SQL di Azure. È possibile creare questo database di esempio in Database SQL seguendo le istruzioni fornite in [Creare un database di esempio in Database SQL di Azure](../azure-sql/database/single-database-create-quickstart.md).
* Account di archiviazione di Azure: Archiviazione di Azure viene usato come BLOB di _staging_ nell'operazione di copia in blocco. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Dati e analisi** > **Data factory**:

2. Nella pagina **nuovo data factory** specificare i valori per gli elementi seguenti:

    * **Nome**: immettere *LoadSQLDWDemo* per nome. Il nome del data factory deve essere * globalmente univoco. Se viene visualizzato l'errore "il nome ' LoadSQLDWDemo ' della data factory non è disponibile", immettere un nome diverso per il data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Crea**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:

   ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-synapse-analytics"></a>Caricare i dati in Azure Synapse Analytics

1. Nella pagina attività **iniziali** selezionare il riquadro **copia dati** per avviare lo strumento copia dati.

2. Nella pagina **Proprietà** specificare **CopyFromSQLToSQLDW** per il campo **nome attività** , quindi fare clic su **Avanti**.

    ![Pagina Proprietà](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. Nella pagina **Archivio dati di origine** completare la procedura seguente:
    >[!TIP]
    >In questa esercitazione si usa *l'autenticazione SQL* come tipo di autenticazione per l'archivio dati di origine, ma è possibile scegliere altri metodi di autenticazione supportati: l'*entità servizio* e l' *identità gestita* , se necessario. Per informazioni dettagliate, vedere le sezioni corrispondenti in [questo articolo](./connector-azure-sql-database.md#linked-service-properties).
    >Per archiviare in modo sicuro i segreti per gli archivi dati, è anche consigliabile usare il servizio Azure Key Vault. Per le spiegazioni dettagliate, vedere [questo articolo](./store-credentials-in-key-vault.md).

    a. fare clic su **+ Crea nuova connessione**.

    b. Selezionare **Database SQL di Azure** dalla raccolta e selezionare **Continua**. È possibile digitare "SQL" nella casella di ricerca per filtrare i connettori.

    ![Selezionare il database SQL di Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Nella pagina **nuovo servizio collegato** selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e la password. Fare clic su **Verifica connessione** per verificare le impostazioni, quindi selezionare **Crea**.

    ![Configurare il database SQL di Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selezionare il servizio collegato appena creato come origine, quindi fare clic su **Avanti**.

4. Nella pagina **Select tables from which to copy the data or use a custom query** (Selezionare le tabelle dalle quali copiare i dati oppure usare una query personalizzata) immettere **SalesLT** per filtrare le tabelle. Scegliere la casella **(Seleziona tutto)** per utilizzare tutte le tabelle per la copia, quindi fare clic su **Avanti**.

    ![Selezionare le tabelle di origine](./media/load-azure-sql-data-warehouse/select-source-tables.png)

5. Nella pagina **Applica filtro** specificare le impostazioni oppure fare clic su **Avanti**.

6. Nella pagina **Archivio dati di destinazione** completare la procedura seguente:
    >[!TIP]
    >In questa esercitazione si usa *l'autenticazione SQL* come tipo di autenticazione per l'archivio dati di destinazione, ma è possibile scegliere altri metodi di autenticazione supportati:*entità servizio* e *identità gestita* , se necessario. Per informazioni dettagliate, vedere le sezioni corrispondenti in [questo articolo](./connector-azure-sql-data-warehouse.md#linked-service-properties).
    >Per archiviare in modo sicuro i segreti per gli archivi dati, è anche consigliabile usare il servizio Azure Key Vault. Per le spiegazioni dettagliate, vedere [questo articolo](./store-credentials-in-key-vault.md).

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    b. Selezionare **Azure sinapsi Analytics (in precedenza SQL Data Warehouse)** dalla raccolta e selezionare **continua**. È possibile digitare "SQL" nella casella di ricerca per filtrare i connettori.

    ![Selezionare Azure sinapsi Analytics](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Nella pagina **nuovo servizio collegato** selezionare il nome del server e il nome del database dall'elenco a discesa e specificare il nome utente e la password. Fare clic su **Verifica connessione** per verificare le impostazioni, quindi selezionare **Crea**.

    ![Configurare Azure sinapsi Analytics](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selezionare il servizio collegato appena creato come sink e quindi fare clic su **Avanti**.

7. Nella pagina **Mapping tabella** esaminare il contenuto e selezionare **Avanti**. Viene visualizzato un mapping intelligente delle tabelle. Viene eseguito il mapping delle tabelle di origine alle tabelle di destinazione in base ai nomi di tabella. Se una tabella di origine non esiste nella destinazione, per impostazione predefinita Azure Data Factory crea una tabella di destinazione con lo stesso nome. È anche possibile eseguire il mapping di una tabella di origine a una tabella di destinazione esistente.

   ![Pagina Mapping tabella](./media/load-azure-sql-data-warehouse/table-mapping.png)

8. Nella pagina **Mapping colonne** esaminare il contenuto e selezionare **Avanti**. Il mapping intelligente delle tabelle è basato sul nome di colonna. Se si sceglie di consentire a Data Factory di creare automaticamente le tabelle, può essere applicata la conversione del tipo di dati in caso di incompatibilità tra gli archivi di origine e di destinazione. Se viene applicata una conversione del tipo di dati non supportata tra la colonna di origine e quella di destinazione, viene visualizzato un messaggio di errore accanto alla tabella corrispondente.

    ![Pagina Mapping colonne](./media/load-azure-sql-data-warehouse/schema-mapping.png)

9. Nella pagina **Impostazioni** completare la procedura seguente:

    a. In **Impostazioni di gestione temporanea** fare clic su **+ Nuovo** per creare una nuova risorsa di archiviazione di gestione temporanea. L'archivio viene usato per eseguire lo staging dei dati prima di caricarli in Azure Synapse Analytics tramite PolyBase. Al termine della copia, i dati provvisori nell'archivio BLOB di Azure vengono puliti automaticamente.

    b. Nella pagina **nuovo servizio collegato** selezionare l'account di archiviazione e selezionare **Crea** per distribuire il servizio collegato.

    c. Nella sezione **Impostazioni avanzate**, deselezionare l’opzione **Usa tipo predefinito**, quindi selezionare **Avanti**.

    ![Configurare PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

10. Nella pagina **Riepilogo** verificare le impostazioni e fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/load-azure-sql-data-warehouse/summary-page.png)

11. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività). 
 
12. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. Quando l'esecuzione della pipeline viene completata correttamente, selezionare il collegamento **CopyFromSQLToSQLDW** nella colonna **nome pipeline** per visualizzare i dettagli dell'esecuzione dell'attività o eseguire di nuovo la pipeline.

    [![Monitorare le esecuzioni delle pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)

13. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Tutte le esecuzioni della pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco.

    ![Monitorare le esecuzioni delle attività](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

14. Per monitorare i dettagli di esecuzione di ogni attività di copia, selezionare il collegamento **Dettagli** (icona occhiali) in **nome attività** nella visualizzazione esecuzioni attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate.
    ![Monitorare prima i dettagli dell'esecuzione dell'attività](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Dettagli esecuzione attività di monitoraggio secondo](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti per altre informazioni sul supporto di Azure Synapse Analytics:

> [!div class="nextstepaction"]
>[Connettore Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md)