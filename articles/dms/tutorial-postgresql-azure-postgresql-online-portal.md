---
title: 'Esercitazione: Eseguire la migrazione di PostgreSQL al database di Azure per PostgreSQL online tramite il portale di AzureTutorial: Migrate PostgreSQL to Azure DB for PostgreSQL online via the Azure portal'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione online da PostgreSQL locale al database di Azure per PostgreSQL tramite il servizio migrazione del database di Azure tramite il portale di Azure.Learn to perform an online migration from PostgreSQL on-premises to Azure Database for PostgreSQL by using Azure Database Migration Service via the Azure portal.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114010"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Esercitazione: Eseguire la migrazione di PostgreSQL al database di Azure per PostgreSQL online tramite DMS tramite il portale di AzureTutorial: Migrate PostgreSQL to Azure DB for PostgreSQL online using DMS via the Azure portal

È possibile usare il servizio migrazione del database di Azure per eseguire la migrazione dei database da un'istanza PostgreSQL locale al database di [Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/) con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **DVD Rental** da un'istanza locale di PostgreSQL 9.6 a Database di Azure per PostgreSQL usando l'attività di migrazione online in Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio utilizzando l'utilità di pg_dump.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione nel servizio di migrazione del database di Azure.Create a migration project in Azure Database Migration Service.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Eseguire la migrazione cutover.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Crittografiamo il disco per prevenire il furto di dati durante il processo di migrazione

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Scaricare e installare [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 o 10. La versione di origine di PostgreSQL Server deve essere 9.4, 9.5, 9.6, 10 o 11.The source PostgreSQL Server version must be 9.4, 9.5, 9.6, 10, or 11. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Si noti inoltre che il database di Azure di destinazione per la versione PostgreSQL deve essere uguale o successiva alla versione PostgreSQL locale. Ad esempio, PostgreSQL 9.6 può eseguire la migrazione al database di Azure per PostgreSQL 9.6, 10 o 11, ma non al database di Azure per PostgreSQL 9.5.For example, PostgreSQL 9.6 can migrate to Azure Database for PostgreSQL 9.6, 10 or 11, but not to Azure Database for PostgreSQL 9.5.

* Creare un database di Azure per il [server PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) o creare un database di [Azure per il server PostgreSQL - Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Creare una rete virtuale di Microsoft Azure per il servizio di migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Per ulteriori informazioni sulla creazione di una rete virtuale, vedere gli articoli relativi alla [documentazione](https://docs.microsoft.com/azure/virtual-network/)relativa alla rete virtuale e in particolare gli articoli delle guide introduttive con dettagli dettagliati.

    > [!NOTE]
    > Durante l'installazione della rete virtuale, se si usa ExpressRoute con peering di rete a Microsoft, aggiungere gli endpoint del servizio seguenti alla subnet in cui verrà eseguito il provisioning del servizio:During virtual network setup, if you use ExpressRoute with network peering to Microsoft, add the following service [endpoints](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) to the subnet in which the service will be provisioned:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Verificare che le regole del gruppo di sicurezza di rete (NSG) per la rete virtuale non blocchino le seguenti porte di comunicazione in ingresso al servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000.Ensure that the Network Security Group (NSG) rules for your virtual network't block the following inbound communication ports to Azure Database Migration Service: 443, 53, 9354, 445, 12000. Per ulteriori informazioni sul filtro del traffico del gruppo di sicurezza di rete virtuale, vedere l'articolo [Filtrare](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)il traffico di rete con i gruppi di sicurezza di rete .
* Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere al server PostgreSQL di origine, per impostazione predefinita attraverso la porta TCP 5432.
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per Database di Azure per PostgreSQL per consentire a Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usata per il servizio di migrazione del database di Azure.Provide the subnet range of the virtual network used for Azure Database Migration Service.
* Abilitare la replica logica nel file postgresql.config e impostare i parametri seguenti:

  * wal_level = **logical**
  * max_replication_slots = [numero di slot]. È consigliabile impostare **cinque slot**
  * max_wal_senders = [numero di attività simultanee]: il parametro max_wal_senders imposta il numero di attività simultanee che è possibile eseguire, si consiglia di impostare il valore su **10 attività**

> [!IMPORTANT]
> Tutte le tabelle nel database esistente necessitano di una chiave primaria per garantire che le modifiche possano essere sincronizzate con il database di destinazione.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Per completare tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database.

1. Usare il comando pg_dump -s per creare un file di dump dello schema per un database.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ad esempio, per creare un file di dump dello schema per il database **dvdrental:For** example, to create a schema dump file for the dvdrental database:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Per altre informazioni sull'uso dell'utilità pg_dump, vedere gli esempi riportati nell'esercitazione su [pg dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Creare un database vuoto nell'ambiente di destinazione, ovvero il Database di Azure per PostgreSQL.

    Per informazioni dettagliate su come connettersi e creare un database, vedere l'articolo Creare un database di [Azure per il server PostgreSQL nel portale](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) di Azure o Creare un database di Azure per il server [PostgreSQL - Hyperscale (Citus) nel portale](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)di Azure.

    > [!NOTE]
    > Un'istanza del database di Azure per PostgreSQL - Hyperscale (Citus) ha un solo database: **citus**.

3. Importare lo schema nel database di destinazione che è stato creato mediante il ripristino del file di dump dello schema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Ad esempio:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Per estrarre lo script di chiave esterna esterna e aggiungerlo nella destinazione (Database di Azure per PostgreSQL), in PgAdmin o in psql, eseguire lo script seguente.

   > [!IMPORTANT]
   > Le chiavi esterne nello schema causeranno l'esito negativo del caricamento iniziale e della sincronizzazione continua della migrazione.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query.

6. Per disabilitare i trigger nel database di destinazione, eseguire lo script seguente.

   > [!IMPORTANT]
   > I trigger (inserimento o aggiornamento) nei dati applicano l'integrità dei dati nella destinazione prima dei dati replicati dall'origine. Di conseguenza, è consigliabile disabilitare i trigger in tutte le tabelle **nella destinazione** durante la migrazione e quindi riattivare i trigger al termine della migrazione.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Cercare la migrazione e quindi a destra di **Microsoft.DataMigration**, selezionare **Registra**.

    ![Registrare il provider di risorse](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Creare un'istanza del servizio Migrazione del database

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Nella schermata **Crea servizio** di migrazione specificare un nome, la sottoscrizione, un gruppo di risorse nuovo o esistente e il percorso del servizio.

4. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale fornisce al servizio di migrazione del database di Azure l'accesso al server PostgreSQL di origine e al database di Azure di destinazione per l'istanza PostgreSQL.The virtual network provides Azure Database Migration Service with access to the source PostgreSQL server and the target Azure Database for PostgreSQL instance.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo Creare una rete virtuale usando il portale di Azure.For more information about how to create a virtual network in the Azure portal, see the article [Create a virtual network using the Azure portal.](https://aka.ms/DMSVnet)

5. Selezione di un piano tariffario.

    Per ulteriori informazioni sui costi e sui piani tariffari, vedere la [pagina dei prezzi](https://aka.ms/dms-pricing).

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selezionare **Revisione : crea** per creare il servizio.

   La creazione del servizio verrà completata entro circa 10-15 minuti.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Nella schermata **Servizi di migrazione database** di Azure cercare il nome dell'istanza del servizio migrazione del database di Azure creata, selezionare l'istanza e quindi selezionare Nuovo progetto di **migrazione.**

3. Nella schermata **Nuovo progetto** di migrazione specificare un nome per il progetto, nella casella di testo Tipo di server di **origine** selezionare **PostgresSQL**, nella casella di testo **Tipo di server** di destinazione selezionare Database di Azure **per PostgreSQL**.

4. Nella sezione Scegliere il **tipo di attività** selezionare Migrazione dei dati **online**.

    ![Creare il progetto del servizio di migrazione del database di AzureCreate Azure Database Migration Service project](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

5. Selezionare **Salva**, prendere nota dei requisiti per usare correttamente il servizio Migrazione del database di Azure per eseguire la migrazione dei dati e quindi selezionare **Crea ed esegui attività**.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Aggiungi dettagli origine** specificare i dettagli di connessione per l'istanza PostgreSQL di origine.

    ![Schermata Aggiungi dettagli origine](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione** specificare i dettagli di connessione per il server Hyperscale (Citus) di destinazione, ovvero l'istanza di pre-provisioning di Hyperscale (Citus) in cui è stato distribuito lo schema **DVD Rentals** tramite pg_dump.

    ![Schermata Dettagli destinazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Schermata Mappa a database di destinazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selezionare **Salva**e quindi nella schermata **Impostazioni di migrazione** accettare i valori predefiniti.

    ![Schermata Impostazioni di migrazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Schermata di riepilogo della migrazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Viene visualizzata la finestra dell'attività di migrazione e lo **stato** dell'attività dovrebbe essere aggiornato in modo da essere visualizzato come **Backup in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

     ![Monitorare il processo di migrazioneMonitor migration process](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Al termine della migrazione, in **Nome database**selezionare un database specifico per ottenere lo stato della migrazione per **operazioni Di caricamento dati completo** e **Sincronizzazione dati incrementale.**

   > [!NOTE]
   > **Caricamento completo** indica lo stato di migrazione del carico iniziale, mentre **Sincronizzazione dei dati incrementale** indica lo stato di Change Data Capture (CDC).

     ![Dettagli completi sul caricamento dei dati](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Dettagli di sincronizzazione dati incrementali](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

2. Attendere che il contatore **Modifiche in sospeso** mostri **0** per assicurarsi che tutte le transazioni in entrata nel database di origine vengano arrestate, selezionare la casella di controllo **Conferma** e quindi selezionare **Applica**.

    ![Schermo completo di taglio](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando lo stato della migrazione del database è **Completato**, connettere le applicazioni alla nuova istanza di destinazione del database di Azure per PostgreSQL.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti delle migrazioni online verso il Database di Azure per PostgreSQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per PostgreSQL](known-issues-azure-postgresql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni su Database di Azure per PostgreSQL, vedere l'articolo [Che cos'è Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
