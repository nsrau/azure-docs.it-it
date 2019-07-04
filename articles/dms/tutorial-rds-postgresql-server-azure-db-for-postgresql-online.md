---
title: 'Esercitazione: Usare il Servizio Migrazione del database di Azure per eseguire la migrazione online da RDS PostgreSQL a Database di Azure per PostgreSQL | Microsoft Docs'
description: Informazioni su come eseguire la migrazione online da RDS PostgreSQL a Database di Azure per PostgreSQL con il Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 17fb83bc845de61f7ec0e674f09c0dc73537f2fd
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461589"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Esercitazione: Eseguire la migrazione online da RDS PostgreSQL a Database di Azure per PostgreSQL con il Servizio Migrazione del database

È possibile usare il Servizio Migrazione del database di Azure per eseguire la migrazione di database da un'istanza di RDS PostgreSQL a [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/), mantenendo al tempo stesso online il database di origine durante la migrazione. In altre parole, la migrazione può essere eseguita con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **DVD Rental** da un'istanza locale di RDS PostgreSQL 9.6 a Database di Azure per PostgreSQL usando l'attività di migrazione online nel Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio con l'utilità pg_dump.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) di Servizio Migrazione del database di Azure.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive come eseguire una migrazione online da un'istanza locale di PostgreSQL a Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Scaricare e installare [PostgreSQL community edition](https://www.postgresql.org/download/) 9.5, 9.6 o 10. La versione del server PostgreSQL di origine deve essere 9.5.11, 9.6.7, 10 o successiva. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Inoltre, la versione di RDS PostgreSQL deve corrispondere alla versione di Database di Azure per PostgreSQL. Ad esempio, per RDS PostgreSQL 9.5.11.5 è possibile eseguire la migrazione solo alla versione 9.5.11 di Database di Azure per PostgreSQL e non alla versione 9.6.7.

    > [!NOTE]
    > Per PostgreSQL versione 10, il Servizio Migrazione del database supporta attualmente solo la migrazione della versione 10.3 a Database di Azure per PostgreSQL.

* Creare un'istanza di [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal). Fare riferimento a questa [sezione](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) del documento per informazioni dettagliate su come connettersi al server PostgreSQL usando pgAdmin.
* Creare una rete virtuale di Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni sulla creazione di una rete virtuale, vedere [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.
* Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445 e 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere al server PostgreSQL di origine, per impostazione predefinita attraverso la porta TCP 5432.
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) per il server di Database di Azure per PostgreSQL per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Configurare AWS RDS PostgreSQL per la replica

1. Per creare un nuovo gruppo di parametri, seguire le istruzioni fornite da AWS nell'articolo [Uso di gruppi di parametri database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Usare il nome utente master per connettersi all'origine dal Servizio Migrazione del database di Azure. Se si usa un account utente diverso dal master, questo deve disporre dei ruoli rds_superuser e rds_replication. Il ruolo rds_replication concede le autorizzazioni per gestire gli slot logici e trasmettere dati tramite questi slot.
3. Creare un nuovo gruppo di parametri con la configurazione seguente: a. Impostare il parametro rds.logical_replication nel gruppo di parametri DB su 1.
    b. max_wal_senders = [numero di attività simultanee]: il parametro max_wal_senders imposta il numero di attività simultanee che è possibile eseguire. È consigliabile impostare 10 attività.
    c. max_replication_slots = [numero di slot]. È consigliabile impostare 5 slot.
4. Associare il gruppo di parametri creato all'istanza di RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Eseguire la migrazione dello schema

1. Estrarre lo schema dal database di origine e applicarlo al database di destinazione per completare la migrazione di tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure.

    Il modo più semplice per eseguire la migrazione solo dello schema è quello di usare pg_dump con l'opzione -s. Per altre informazioni, vedere gli [esempi](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) nell'esercitazione relativa a pg_dump per Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ad esempio, per eseguire il dump di un file dello schema per il database **dvdrental**, usare il comando seguente:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Creare un database vuoto nel servizio di destinazione, ovvero Database di Azure per PostgreSQL. Per connettersi e creare un database, fare riferimento a uno degli articoli seguenti:

    * [Creare un server di Database di Azure per PostgreSQL nel portale di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Creare un Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. Importare lo schema nel servizio di destinazione, ovvero Database di Azure per PostgreSQL. Per ripristinare il file di dump dello schema, eseguire il comando seguente:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Ad esempio:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se nello schema sono presenti chiavi esterne, il caricamento iniziale e la sincronizzazione continua della migrazione avrà esito negativo. Per estrarre lo script di eliminazione della chiave esterna e aggiungere lo script della chiave esterna nel database di destinazione (Database di Azure per PostgreSQL), eseguire lo script seguente in PgAdmin o in psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query per eliminare la chiave esterna.

6. Se nei dati sono presenti trigger (di inserimento o di aggiornamento), prima di replicare i dati dall'origine nel database di destinazione verrà applicata l'integrità dei dati. È consigliabile disabilitare i trigger in tutte le tabelle *del database di destinazione* durante la migrazione e quindi riabilitarli al termine della migrazione.

    Per disabilitare i trigger nel database di destinazione:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di PostgreSQL di origine e all'istanza di Database di Azure per PostgreSQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

6. Selezionare un piano tariffario. Per questa migrazione online assicurarsi di selezionare il piano tariffario Premium: 4 vCore.

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

     ![Individuare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Selezionare **+ Nuovo progetto di migrazione**.
4. Nella schermata **Nuovo progetto di migrazione** specificare il nome del progetto, quindi selezionare **AWS RDS per PostgreSQL** nella casella di testo **Tipo del server di origine** e **Database di Azure per PostgreSQL** nella casella di testo **Tipo del server di destinazione**.
5. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

    > [!IMPORTANT]
    > Assicurarsi di selezionare **Migrazione dei dati online**; le migrazioni offline non sono supportate per questo scenario.

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

6. Selezionare **Salva**.

7. Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

    > [!NOTE]
    > Annotare i prerequisiti necessari per configurare la migrazione online nel pannello di creazione del progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

* Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di PostgreSQL di origine.

   ![Dettagli origine](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Selezionare **Salva** e quindi nella schermata **Dettagli destinazione** specificare i dettagli di connessione per il server di Database di Azure per PostgreSQL di destinazione, di cui è già stato eseguito il provisioning e in cui è stato distribuito lo schema **DVD Rentals** tramite pg_dump.

    ![Selezionare la destinazione](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **In esecuzione**.

    ![Stato attività: in esecuzione](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. In **NOME DATABASE** selezionare un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo** e **Sincronizzazione dei dati incrementale**.

    **Caricamento completo** indica lo stato di migrazione del carico iniziale, mentre **Sincronizzazione dei dati incrementale** indica lo stato di Change Data Capture (CDC).

    ![Schermata dell'inventario: caricamento dei dati completo](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Schermata dell'inventario: sincronizzazione dei dati incrementale](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

    ![Avviare il cutover](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. Assicurarsi di arrestare tutte le transazioni in ingresso nel database di origine. Attendere finché il contatore **Modifiche in sospeso** mostra **0**.
3. Selezionare **Conferma** e quindi **Applica**.
4. Quando lo stato della migrazione del database è **Completato**, connettere le applicazioni alla nuova istanza di Database di Azure per PostgreSQL di destinazione.

La migrazione online di un'istanza locale di PostgreSQL in Database di Azure per PostgreSQL è stata completata.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni su Database di Azure per PostgreSQL, vedere l'articolo [Che cos'è Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
* Per altre domande, inviare un messaggio di posta elettronica all'alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
