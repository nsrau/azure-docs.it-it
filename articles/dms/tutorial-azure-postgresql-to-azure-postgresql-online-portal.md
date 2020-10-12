---
title: 'Esercitazione: Eseguire la migrazione online del database di Azure per PostgreSQL al database di Azure per PostgreSQL tramite il portale di Azure'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione online da un database di Azure per PostgreSQL a un altro database di Azure per PostgreSQL con il Servizio Migrazione del database di Azure tramite il portale di Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 713b1698bff703507f46e1a8f76c6be385f41ec5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282461"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server-or-hyperscale-citus-online-using-dms-via-the-azure-portal"></a>Esercitazione: Eseguire la migrazione online di un database di Azure per PostgreSQL - Server singolo a un database di Azure per PostgreSQL - Server singolo o Hyperscale (Citus) con il Servizio Migrazione del database tramite il portale di Azure

È possibile usare il Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di [Database di Azure per PostgreSQL - Server singolo](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) a un'istanza di [Hyperscale (Citus) in Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) con tempi di inattività minimi. In questa esercitazione si esegue la migrazione del database di esempio **DVD Rental** dal Database di Azure per PostgreSQL v10 ad Hyperscale (Citus) in Database di Azure per PostgreSQL usando l'attività di migrazione online in Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio con l'utilità pg_dump.
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione nel Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Eseguire la migrazione completa.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Il disco viene crittografato per impedire il furto dei dati durante il processo di migrazione

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

> [!IMPORTANT]
> La migrazione da Database di Azure per PostgreSQL è supportata per PostgreSQL versione 10 e successive. È anche possibile usare questa esercitazione per eseguire la migrazione da un'istanza di Database di Azure per PostgreSQL a un'altra istanza di Database di Azure per PostgreSQL o a un'istanza Hyperscale (Citus).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Controllare lo [stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status) per le combinazioni di migrazione e versione supportate. 
* Un'istanza di [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/) versione 10 e successive con il database **DVD Rental**. Il Servizio Migrazione del database di Azure non supporta la migrazione dal database di Azure per PostgreSQL 9.5 o 9.6.

    Si noti anche che la versione del database di Azure per PostgreSQL di destinazione deve essere uguale o successiva alla versione locale di PostgreSQL. Ad esempio, per PostgreSQL 10 è possibile eseguire la migrazione al Database di Azure per PostgreSQL 10 o 11 ma non al Database di Azure per PostgreSQL 9.6.

* [Creare un server di database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) o [Creare un server di database di Azure per PostgreSQL - Hyperscale (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) come server di database di destinazione in cui eseguire la migrazione dei dati.
* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager. Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

* Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso seguenti nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello del server per l'origine del Database di Azure per PostgreSQL per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per la destinazione del Database di Azure per PostgreSQL per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
* Impostare i parametri del server seguenti nell'istanza di Database di Azure per PostgreSQL usata come origine:

  * max_replication_slots = [numero di slot]. È consigliabile impostare **cinque slot**
  * max_wal_senders = [numero di attività simultanee]: il parametro max_wal_senders imposta il numero di attività simultanee che è possibile eseguire, si consiglia di impostare il valore su **10 attività**

> [!NOTE]
> I parametri del server precedenti sono statici e richiederanno un riavvio dell'istanza di Database di Azure per PostgreSQL per renderli effettivi. Per altre informazioni sull'attivazione/disattivazione dei parametri del server, vedere [Configurare i parametri del server di Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal).

> [!IMPORTANT]
> Per tutte le tabelle nel database esistente è necessaria una chiave primaria per garantire che le modifiche possano essere sincronizzate con il database di destinazione.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Per completare tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database.

1. Usare il comando pg_dump -s per creare un file di dump dello schema per un database.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ad esempio, per creare un file di dump dello schema per il database **dvdrental**:

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Per altre informazioni sull'uso dell'utilità pg_dump, vedere gli esempi riportati nell'esercitazione su [pg dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Creare un database vuoto nell'ambiente di destinazione, ovvero il Database di Azure per PostgreSQL.

    Per informazioni dettagliate su come connettere e creare un database, vedere l'articolo [Creare un server di Database di Azure per PostgreSQL nel portale di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) o [Creare un server di Database di Azure per PostgreSQL - Hyperscale (Citus) nel portale di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Un'istanza di Database di Azure per PostgreSQL - Hyperscale (Citus) contiene solo un database singolo: **citus**.

3. Importare lo schema nel database di destinazione che è stato creato mediante il ripristino del file di dump dello schema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Ad esempio:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Per estrarre lo script di eliminazione della chiave esterna e aggiungerlo alla destinazione (Database di Azure per PostgreSQL), eseguire lo script seguente in PgAdmin o in psql.

   > [!IMPORTANT]
   > A causa delle chiavi esterne nello schema, il caricamento iniziale e la sincronizzazione continua della migrazione avranno esito negativo.

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

6. Per disabilitare i trigger nel database di destinazione, eseguire questo script.

   > [!IMPORTANT]
   > I trigger di inserimento o di aggiornamento presenti nei dati impongono l'integrità dei dati nella destinazione prima che i dati vengano replicati dall'origine. Di conseguenza, è consigliabile disabilitare i trigger in tutte le tabelle **nella destinazione** durante la migrazione e quindi abilitare di nuovo i trigger al termine della migrazione.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Creare un'istanza del servizio Migrazione del database

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome, la sottoscrizione, un gruppo di risorse nuovo o esistente e la località per il servizio.

4. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere al server PostgreSQL di origine e all'istanza di Database di Azure per PostgreSQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](https://aka.ms/DMSVnet).

5. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selezionare **Rivedi e crea** per creare il servizio.

   La creazione del servizio viene completata entro 10-15 minuti.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza di Servizio Migrazione del database di Azure appena creata, selezionare l'istanza e quindi selezionare + **Nuovo progetto di migrazione**.

3. Nella schermata **Nuovo progetto di migrazione** specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **PostgreSQL** e nella casella di testo **Tipo del server di destinazione** selezionare **Database di Azure per PostgreSQL**.
    > [!NOTE]
    > Scegliere **PostgreSQL** in **Tipo del server di origine** anche se il server di origine è un'istanza di **Database di Azure per PostgreSQL**.  

4. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

    ![Creare un progetto del Servizio Migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

5. Selezionare **Salva**. Prendere nota dei requisiti per usare correttamente il Servizio Migrazione del database di Azure per la migrazione dei dati e quindi selezionare **Crea ed esegui attività**.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Aggiungi dettagli origine** specificare i dettagli di connessione per l'istanza PostgreSQL di origine.

    ![Schermata Aggiungi dettagli origine](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Le informazioni dettagliate, ad esempio "Nome del server", "Porta del server", "Nome database" e così via, sono disponibili portale del **Database di Azure per PostgreSQL**.

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione** specificare i dettagli di connessione per il server Hyperscale (Citus) di destinazione, ovvero l'istanza di Hyperscale (Citus) di stato eseguito il provisioning e in cui è stato distribuito lo schema **DVD Rentals** tramite pg_dump.

    ![Schermata Dettagli destinazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > È possibile eseguire la migrazione da un'istanza di Database di Azure per PostgreSQL a un'altra istanza di Database di Azure per PostgreSQL - Server singolo o a un server Hyperscale (Citus).

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Schermata Mappa ai database di destinazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selezionare **Salva**, quindi nella schermata **Impostazioni di migrazione** accettare i valori predefiniti.

    ![Schermata Impostazioni di migrazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Schermata Riepilogo della migrazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Verrà visualizzata la finestra dell'attività di migrazione con lo **Stato** dell'attività che passerà a **Backup in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

     ![Monitorare il processo di migrazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Al termine della migrazione, in **Nome database** selezionare un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo dei dati** e **Sincronizzazione dei dati incrementale**.

   > [!NOTE]
   > **Caricamento completo** indica lo stato di migrazione del carico iniziale, mentre **Sincronizzazione dei dati incrementale** indica lo stato di Change Data Capture (CDC).

     ![Dettagli caricamento completo dei dati](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Dettagli sincronizzazione dei dati incrementale](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

2. Attendere che il contatore **Modifiche in sospeso** mostri **0** per assicurarsi che tutte le transazioni in ingresso nel database di origine siano terminate, selezionare la casella di controllo **Conferma** e quindi selezionare **Applica**.

    ![Schermata Completa cutover](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando lo stato della migrazione del database è **Completato**, connettere le applicazioni alla nuova istanza di destinazione di Database di Azure per PostgreSQL.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti delle migrazioni online verso il Database di Azure per PostgreSQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per PostgreSQL](known-issues-azure-postgresql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni su Database di Azure per PostgreSQL, vedere l'articolo [Che cos'è Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
