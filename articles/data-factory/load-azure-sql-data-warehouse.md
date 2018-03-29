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
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 49ba61ba8cf68a39eef21b1939a3e8a6c92f8827
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Caricare dati in Azure SQL Data Warehouse tramite Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) è un database basato su cloud con scalabilità orizzontale che può elaborare ingenti volumi di dati relazionali e non relazionali. Basato sull'architettura Massively Parallel Processing (MPP), SQL Data Warehouse è ottimizzato per i carichi di lavoro dei data warehouse aziendali. Offre l'elasticità del cloud con la flessibilità per ridimensionare la capacità di archiviazione e di calcolo in modo indipendente.

Iniziare a usare Azure SQL Data Warehouse è oggi più semplice che mai con Azure Data Factory. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare SQL Data Warehouse con dati provenienti dal sistema esistente e risparmiare tempo durante la compilazione di soluzioni di analisi.

Azure Data Factory offre i vantaggi seguenti per il caricamento di dati in Azure SQL Data Warehouse:

* **Semplicità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto completo per archivi dati**: supporto integrato per una vasta gamma di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni ineguagliabili con PolyBase**: PolyBase è lo strumento più efficiente per spostare dati in Azure SQL Data Warehouse. Usare la funzionalità del BLOB di staging per ottenere velocità di carico elevate da tutti i tipi di archivi dati, tra cui l'archiviazione BLOB di Azure e Data Lake Store. Polybase supporta l'archiviazione BLOB di Azure e Azure Data Lake Store per impostazione predefinita. Per informazioni, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo mostra come usare lo strumento Copia dati di Data Factory per _caricare dati dal database SQL di Azure in Azure SQL Data Warehouse_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
> Per altre informazioni, vedere [Copiare dati da o in Azure SQL Data Warehouse usando Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Questo articolo è applicabile alla versione 2 di Azure Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia in Azure Data Factory versione 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>prerequisiti

* Sottoscrizione di Azure: se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Azure SQL Data Warehouse: il data warehouse contiene i dati copiati dal database SQL. Se non è disponibile un'istanza di Azure SQL Data Warehouse, vedere le istruzioni fornite in [Creare un'istanza di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Database SQL di Azure: questa esercitazione copia i dati da un database SQL di Azure con dati di esempio di Adventure Works LT. È possibile creare un database SQL seguendo le istruzioni fornite in [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md). 
* Account di archiviazione di Azure: Archiviazione di Azure viene usato come BLOB di _staging_ nell'operazione di copia in blocco. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Nel menu a sinistra scegliere **Nuovo** > **Dati e analisi** > **Data factory**: 
   
   ![Creare una nuova data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente:
      
   ![Pagina Nuova data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadSQLDWDemo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione**: selezionare **V2 (anteprima)**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Create**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:
   
   ![Home page di Data factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati in Azure SQL Data Warehouse

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati:

   ![Riquadro dello strumento Copia dati](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** specificare **CopyFromSQLToSQLDW** per il campo **Nome attività** e quindi selezionare **Avanti**:

    ![Pagina Proprietà](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** selezionare **Database SQL di Azure** e quindi **Avanti**:

    ![Pagina Archivio dati di origine](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Nella pagina **Specify the Azure SQL database** (Specificare il database SQL di Azure) seguire questa procedura: 
   1. Selezionare il server di Azure SQL per il **nome server**.
   2. Selezionare il database SQL di Azure per il **nome database**.
   3. Immettere il nome dell'utente per il **nome utente**.
   4. Immettere la password dell'utente per **Password**.
   5. Selezionare **Avanti**.
   
   ![Specificare Azure SQL DB](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Nella pagina **Select tables from which to copy the data or use a custom query** (Selezionare le tabelle dalle quali copiare i dati oppure usare una query personalizzata) immettere **SalesLT** per filtrare le tabelle. Scegliere la casella **(Seleziona tutto)** per usare tutte le tabelle per la copia e quindi selezionare **Avanti**: 

    ![Selezionare le tabelle di origine](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Nella pagina **Archivio dati di destinazione** selezionare **Azure SQL Data Warehouse** e quindi **Avanti**:

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Nella pagina **Specify the Azure SQL Data Warehouse** (Specificare Azure SQL Data Warehouse) seguire questa procedura: 

   1. Selezionare il server di Azure SQL per il **nome server**.
   2. Selezionare Azure SQL Data Warehouse nel campo **Nome database**.
   3. Immettere il nome dell'utente per il **nome utente**.
   4. Immettere la password dell'utente per **Password**.
   5. Selezionare **Avanti**.
   
   ![Specificare Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Nella pagina **Mapping tabella** esaminare il contenuto e selezionare **Avanti**. Viene visualizzato un mapping intelligente delle tabelle. Viene eseguito il mapping delle tabelle di origine alle tabelle di destinazione in base ai nomi di tabella. Se una tabella di origine non esiste nella destinazione, per impostazione predefinita Azure Data Factory crea una tabella di destinazione con lo stesso nome. È anche possibile eseguire il mapping di una tabella di origine a una tabella di destinazione esistente. 

   > [!NOTE]
   > La creazione automatica di tabelle per il sink SQL Data Warehouse viene applicata quando l'origine è SQL Server o il database SQL di Azure. Se si copiano i dati da un altro archivio dati di origine, è necessario aver creato lo schema nel sink Azure SQL Data Warehouse prima di eseguire la copia dei dati.

   ![Pagina Mapping tabella](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Nella pagina **Mapping dello schema** esaminare il contenuto e selezionare **Avanti**. Il mapping intelligente delle tabelle è basato sul nome di colonna. Se si sceglie di consentire a Data Factory di creare automaticamente le tabelle, può essere applicata la conversione del tipo di dati in caso di incompatibilità tra gli archivi di origine e di destinazione. Se viene applicata una conversione del tipo di dati non supportata tra la colonna di origine e quella di destinazione, viene visualizzato un messaggio di errore accanto alla tabella corrispondente.

    ![Pagina Mapping dello schema](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Nella pagina **Impostazioni** selezionare l'account di archiviazione di Azure nell'elenco a discesa **Nome account di archiviazione**. L'account viene usato per eseguire lo staging dei dati prima di caricarli in SQL Data Warehouse tramite PolyBase. Al termine della copia, viene eseguita la pulizia automatica dei dati provvisori in Archiviazione di Azure. In **Impostazioni avanzate** deselezionare l'opzione **Tipo di uso predefinito**:

    ![Pagina Impostazioni](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**:

    ![Pagina Riepilogo](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività):

    ![Pagina Distribuzione](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline: 

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Nella pipeline sono presenti 10 attività di copia, ciascuna delle quali copia una tabella di dati. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate:

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Connettore Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)