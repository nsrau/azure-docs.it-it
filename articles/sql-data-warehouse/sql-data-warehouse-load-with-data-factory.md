---
title: "Caricare i dati in Azure SQL Data Warehouse – Data factory | Documentazione Microsoft"
description: Questa esercitazione carica i dati in Azure SQL Data Warehouse tramite Data factory di Azure e usa un database SQL Server come origine dati.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Caricare i dati in SQL Data Warehouse

È possibile usare Azure Data Factory per caricare dati in Azure SQL Data Warehouse da uno qualsiasi degli [archivi dati di origine supportati](../data-factory/copy-activity-overview.md). Ad esempio, i dati possono essere caricati da un database SQL di Azure o da un database Oracle in SQL Data Warehouse tramite Data Factory. L'esercitazione contenuta in questo articolo mostra come caricare dati da un database di SQL Server locale in SQL Data Warehouse.

**Tempo stimato**: per completare questa esercitazione sono necessari circa 10-15 minuti una volta soddisfatti i prerequisiti.

## <a name="prerequisites"></a>Prerequisiti

- È necessario un **database di SQL Server** con tabelle contenenti i dati da copiare in SQL Data Warehouse.  

- È necessario un **SQL Data Warehouse** online. Se non si dispone di un data warehouse, vedere l'articolo relativo alla [creazione di un'istanza di Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).

- È necessario un **account di archiviazione di Azure**. Se non si dispone di un account di archiviazione, vedere l'articolo relativo alla [creazione di un account di archiviazione](../storage/common/storage-create-storage-account.md). Per le migliori prestazioni, posizionare l'account di archiviazione e il data warehouse nella stessa area si Azure.

## <a name="configure-a-data-factory"></a>Configurare una data factory
1. Accedere al [Portale di Azure][].
2. Individuare il data warehouse e fare clic per aprirlo.
3. Nel pannello principale fare clic su **Carica dati** > **Data factory di Azure**.

    ![Avviare la procedura guidata di caricamento dei dati](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Se non si dispone di una data factory nella sottoscrizione di Azure, verrà visualizzata una finestra di dialogo **Nuova data factory** in una scheda separata del browser. Inserire le informazioni richieste e fare clic su **Crea**. Dopo aver creato la data factory, la finestra di dialogo **Nuova data factory** verrà chiusa e sarà visualizzata la finestra di dialogo **Select Data Factory** (Seleziona data factory).

    Se si dispone già di una o più data factory nella sottoscrizione di Azure, verrà visualizzata la finestra di dialogo **Select Data Factory** (Seleziona data factory). In questa finestra di dialogo è possibile selezionare una data factory esistente o fare clic su **Create new data factory** (Crea nuova data factory) per crearne una nuova.

    ![Configurare una data factory](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. Nella finestra di dialogo **Select Data Factory** (Seleziona data factory) l'opzione **Carica dati** è selezionata per impostazione predefinita. Fare clic su **Avanti** per avviare la creazione di un'attività di caricamento dei dati.

## <a name="configure-the-data-factory-properties"></a>Configurare le proprietà della data factory
Dopo avere creato una data factory, il passaggio successivo consiste nel configurare la pianificazione di caricamento dei dati.

1. Per **Nome attività** immettere **DWLoadData-fromSQLServer**.
2. Selezionare l'opzione **Esegui una volta** e fare clic su **Avanti**.

    ![Configurare il bilanciamento del carico](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Configurare l'archivio dati e il gateway di origine
Ora indicare alla data factory il database SQL Server locale da cui si desidera caricare i dati.

1. Scegliere **SQL Server** dal catalogo dell'archivio dati di origine supportato e fare clic su **Avanti**.

    ![Scegliere l'origine SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. Verrà visualizzata la finestra di dialogo **Specify the on-premises SQL Server database** (Specificare il database SQL Server locale). Il primo campo **Nome connessione** viene compilato automaticamente. Il secondo campo richiede il nome del **gateway**. Se si usa una data factory esistente che dispone già di un gateway, è possibile riusarlo selezionandolo dall'elenco a discesa. Fare clic sul collegamento **Crea gateway** per creare un gateway di gestione dati.  

    > [!NOTE]
    > Se l'archivio dati di origine è locale o in una macchina virtuale IaaS di Azure, è necessario un gateway di gestione dati. Un gateway ha una relazione 1-1 con una data factory. Non può essere usato da un'altra data factory, ma può essere usato da più attività di caricamento dei dati nella stessa data factory. Un gateway consente di connettersi a più archivi di dati durante l'esecuzione di attività di caricamento dei dati.
    >
    > Per informazioni dettagliate sul gateway, vedere l'articolo [Gateway di gestione dati](../data-factory/v1/data-factory-data-management-gateway.md).

3. Verrà visualizzata la finestra di dialogo **Crea gateway**. Come nome immettere **GatewayForDWLoading** e fare clic su **Crea**.

4. Verrà visualizzata la finestra di dialogo **Configure Gateway** (Configura gateway). Fare clic su **Launch express setup on this computer** (Avvia installazione rapida sul computer) per scaricare, installare e registrare automaticamente il gateway di gestione dati nel computer corrente. Lo stato di avanzamento viene visualizzato in una finestra popup. Se il computer non riesce a connettersi all'archivio dati, è possibile [scaricare e installare il gateway](https://www.microsoft.com/download/details.aspx?id=39717) manualmente in un computer in grado di connettersi all'archivio dati e quindi usare la chiave per la registrazione.
    > [!NOTE]
    > L'installazione rapida funziona in modo nativo con Microsoft Edge e Internet Explorer. Se si usa Google Chrome, installare innanzitutto l'estensione ClickOnce dal web store Chrome.

    ![Avviare l'installazione rapida](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Attendere il completamento dell'installazione del gateway. Una volta che il gateway è stato registrato correttamente ed è online, la finestra popup viene chiusa e il nuovo gateway viene visualizzato nel campo del gateway. Quindi compilare come segue i restanti campi obbligatori e fare clic su **Avanti**.
    - **Nome del server**: nome del server SQL locale.
    - **Nome del database**: database SQL Server.
    - **Crittografia delle credenziali**: usare il valore predefinito dal web browser.
    - **Tipo di autenticazione**: scegliere il tipo di autenticazione in uso.
    - **Nome utente** e **Password**: immettere il nome utente e la password per un utente che dispone dell'autorizzazione per copiare i dati.

    ![Avviare l'installazione rapida](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. Il passaggio successivo consiste nello scegliere le tabelle da cui copiare i dati. È possibile filtrare le tabelle usando parole chiave. Ed è possibile visualizzare in anteprima lo schema dei dati e della tabella nel riquadro inferiore. Dopo aver completato la selezione, fare clic su **Avanti**.

    ![Selezionare le tabelle](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Configurare la destinazione, ovvero SQL Data Warehouse

Ora informare Data Factory sulle informazioni di destinazione.

1. Le informazioni di connessione di SQL Data Warehouse sono inserite automaticamente. Immettere il nome utente e la password e fare clic su **Avanti**.

    ![Configurare la destinazione](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Viene visualizzato un mapping intelligente delle tabelle che associa le tabelle di origine a quelle di destinazione in base ai nomi di tabella. Se la tabella non esiste nella destinazione, per impostazione predefinita Azure Data Factory ne crea una con lo stesso nome; si applica a SQL Server o al database SQL di Azure come origine. È possibile anche eseguire il mapping a una tabella esistente. Controllare le associazioni e fare clic su **Avanti**.

    ![Eseguire il mapping delle tabelle](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Esaminare il mapping dello schema e cercare eventuali messaggi di errore o di avviso. Il mapping intelligente è basato sui nomi delle colonne. Se è presente una conversione di tipo di dati non supportata tra la colonna di origine e di destinazione, viene visualizzato un messaggio di errore insieme della tabella corrispondente. Se si sceglie di consentire a Data Factory di creare automaticamente le tabelle, può essere applicata un'adeguata conversione del tipo di dati se è necessario per risolvere eventuali incompatibilità tra gli archivi di origine e di destinazione.

    ![Eseguire il mapping dello schema](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Fare clic su **Avanti**.

## <a name="configure-the-performance-settings"></a>Configurare le impostazioni delle prestazioni
Nelle configurazioni relative alle prestazioni configurare un account di archiviazione usato per la gestione temporanea dei dati prima di caricarli con efficacia in SQL Data Warehouse usando [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Al termine della copia, i dati provvisori nell'archiviazione verranno eliminati automaticamente.

Selezionare un account di archiviazione di Azure esistente e fare clic su **Avanti**.

![Configurare il BLOB di gestione temporanea](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Esaminare le informazioni di riepilogo e distribuire la pipeline

Esaminare la configurazione e fare clic sul pulsante **Fine** per distribuire la pipeline.

![Distribuire la data factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Monitorare lo stato di avanzamento del caricamento dei dati

È possibile visualizzare l'avanzamento e i risultati della distribuzione nella pagina **Distribuzione**.

1. Una volta terminata la distribuzione, fare clic sul collegamento **Click here to monitor copy pipeline** (Fare clic qui per monitorare la pipeline di copia) per monitorare l'avanzamento del caricamento dei dati.

    ![Monitorare la pipeline](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. La pipeline di caricamento dati **DWLoadData-fromSQLServer** appena creata viene selezionata automaticamente da **Esplora risorse** a sinistra.

    ![Visualizzare la pipeline](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Fare clic nella pipeline nel pannello centrale per vedere lo stato dettagliato per ogni tabella associata a un'attività.

    ![Visualizzare l'attività della tabella](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Fare clic su un'attività per visualizzare i dettagli di caricamento dei dati nel riquadro di destra, tra cui la dimensione dei dati, le righe, la velocità effettiva e così via.

    ![Visualizzare i dettagli dell'attività della tabella](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Per avviare questa visualizzazione di monitoraggio in seguito, accedere al proprio SQL Data Warehouse, fare clic su **Carica dati > Data factory di Azure**, selezionare la data factory e scegliere **Monitor existing loading tasks** (Monitora attività di caricamento esistenti).

## <a name="next-steps"></a>Passaggi successivi

Per eseguire la migrazione del database in SQL Data Warehouse, vedere [Eseguire la migrazione della soluzione in SQL Data Warehouse](sql-data-warehouse-overview-migrate.md).

Per altre informazioni su Azure Data Factory e le funzionalità di spostamento dei dati, vedere gli articoli seguenti:

- [Introduzione al servizio Azure Data Factory](../data-factory/introduction.md)
- [Spostare dati con l'attività di copia](../data-factory/copy-activity-overview.md)
- [Spostare dati da e verso Azure SQL Data Warehouse con Azure Data Factory](../data-factory/connector-azure-sql-data-warehouse.md)

Per esplorare i dati in SQL Data Warehouse, vedere gli articoli seguenti:

- [Connettersi a SQL Data Warehouse con Visual Studio e SSDT](sql-data-warehouse-query-visual-studio.md)
- [Visualizzare i dati con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

<!-- Azure references -->
[Portale di Azure]: https://portal.azure.com
