---
title: Caricare dati in Azure SQL Data Warehouse tramite Azure Data Factory | Microsoft Docs
description: Usare Azure Data Factory per copiare dati in Azure SQL Data Warehouse
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Caricare dati in Azure SQL Data Warehouse tramite Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) è un database basato su cloud, con possibilità di aumentare il numero di istanze, che può elaborare volumi massivi di dati relazionali e non relazionali.  Basato sull'architettura di elaborazione parallela massiva (MPP, Massively Parallel Processing), SQL Data Warehouse è ottimizzato per i carichi di lavoro dei data warehouse dell'organizzazione.  Offre l'elasticità del cloud con la flessibilità per ridimensionare la capacità di archiviazione e di calcolo in modo indipendente.

L'uso di Azure SQL Data Warehouse è ora più semplice dell'uso di **Azure Data Factory**.  Azure Data Factory è un servizio di integrazione di dati basato su cloud completamente gestito, che può essere usato per popolare un'istanza di SQL Data Warehouse con i dati del sistema esistente e che consente di risparmiare tempo prezioso durante la valutazione di SQL Data Warehouse e la creazione di soluzioni di analisi. Di seguito sono elencati i vantaggi principali del caricamento di dati in Azure SQL Data Warehouse mediante Azure Data Factory:

* **Semplicità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto completo per archivi dati**: supporto integrato per una vasta gamma di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute e la presenza di un servizio globale garantisce che i dati non superino mai il confine geografico.
* **Prestazioni ineguagliabili tramite PolyBase**: PolyBase rappresenta il modo più efficiente per spostare dati in Azure SQL Data Warehouse. Mediante il BLOB di staging è possibile ottenere velocità di carico elevate da tutti i tipi di archivi dati, oltre all'archivio BLOB di Azure e a Data Lake Store, supportati da Polybase per impostazione predefinita. Per informazioni, vedere l'articolo sulle [prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per **caricare dati dal database SQL di Azure in Azure SQL Data Warehouse**. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
>  Per informazioni generali sulle funzionalità di Data Factory per la copia di dati da e verso Azure SQL Data Warehouse, vedere [Copiare dati da e verso Azure SQL Data Warehouse mediante Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Azure SQL Data Warehouse**. Questo data warehouse include i dati copiati dal database SQL. Se non si ha un'istanza di Azure SQL Data Warehouse, vedere la procedura per la creazione di un'istanza nell'articolo [Creare un SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* **Database SQL di Azure**. In questa esercitazione vengono copiati dati da un database SQL di Azure con i dati dell'esempio Adventure Works LT, che è possibile creare seguendo l'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md). 
* **Account di archiviazione di Azure**. Archiviazione di Azure viene usata come BLOB di **staging** nell'operazione di copia in blocco. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Fare clic su **NUOVO** nel menu a sinistra e quindi su **Dati e analisi** e **Data factory**. 
   
   ![Nuovo->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** inserire i valori come illustrato nello screenshot seguente:
      
     ![Pagina Nuova data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **Nome.** Immettere un nome univoco globale per la data factory. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **Sottoscrizione.** Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
    * **Gruppo di risorse.** Selezionare un gruppo di risorse esistente dall'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione.** Selezionare **V2 (anteprima)**.
    * **Località.** Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) usati dalla data factory possono trovarsi in altre località/aree. 

3. Fare clic su **Crea**.
4. Al termine della creazione, accedendo alla data factory verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.
   
   ![Home page di Data factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati in Azure SQL Data Warehouse

1. Nella pagina delle attività iniziali fare clic sul riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro dello strumento Copia dati](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** dello strumento Copia dati specificare **CopyFromSQLToSQLDW** per **Nome attività** e quindi fare clic su **Avanti**. 

    ![Strumento Copia dati - Pagina Proprietà](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** selezionare **Database SQL di Azure** e quindi fare clic su **Avanti**.

    ![Pagina Archivio dati di origine](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Nella pagina **Specify the Azure SQL database** (Specificare il database SQL di Azure) seguire questa procedura: 
    1. Selezionare il server di Azure SQL per il **nome server**.
    2. Selezionare il database SQL di Azure per il **nome database**.
    3. Immettere il nome dell'utente per il **nome utente**.
    4. Immettere la password dell'utente per la **password**.
    5. Fare clic su **Avanti**. 

        ![Specificare Azure SQL DB](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Nella pagina **Select tables from which to copy the data or use a custom query** (Selezionare le tabelle dalle quali copiare i dati oppure usare una query personalizzata), filtrare le tabelle specificando **SalesLT** nella casella di input, selezionare la casella di controllo **(Seleziona tutto)** per selezionare tutte le tabelle e quindi fare clic su **Avanti**. 

    ![Scegliere il file o la cartella di input](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Nella pagina **Archivio dati di destinazione** selezionare **Azure SQL Data Warehouse** e quindi fare clic su **Avanti**. 

    ![Pagina dell'archivio dati di destinazione](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Nella pagina **Specify the Azure SQL Data Warehouse** (Specificare Azure SQL Data Warehouse) seguire questa procedura: 

    1. Selezionare il server di Azure SQL per il **nome server**.
    2. Selezionare Azure SQL Data Warehouse nel campo **Nome database**.
    3. Immettere il nome dell'utente per il **nome utente**.
    4. Immettere la password dell'utente per la **password**.
    5. Fare clic su **Avanti**. 

        ![Specificare Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Nella pagina **Mapping tabella** esaminare le informazioni e fare clic su **Avanti**. Viene visualizzato un mapping intelligente delle tabelle che associa le tabelle di origine a quelle di destinazione in base ai nomi di tabella. Se la tabella non esiste nella destinazione, per impostazione predefinita Azure Data Factory ne crea una con lo stesso nome. È possibile anche eseguire il mapping a una tabella esistente. 

    > [!NOTE]
    > Quando l'origine è costituita da SQL Server o un database SQL di Azure, viene applicata la creazione automatica della tabella per il sink Azure SQL Data Warehouse. Se si copiano i dati da un altro archivio dati di origine, prima di copiare i dati è necessario creare lo schema nel sink Azure SQL Data Warehouse.

    ![Pagina Mapping tabella](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Nella pagina **Mapping dello schema** esaminare le informazioni e fare clic su **Avanti**. Il mapping intelligente è basato sui nomi delle colonne. Se si sceglie di consentire a Data Factory di creare automaticamente le tabelle, può essere applicata un'adeguata conversione del tipo di dati se è necessario per risolvere eventuali incompatibilità tra gli archivi di origine e di destinazione. Se è presente una conversione di tipo di dati non supportata tra la colonna di origine e di destinazione, viene visualizzato un messaggio di errore insieme della tabella corrispondente.

    ![Pagina Mapping dello schema](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Nella pagina **Impostazioni** selezionare una risorsa di Archiviazione di Azure nell'elenco a discesa **Nome account di archiviazione**. La risorsa viene usata per eseguire lo staging dei dati prima che vengano caricati in SQL Data Warehouse tramite PolyBase. Al termine della copia, i dati provvisori nell'archiviazione verranno eliminati automaticamente. 

    In "Impostazioni avanzate" deselezionare l'opzione "Tipo di uso predefinito".

    ![Pagina Impostazioni](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Nella pagina **Riepilogo** esaminare le impostazioni e fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Nella pagina **Distribuzione** fare clic su **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. Nella colonna **Azioni** sono disponibili i collegamenti per visualizzare i dettagli delle esecuzioni dell'attività e per eseguire di nuovo la pipeline. 

    ![Monitorare le esecuzioni di pipeline](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Nella pipeline sono presenti 10 attività di copia, ciascuna delle quali copia una tabella di dati. Per tornare alla visualizzazione delle esecuzioni di pipeline, fare clic sul collegamento **Pipeline** in alto. Fare clic su **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. È possibile monitorare ulteriormente i dettagli di esecuzione di ogni attività di copia facendo clic sul collegamento **Dettagli** in **Azioni** nella vista di monitoraggio delle attività. Vengono visualizzate informazioni come il volume dei dati copiati dall'origine al sink, la velocità effettiva, i passaggi eseguiti (con la relativa durata) e le configurazioni usate.

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Connettore Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)