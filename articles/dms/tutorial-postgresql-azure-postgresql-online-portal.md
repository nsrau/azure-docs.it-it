---
title: 'Esercitazione: eseguire la migrazione di PostgreSQL in database di Azure per PostgreSQL online tramite il portale di Azure'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione in linea da PostgreSQL locale al database di Azure per PostgreSQL usando il servizio migrazione del database di Azure tramite il portale di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 67eced7f647d50733dc8d273bdd9cd8a31b7b6dc
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255509"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Esercitazione: eseguire la migrazione di PostgreSQL nel database di Azure per PostgreSQL online con DMS tramite il portale di Azure

È possibile usare il servizio migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di PostgreSQL locale al [database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/) con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **DVD Rental** da un'istanza locale di PostgreSQL 9.6 a Database di Azure per PostgreSQL usando l'attività di migrazione online in Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio utilizzando l'utilità pg_dump.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione nel servizio migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Eseguire la migrazione cutover.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Scaricare e installare [PostgreSQL community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 o 10. La versione del server PostgreSQL di origine deve essere 9,4, 9,5, 9,6, 10 o 11. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Inoltre, la versione di PostgreSQL in locale deve corrispondere alla versione del Database di Azure per PostgreSQL. Ad esempio, PostgreSQL 9,6 può eseguire solo la migrazione a database di Azure per PostgreSQL 9,6, 10 o 11, ma non al database di Azure per PostgreSQL 9,5.

* [Creare un server di database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) o [creare un server di database di Azure per PostgreSQL con iperscalabilità (CITUS)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).
* Creare una Rete virtuale di Microsoft Azure per il servizio migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per ulteriori informazioni sulla creazione di una rete virtuale, vedere la [documentazione relativa alla rete virtuale](https://docs.microsoft.com/azure/virtual-network/)e, in particolare, gli articoli introduttivi con informazioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete a Microsoft, aggiungere i seguenti [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio alla subnet in cui verrà eseguito il provisioning del servizio:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Verificare che le regole del gruppo di sicurezza di rete (NSG) per la rete virtuale non blocchino le porte di comunicazione in ingresso seguenti al servizio migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni più dettagliate sul filtro del traffico NSG per la rete virtuale, vedere l'articolo [filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere al server PostgreSQL di origine, per impostazione predefinita attraverso la porta TCP 5432.
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per Database di Azure per PostgreSQL per consentire a Servizio Migrazione del database di Azure di accedere ai database di destinazione. Fornire l'intervallo di subnet della rete virtuale usata per il servizio migrazione del database di Azure.
* Abilitare la replica logica nel file postgresql.config e impostare i parametri seguenti:

  * wal_level = **logical**
  * max_replication_slots = [numero di slot]. È consigliabile impostare **cinque slot**
  * max_wal_senders = [numero di attività simultanee]: il parametro max_wal_senders imposta il numero di attività simultanee che è possibile eseguire, si consiglia di impostare il valore su **10 attività**

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
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
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

    Ad esempio,

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Per estrarre lo script DROP FOREIGN KEY e aggiungerlo alla destinazione (database di Azure per PostgreSQL), in PgAdmin o in PSQL, eseguire lo script seguente.

   > [!IMPORTANT]
   > Con le chiavi esterne nello schema, il caricamento iniziale e la sincronizzazione continua della migrazione avranno esito negativo.

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

5. Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query.

6. Per disabilitare i trigger nel database di destinazione, eseguire lo script seguente.

   > [!IMPORTANT]
   > I trigger (inserimento o aggiornamento) nei dati applicano l'integrità dei dati nella destinazione prima che i dati vengano replicati dall'origine. Di conseguenza, è consigliabile disabilitare i trigger in tutte le tabelle della destinazione durante **la** migrazione e quindi riabilitare i trigger al termine della migrazione.

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Creare un'istanza del servizio Migrazione del database

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Nella schermata **Crea servizio migrazione** specificare un nome, la sottoscrizione, un gruppo di risorse nuovo o esistente e il percorso del servizio.

4. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale fornisce al servizio migrazione del database di Azure l'accesso al server PostgreSQL di origine e l'istanza di database di Azure per PostgreSQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nella portale di Azure, vedere l'articolo [creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

5. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selezionare **Verifica + crea** per creare il servizio.

   La creazione del servizio viene completata entro 10-15 minuti.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Nella schermata **Servizi migrazione del database di Azure** cercare il nome dell'istanza del servizio migrazione del database di Azure creata, selezionare l'istanza e quindi selezionare + **nuovo progetto di migrazione**.

3. Nella schermata **nuovo progetto di migrazione** specificare un nome per il progetto, nella casella di testo **tipo server di origine** Selezionare **PostgreSQL**nella casella di testo **tipo di server di destinazione** selezionare **database di Azure per PostgreSQL**.

4. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

    ![Creare un progetto di servizio migrazione del database di Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

5. Selezionare **Salva**, prendere nota dei requisiti per usare correttamente il servizio migrazione del database di Azure per eseguire la migrazione dei dati e quindi selezionare **Crea ed Esegui attività**.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Aggiungi Dettagli origine** specificare i dettagli di connessione per l'istanza di PostgreSQL di origine.

    ![Schermata Aggiungi dettagli origine](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione** , specificare i dettagli di connessione per il server CITUS (target iperscale), ovvero l'istanza pre-provisioning di iperscala (CITUS) a cui è stato distribuito lo schema dei **noleggi DVD** con pg_dump.

    ![Schermata Dettagli destinazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Schermata mappa per database di destinazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selezionare **Save (Salva**) e quindi nella schermata **impostazioni di migrazione** accettare i valori predefiniti.

    ![Schermata delle impostazioni di migrazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Schermata di riepilogo della migrazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Viene visualizzata la finestra attività di migrazione e lo **stato** dell'attività deve essere aggiornato in modo da essere visualizzato come **backup in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

     ![Monitorare il processo di migrazione](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Al termine della migrazione, in **nome database**selezionare un database specifico per ottenere lo stato di migrazione per il **caricamento completo dei dati** e le operazioni di **sincronizzazione dati incrementali** .

   > [!NOTE]
   > **Caricamento completo** indica lo stato di migrazione del carico iniziale, mentre **Sincronizzazione dei dati incrementale** indica lo stato di Change Data Capture (CDC).

     ![Dettagli caricamento dati completo](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Dettagli della sincronizzazione dati incrementale](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

2. Attendere che il contatore delle **modifiche in sospeso** mostri **0** per assicurarsi che tutte le transazioni in ingresso nel database di origine siano interrotte, selezionare la casella di controllo **conferma** e quindi fare clic su **applica**.

    ![Schermata completa cutover](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando lo stato della migrazione del database viene visualizzato **completato**, connettere le applicazioni alla nuova istanza di destinazione del database di Azure per PostgreSQL.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti delle migrazioni online verso il Database di Azure per PostgreSQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per PostgreSQL](known-issues-azure-postgresql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni su Database di Azure per PostgreSQL, vedere l'articolo [Che cos'è Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
