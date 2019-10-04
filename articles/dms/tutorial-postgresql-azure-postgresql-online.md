---
title: 'Esercitazione: Usare il Servizio Migrazione del database di Azure per eseguire la migrazione online di PostgreSQL in Database di Azure per PostgreSQL | Microsoft Docs'
description: Informazioni su come eseguire la migrazione online da PostgreSQL locale in Database di Azure per PostgreSQL con Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/06/2019
ms.openlocfilehash: 5888555e93c28c96445bed1936deda022b0a4b94
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734596"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Esercitazione: Eseguire la migrazione di PostgreSQL in Database di Azure per PostgreSQL online mediante il Servizio Migrazione del database di Azure

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di PostgreSQL locale a [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/) con tempi di inattività minimi. In altre parole, la migrazione può essere eseguita con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **DVD Rental** da un'istanza locale di PostgreSQL 9.6 a Database di Azure per PostgreSQL usando l'attività di migrazione online in Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio con l'utilità pg_dump.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Scaricare e installare [PostgreSQL community edition](https://www.postgresql.org/download/) 9.5, 9.6 o 10. La versione del server PostgreSQL di origine deve essere 9.5.11, 9.6.7, 10 o successiva. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Inoltre, la versione di PostgreSQL in locale deve corrispondere alla versione del Database di Azure per PostgreSQL. Ad esempio, per PostgreSQL 9.5.11.5 è possibile solo eseguire la migrazione al Database di Azure per PostgreSQL 9.5.11 e non alla versione 9.6.7.

* [Creare un'istanza nel Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* Creare una rete virtuale di Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni sulla creazione di una rete virtuale, vedere [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché Servizio Migrazione del database di Azure non dispone di connettività Internet.

* Verificare che le regole del gruppo di sicurezza di rete (NSG) per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere al server PostgreSQL di origine, per impostazione predefinita attraverso la porta TCP 5432.
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per Database di Azure per PostgreSQL per consentire a Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
* Esistono due metodi per richiamare l'interfaccia della riga di comando:

  * Nel menu nell'angolo superiore destro del portale di Azure selezionare il pulsante Cloud Shell:

       ![Pulsante Cloud Shell nel portale di Azure](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Installare ed eseguire l'interfaccia della riga di comando. L'interfaccia della riga di comando 2.0 è uno strumento da riga di comando per la gestione delle risorse di Azure.

       Per scaricare l'interfaccia della riga di comando, seguire le istruzioni riportate nell'articolo [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). L'articolo elenca anche le piattaforme che supportano l'interfaccia della riga di comando 2.0.

       Per configurare il sottosistema Windows per Linux (WSL), seguire le istruzioni riportate nella [Guida all'installazione di Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)

* Abilitare la replica logica nel file postgresql.config e impostare i parametri seguenti:

  * wal_level = **logical**
  * max_replication_slots = [numero di slot]. È consigliabile impostare **cinque slot**
  * max_wal_senders = [numero di attività simultanee]: il parametro max_wal_senders imposta il numero di attività simultanee che è possibile eseguire, si consiglia di impostare il valore su **10 attività**

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Per completare tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database.

1. Usare il comando pg_dump -s per creare un file di dump dello schema per un database. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ad esempio, per eseguire il dump di un file di schema per il database dvdrental:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Per altre informazioni sull'uso dell'utilità pg_dump, vedere gli esempi riportati nell'esercitazione su [pg dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Creare un database vuoto nell'ambiente di destinazione, ovvero il Database di Azure per PostgreSQL.

    Per informazioni dettagliate su come connettersi e creare un database, vedere l'articolo [Creare un database di Azure per il server PostgreSQL nel portale di Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).

3. Importare lo schema nel database di destinazione che è stato creato mediante il ripristino del file di dump dello schema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Ad esempio:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se nello schema sono presenti chiavi esterne, il caricamento iniziale e la sincronizzazione continua della migrazione avrà esito negativo. Eseguire lo script seguente in PgAdmin o psql per estrarre lo script di eliminazione della chiave esterna e aggiungere lo script della chiave esterna nella destinazione (Database di Azure per PostgreSQL).
  
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

    Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query.

5. I trigger di inserimento o di aggiornamento presenti nei dati applicheranno l'integrità dei dati nella destinazione prima dei dati replicati dall'origine. È consigliabile disabilitare i trigger in tutte le tabelle **nella destinazione** durante la migrazione e quindi abilitare di nuovo i trigger al termine della migrazione.

    Per disabilitare i trigger nel database di destinazione, usare il comando seguente:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se sono presenti dati di tipo ENUM nelle tabelle, è consigliabile aggiornarli temporaneamente a un tipo di dati character "variante" nella tabella di destinazione. Dopo che la replica dei dati è stata completata, ripristinare il tipo di dati ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Provisioning di un'istanza del Servizio Migrazione del database mediante l'interfaccia della riga di comando

1. Installare l'estensione di sincronizzazione del Servizio Migrazione del database:
   * Accedere ad Azure mediante il comando seguente:
       ```
       az login
       ```

   * Quando richiesto, aprire un Web browser e immettere un codice per autenticare il dispositivo. Seguire le istruzioni indicate.
   * Aggiungere l'estensione Servizio Migrazione del database:
       * Per elencare le estensioni disponibili, eseguire il comando seguente:

           ```
           az extension list-available –otable
           ```

       * Per installare l'estensione, eseguire il comando seguente:

           ```
           az extension add –n dms-preview
           ```

   * Per verificare di avere installato l'estensione Servizio Migrazione del database corretta, eseguire il comando seguente:

       ```
       az extension list -otable
       ```
       Dovrebbe venire visualizzato l'output seguente.

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * È possibile visualizzare in qualsiasi momento tutti i comandi supportati nel Servizio Migrazione del database eseguendo:

       ```
       az dms -h
       ```

   * Se si hanno più sottoscrizioni di Azure, eseguire questo comando per impostare la sottoscrizione che si vuole usare per eseguire il provisioning di un'istanza del Servizio Migrazione del database.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Eseguire il provisioning di un'istanza del Servizio Migrazione del database eseguendo il comando seguente:

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Ad esempio il comando seguente creerà un servizio in:

   * Posizione: Stati Uniti Orientali 2
   * Sottoscrizione: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Nome gruppo di risorse: PostgresDemo
   * Nome servizio Migrazione del database: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   Sono necessari circa 10-12 minuti per creare l'istanza del Servizio Migrazione del database.

3. Per identificare l'indirizzo IP dell'agente Servizio Migrazione del database in modo che sia possibile aggiungerlo al file Postgres pg_hba.conf, eseguire il comando seguente:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Ad esempio:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Si ottiene un risultato simile all'indirizzo seguente: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Aggiungere l'indirizzo IP dell'agente Servizio Migrazione del database al file Postgres pg_hba.conf.

    * Prendere nota dell'indirizzo IP del Servizio Migrazione del database dopo aver completato il provisioning nel Servizio Migrazione del database.
    * Aggiungere l'indirizzo IP al file pg_hba.conf nell'origine; la voce è simile alla seguente:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Quindi creare un progetto di migrazione PostgreSQL eseguendo il comando seguente:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Ad esempio, il comando seguente crea un progetto usando questi parametri:

   * Posizione: Stati Uniti centro-occidentali
   * Nome gruppo di risorse: PostgresDemo
   * Nome servizio: PostgresCLI
   * Nome progetto: PGMigration
   * Piattaforma di origine: PostgreSQL
   * Piattaforma di destinazione: AzureDbForPostgreSql

     ```
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Creare un'attività di migrazione di PostgreSQL usando la procedura seguente.

    Questo passaggio include l'uso dell'indirizzo IP, l'ID utente e la password di origine nonché l'IP, l'ID utente, la password e il tipo di attività di destinazione per stabilire la connessione.

   * Per visualizzare l'elenco completo delle opzioni eseguire il comando seguente:

       ```
       az dms project task create -h
       ```

       Sia per la connessione di origine che per quella di destinazione il parametro di input fa riferimento a un file json che contiene l'elenco di oggetti.

       Il formato dell'oggetto connessione JSON per le connessioni PostgreSQL.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * È disponibile anche un file json di opzioni di database con l'elenco degli oggetti JSON. Il formato dell'oggetto JSON delle opzioni di database per PostgreSQL è illustrato di seguito:

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Creare un file json con il Blocco note, copiare i comandi seguenti e incollarli nel file, quindi salvare il file in C:\DMS\source.json.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Creare un altro file denominato target.json e salvarlo come C:\DMS\target.json. Includere i comandi seguenti:

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Creare un file json di opzioni di database che elenca inventory come database di cui eseguire la migrazione:

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Eseguire il comando seguente, che usa il file di origine, quello di destinazione e il file json di opzioni.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     L'attività di migrazione è stata inviata correttamente.

7. Per visualizzare lo stato di avanzamento dell'attività, eseguire il comando seguente:

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   Oppure

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. È anche possibile eseguire una query per migrationState da expand output:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Informazioni sullo stato dell'attività di migrazione

Nel file di output ci sono diversi parametri che indicano lo stato di avanzamento della migrazione. Ad esempio vedere il file di output seguente:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Attività di migrazione cutover

Quando il caricamento completo termina, il database è pronto per il cutover. A seconda di quanto il server di origine è occupato con le nuove transazioni in ingresso, l'attività Servizio Migrazione del database potrebbe comunque applicare modifiche dopo il termine del caricamento completo.

Per assicurarsi che tutti i dati siano aggiornati, verificare i conteggi delle righe tra i database di origine e di destinazione. Ad esempio, è possibile usare il comando seguente:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. Eseguire l'attività di migrazione del database cutover usando il comando seguente:

    ```
    az dms project task cutover -h
    ```

    Ad esempio:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. Per monitorare l'avanzamento del cutover, eseguire il comando seguente:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Pulizia di servizi, progetti e attività

Se è necessario annullare o eliminare qualsiasi attività, progetto o servizio del Servizio Migrazione del database, eseguire l'annullamento nella sequenza seguente:

* Annullare qualsiasi attività in esecuzione
* Eliminare l'attività
* Eliminare il progetto
* Eliminare il Servizio Migrazione del database

1. Per annullare un'attività in esecuzione, usare il comando seguente:

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Per eliminare un'attività in esecuzione, usare il comando seguente:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Per annullare un progetto in esecuzione, usare il comando seguente:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Per eliminare un progetto in esecuzione, usare il comando seguente:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Per eliminare il Servizio Migrazione del database di Azure, usare il comando seguente:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti delle migrazioni online verso il Database di Azure per PostgreSQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per PostgreSQL](known-issues-azure-postgresql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni sul Database di Azure per PostgreSQL, vedere l'articolo [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
