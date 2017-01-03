---

title: "Caricare i dati in Azure SQL Data Warehouse – Data factory | Documentazione Microsoft"
description: Questa esercitazione carica i dati in Azure SQL Data Warehouse tramite Data factory di Azure e usa un database SQL Server come origine dati.
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b46ac604c10a2cb014991f3707fc3fc3b300f772
ms.openlocfilehash: 5b045ed236771919ea3f644a6b2351d54c75549b


---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Caricare i dati in SQL Data Warehouse

Questa esercitazione carica i dati in Azure SQL Data Warehouse tramite Data factory di Azure e usa un database SQL Server come origine dati. Al termine i dati si trovano in SQL Data Warehouse.

**Tempo stimato**: per completare questa esercitazione sono necessari circa 10-15 minuti una volta soddisfatti i prerequisiti.

## <a name="prerequisites"></a>Prerequisiti

- L'esercitazione presuppone che si conoscano le nozioni di base sull'utilizzo di Transact-SQL per creare tabelle e schemi.  

- È necessario un account di archiviazione di Azure. È possibile [aprire un account Azure gratuito](/pricing/free-trial/?WT.mc_id=A261C142F) o [attivare i benefici della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

- È necessario un SQL Data Warehouse online. Se non si dispone di un data warehouse, vedere l'articolo relativo alla [creazione di un'istanza di Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md). Per le migliori prestazioni, posizionare l'account di archiviazione e il data warehouse nella stessa area si Azure.

- Preparare il data warehouse per ricevere i dati in ingresso mediante la creazione di uno o più schemi di tabella. È possibile usare l'[utilità di migrazione di SQL Data Warehouse](sql-data-warehouse-migrate-migration-utility.md) per creare uno script per gli schemi. 

## <a name="configure-a-new-data-factory"></a>Configurare una nuova data factory
1. Accedere al [Portale di Azure][].
2. Individuare il data warehouse e fare clic per aprirlo. 
3. Nel pannello **Proprietà** fare clic su **Carica dati > Data factory di Azure**.

    ![Avviare la procedura guidata di caricamento dei dati](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Viene visualizzata la finestra di dialogo **Nuova data factory**. Inserire le informazioni richieste o scegliere una data factory esistente. Fare clic su **Crea**.

5. Nella finestra di dialogo **Select Data Factory** (Seleziona data factory) l'opzione **Carica dati** è selezionata per impostazione predefinita. Fare clic su **Avanti** per completare la creazione della data factory. 

## <a name="configure-the-data-factory-properties"></a>Configurare le proprietà della data factory
Dopo avere creato una data factory, il passaggio successivo consiste nel configurare la pianificazione di caricamento dei dati. 

1. Selezionare **Proprietà** e inserire le informazioni richieste.
2. Per **Nome attività** immettere **DWLoadData-fromSQLServer**.
3. Fare clic su **Next**.

    ![Configurare il bilanciamento del carico](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-data-factory-source-and-gateway"></a>Configurare l'origine e il gateway della data factory
Ora indicare alla data factory il database SQL Server locale da cui si desidera caricare i dati.

1. Fare clic su **Origine**.
2. Scegliere **SQL Server** dal catalogo dell'archivio dati di origine supportato e fare clic su **Avanti**.

    ![Scegliere l'origine SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

3. Verrà visualizzata la finestra di dialogo **Specify the on-premises SQL Server database** (Specificare il database SQL Server locale). Compilare i campi obbligatori come segue:

    - **Nome della connessione**: specificare un nuovo nome per la connessione.
    - **Nome del server**: nome del server SQL locale.
    - **Nome del database**: database SQL Server.
    - **Crittografia delle credenziali**: nessuna. 
    - **Tipo di autenticazione**: scegliere il tipo di autenticazione in uso.
    - **Nome utente** e **Password**: immettere il nome utente e la password per un utente che dispone dell'autorizzazione per copiare i dati.

4. L'ultimo campo richiede il nome del gateway. Fare clic sul collegamento **Crea gateway** per creare un gateway di gestione dati. Il gateway di gestione dati è un agente client che deve essere installato nell'ambiente locale per copiare i dati tra archivi dati cloud e locali. 

5. Verrà visualizzata la finestra di dialogo **Crea gateway**. Come nome immettere **GatewayForDWLoading** e fare clic su **Crea**.

6. Verrà visualizzata la finestra di dialogo **Configure Gateway** (Configura gateway).  
    ![Avviare l'installazione rapida](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

7. Fare clic su **Launch express setup on this computer** (Avvia installazione rapida sul computer) per scaricare, installare e registrare il gateway nel computer corrente. Lo stato di avanzamento viene visualizzato in una finestra popup.

    L'installazione rapida funziona in modo nativo con Microsoft Edge e Internet Explorer. Se si usa Google Chrome, installare innanzitutto l'estensione ClickOnce dal web store Chrome. In alternativa è possibile scaricare e installare manualmente il gateway, quindi usare la chiave per la registrazione.

8. Attendere il completamento dell'installazione del gateway. Una volta che il gateway è stato registrato correttamente ed è online, la finestra popup viene chiusa e il nuovo gateway viene visualizzato nel campo del gateway. Fare clic su **Next**.

9. Il passaggio successivo consiste nello scegliere le tabelle da cui copiare i dati. È possibile filtrare le tabelle usando parole chiave. Ed è possibile visualizzare in anteprima lo schema dei dati e della tabella nel riquadro inferiore. Dopo aver completato la selezione, fare clic su **Avanti**.

    ![Selezionare le tabelle](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Configurare la destinazione, ovvero SQL Data Warehouse

1. Fare clic su **Destinazione**. Le informazioni di connessione di SQL Data Warehouse sono inserite automaticamente.
2. Immettere il nome utente e la password e fare clic su **Avanti**.

    ![Configurare la destinazione](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

3. Viene visualizzato un mapping intelligente delle tabelle che associa le tabelle di origine a quelle di destinazione in base ai nomi simili. Aggiungere
4.  il mapping per qualsiasi tabella e quelle restanti saranno associate automaticamente sulla base dell'esempio. 
5. Controllare le associazioni e fare clic su **Avanti**.

    ![Eseguire il mapping delle tabelle](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

6. Esaminare il mapping dello schema e cercare eventuali messaggi di errore. Il mapping intelligente è basato sui nomi delle colonne. Se è presente una conversione di tipo di dati non supportata tra la colonna di origine e di destinazione, viene visualizzato un messaggio di errore insieme della tabella corrispondente.

    ![Eseguire il mapping dello schema](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

7. Fare clic su **Avanti**.

## <a name="configure-the-performance-settings"></a>Configurare le impostazioni delle prestazioni
Nelle configurazioni relative alle prestazioni configurare un account di archiviazione da usare per la gestione temporanea dei dati prima di caricarli in SQL Data Warehouse.

1. Fare clic su **Prestazioni**. 
2. Selezionare un account di archiviazione di Azure esistente e fare clic su **Avanti**.

    ![Configurare il BLOB di gestione temporanea](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Esaminare le informazioni di riepilogo e distribuire la pipeline

1. Fare clic su **Riepilogo** ed esaminare le informazioni.
2. Fare clic sul pulsante **Fine** per distribuire la pipeline.

    ![Distribuire la data factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Monitorare lo stato di avanzamento del caricamento dei dati

Al termine della distribuzione viene visualizzata l'opzione **Distribuzione** nel menu a sinistra. 

1. Fare clic su **Distribuzione**.
2. Per monitorare lo stato di avanzamento del caricamento dei dati, fare clic sul collegamento **Click here to monitor copy pipeline** (Fare clic qui per monitorare la pipeline di copia).

    ![Monitorare la pipeline](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

3. In **Esplora risorse** espandere i nodi di pipeline e fare clic sulla pipeline di caricamento dei dati
4. **DWLoadData-fromSQLServer** che è stata creata in questa esercitazione.

    ![Visualizzare la pipeline](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

5. Fare clic nella pipeline per vedere lo stato dettagliato per ogni tabella associata a un'attività.

    ![Visualizzare l'attività della tabella](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

6. Fare clic su un'attività per visualizzare i dettagli di caricamento dei dati nel riquadro di destra, tra cui la dimensione dei dati, le righe, la velocità effettiva e così via.

    ![Visualizzare i dettagli dell'attività della tabella](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

7. Per avviare questa visualizzazione di monitoraggio in seguito, accedere al proprio SQL Data Warehouse, fare clic su **Carica dati > Data factory di Azure**, selezionare la data factory e scegliere **Monitor existing loading tasks** (Monitora attività di caricamento esistenti).

## <a name="next-steps"></a>Passaggi successivi

Per eseguire la migrazione del database in SQL Data Warehouse, vedere [Eseguire la migrazione della soluzione in SQL Data Warehouse](sql-data-warehouse-overview-migrate.md).

Per altre informazioni sulle funzionalità di copia di Data factory di Azure, vedere [Introduzione al servizio Azure Data Factory, un servizio di integrazione dati nel cloud](../data-factory/data-factory-introduction.md) e [Spostare dati con l'attività di copia](../data-factory/data-factory-data-movement-activities.md).

Per esplorare i dati in SQL Data Warehouse, vedere [Connettersi a SQL Data Warehouse con Visual Studio e SSDT](sql-data-warehouse-query-visual-studio.md) e [Visualizzare i dati con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Portale di Azure]: https://portal.azure.com 


<!--HONumber=Nov16_HO4-->


