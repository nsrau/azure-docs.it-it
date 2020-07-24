---
title: 'Esercitazione: eseguire la migrazione del database di Azure per PostgreSQL al database di Azure per PostgreSQL online tramite il portale di Azure'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione in linea da un database di Azure per PostgreSQL a un altro database di Azure per PostgreSQL usando il servizio migrazione del database di Azure tramite il portale di Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 6a5415e12a5a063790077eeefdc9ea4d1487d68b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099465"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server-or-hyperscale-citus-online-using-dms-via-the-azure-portal"></a>Esercitazione: eseguire la migrazione di database di Azure per PostgreSQL-server singolo a database di Azure per PostgreSQL-server singolo o iperscalabile (CITUS) online con DMS tramite il portale di Azure

È possibile usare il servizio migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di [database di Azure per PostgreSQL-server singolo](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) a un'istanza di database di Azure per [PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) con tempi di inattività minimi. In questa esercitazione si esegue la migrazione del database di esempio di **noleggio DVD** da un database di Azure per PostgreSQL a iperscalabile (CITUS) nel database di Azure per PostgreSQL usando l'attività di migrazione online nel servizio migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio utilizzando l'utilità pg_dump.
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione nel servizio migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Eseguire la migrazione cutover.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Si crittografa il disco per impedire il furto dei dati durante il processo di migrazione

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

> [!IMPORTANT]
> La migrazione da database di Azure per PostgreSQL è supportata per PostgreSQL versione 10 e successive. È anche possibile usare questa esercitazione per eseguire la migrazione da un'istanza di database di Azure per PostgreSQL a un'altra istanza di database di Azure per PostgreSQL o iperscalabile (CITUS).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Verificare [lo stato degli scenari di migrazione supportati dal servizio migrazione del database di Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status) per le combinazioni di migrazione e versione supportate. 
* Un'istanza esistente [di database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/) versione 10 e successive con il database di **noleggio DVD** . Il servizio migrazione del database di Azure non supporta la migrazione dal database di Azure per PostgreSQL 9,5 o 9,6.

    Si noti anche che la versione del database di Azure per PostgreSQL di destinazione deve essere uguale o successiva alla versione locale di PostgreSQL. PostgreSQL 10, ad esempio, può eseguire la migrazione al database di Azure per PostgreSQL 10 o 11, ma non al database di Azure per PostgreSQL 9,6.

* [Creare un server di database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) o [creare un server di database di Azure per PostgreSQL-overscale (CITUS)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) come server di database di destinazione in cui eseguire la migrazione dei dati.
* Creare una Rete virtuale di Microsoft Azure per il servizio migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager. Per ulteriori informazioni sulla creazione di una rete virtuale, vedere la [documentazione relativa alla rete virtuale](https://docs.microsoft.com/azure/virtual-network/)e, in particolare, gli articoli introduttivi con informazioni dettagliate.

* Verificare che le regole del gruppo di sicurezza di rete (NSG) per la rete virtuale non blocchino le porte di comunicazione in ingresso seguenti al servizio migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni più dettagliate sul filtro del traffico NSG per la rete virtuale, vedere l'articolo [filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per l'origine database di Azure per PostgreSQL per consentire al servizio migrazione del database di Azure di accedere ai database di origine. Fornire l'intervallo di subnet della rete virtuale usata per il servizio migrazione del database di Azure.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per il database di Azure per PostgreSQL destinazione per consentire al servizio migrazione del database di Azure di accedere ai database di destinazione. Fornire l'intervallo di subnet della rete virtuale usata per il servizio migrazione del database di Azure.
* Impostare i parametri del server seguenti nell'istanza del database di Azure per PostgreSQL utilizzata come origine:

  * max_replication_slots = [numero di slot]. È consigliabile impostare **cinque slot**
  * max_wal_senders = [numero di attività simultanee]: il parametro max_wal_senders imposta il numero di attività simultanee che è possibile eseguire, si consiglia di impostare il valore su **10 attività**

> [!NOTE]
> I parametri del server precedenti sono statici ed è necessario riavviare l'istanza del database di Azure per PostgreSQL per renderli effettive. Per altre informazioni sull'attivazione e disattivazione dei parametri del server, vedere [configurare i parametri del server di database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal).

> [!IMPORTANT]
> Per tutte le tabelle nel database esistente è necessaria una chiave primaria per garantire che le modifiche possano essere sincronizzate con il database di destinazione.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Per completare tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database.

1. Usare il comando pg_dump -s per creare un file di dump dello schema per un database.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ad esempio, per creare un file di dump dello schema per il database **dvdrental** :

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Per altre informazioni sull'uso dell'utilità pg_dump, vedere gli esempi riportati nell'esercitazione su [pg dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Creare un database vuoto nell'ambiente di destinazione, ovvero il Database di Azure per PostgreSQL.

    Per informazioni dettagliate su come connettersi e creare un database, vedere l'articolo [creare un database di Azure per il server PostgreSQL nel portale di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) o [creare un server di database di Azure per PostgreSQL-iperscalabilità (Citus) nel portale di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Un'istanza di database di Azure per PostgreSQL-iperscala (CITUS) ha solo un database singolo: **CITUS**.

3. Importare lo schema nel database di destinazione che è stato creato mediante il ripristino del file di dump dello schema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Ad esempio:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Per estrarre lo script DROP FOREIGN KEY e aggiungerlo alla destinazione (database di Azure per PostgreSQL), in PgAdmin o in PSQL, eseguire lo script seguente.

   > [!IMPORTANT]
   > Con le chiavi esterne nello schema, il caricamento iniziale e la sincronizzazione continua della migrazione avranno esito negativo.

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
   > I trigger (inserimento o aggiornamento) nei dati applicano l'integrità dei dati nella destinazione prima che i dati vengano replicati dall'origine. Di conseguenza, è consigliabile disabilitare i trigger in tutte le tabelle della destinazione durante **la** migrazione e quindi riabilitare i trigger al termine della migrazione.

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
  
3. Nella schermata **Crea servizio migrazione** specificare un nome, la sottoscrizione, un gruppo di risorse nuovo o esistente e il percorso del servizio.

4. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale fornisce al servizio migrazione del database di Azure l'accesso al server PostgreSQL di origine e l'istanza di database di Azure per PostgreSQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nella portale di Azure, vedere l'articolo [creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

5. Selezione di un piano tariffario.

    Per ulteriori informazioni sui costi e sui piani tariffari, vedere la [pagina](https://aka.ms/dms-pricing)relativa ai prezzi.

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selezionare **Verifica + crea** per creare il servizio.

   La creazione del servizio viene completata entro 10-15 minuti.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Nella schermata **Servizi migrazione del database di Azure** cercare il nome dell'istanza del servizio migrazione del database di Azure creata, selezionare l'istanza e quindi selezionare + **nuovo progetto di migrazione**.

3. Nella schermata **nuovo progetto di migrazione** specificare un nome per il progetto, nella casella di testo **tipo server di origine** Selezionare **PostgreSQL**, nella casella di testo tipo di **server di destinazione** selezionare **database di Azure per PostgreSQL**.
    > [!NOTE]
    > Scegliere **PostgreSQL** nel **tipo di server di origine** anche se il server di origine è un'istanza **di database di Azure per PostgreSQL** .  

4. Nella sezione **scegliere il tipo di attività** selezionare **migrazione dei dati online**.

    ![Creare un progetto di servizio migrazione del database di Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

5. Selezionare **Salva**, prendere nota dei requisiti per usare correttamente il servizio migrazione del database di Azure per eseguire la migrazione dei dati e quindi selezionare **Crea ed Esegui attività**.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Aggiungi Dettagli origine** specificare i dettagli di connessione per l'istanza di PostgreSQL di origine.

    ![Schermata Aggiungi dettagli origine](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Per informazioni dettagliate, ad esempio "nome server", "porta server", "nome database" e così via, vedere il portale **database di Azure per PostgreSQL** .

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione** , specificare i dettagli di connessione per il server CITUS (target iperscale), ovvero l'istanza pre-provisioning di iperscala (CITUS) a cui è stato distribuito lo schema dei **noleggi DVD** con pg_dump.

    ![Schermata Dettagli destinazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > È possibile eseguire la migrazione da un'istanza di database di Azure per PostgreSQL a un'altra istanza di database di Azure per PostgreSQL a server singolo o a un server con iperscalabilità (CITUS).

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Schermata mappa per database di destinazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selezionare **Save (Salva**) e quindi nella schermata **impostazioni di migrazione** accettare i valori predefiniti.

    ![Schermata delle impostazioni di migrazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Schermata di riepilogo della migrazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Viene visualizzata la finestra attività di migrazione e lo **stato** dell'attività deve essere aggiornato in modo da essere visualizzato come **backup in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

     ![Monitorare il processo di migrazione](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Al termine della migrazione, in **nome database**selezionare un database specifico per ottenere lo stato di migrazione per il **caricamento completo dei dati** e le operazioni di **sincronizzazione dati incrementali** .

   > [!NOTE]
   > **Caricamento completo** indica lo stato di migrazione del carico iniziale, mentre **Sincronizzazione dei dati incrementale** indica lo stato di Change Data Capture (CDC).

     ![Dettagli caricamento dati completo](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Dettagli della sincronizzazione dati incrementale](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

2. Attendere che il contatore delle **modifiche in sospeso** mostri **0** per assicurarsi che tutte le transazioni in ingresso nel database di origine siano interrotte, selezionare la casella di controllo **conferma** e quindi fare clic su **applica**.

    ![Schermata completa cutover](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando lo stato della migrazione del database viene visualizzato **completato**, connettere le applicazioni alla nuova istanza di destinazione del database di Azure per PostgreSQL.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti delle migrazioni online verso il Database di Azure per PostgreSQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per PostgreSQL](known-issues-azure-postgresql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni su Database di Azure per PostgreSQL, vedere l'articolo [Che cos'è Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
