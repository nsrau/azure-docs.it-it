---
title: Configurare la replica geografica per il database SQL di Azure con Transact-SQL | Microsoft Docs
description: Configurare la replica geografica per il database SQL di Azure con Transact-SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 702a01f7ea56e8af6286149bcb42590294cb618b


---
# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurare la replica geografica per il database SQL di Azure con Transact-SQL
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-geo-replication-overview.md)
> * [Portale di Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

Questo articolo illustra come configurare la replica geografica attiva per un database SQL di Azure con Transact-SQL.

Per avviare il failover usando Transact-SQL, vedere [Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> La replica geografica attiva (database secondari accessibili in lettura) è ora disponibile per tutti i database in tutti i livelli di servizio. Nell'aprile 2017 il tipo di database secondario non leggibile verrà ritirato e i database non leggibili esistenti verranno aggiornati automaticamente a database secondari accessibili in lettura.
> 
> 

Per configurare la replica geografica attiva con Transact-SQL, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Un server di database SQL di Azure logico <MyLocalServer> e un database SQL <MyDB>: il database primario che si vuole replicare.
* Uno o più server di database SQL di Azure logici <MySecondaryServer(n)>: i server logici saranno i server partner in cui verranno creati i database secondari.
* Un account di accesso con il ruolo DBManager nel database primario e con db_ownership del database locale per il quale verrà eseguita la replica geografica, oltre al ruolo DBManager nei server partner in cui si configurerà la replica geografica.
* SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Aggiungere un database secondario
È possibile usare l'istruzione **ALTER DATABASE** per creare un database secondario in un server partner. Eseguire questa istruzione sul database master del server che contiene il database da replicare. Il database con replica geografica, ovvero il "database primario", avrà lo stesso nome del database che viene replicato e, per impostazione predefinita, lo stesso livello di servizio del database primario. Il database secondario può essere accessibile o non accessibile in lettura e può essere un database singolo o un database elastico. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).
Dopo la creazione e il seeding del database secondario, inizierà la replica asincrona dei dati dal database primario. I passaggi seguenti descrivono come configurare la replica geografica tramite Management Studio. Vengono descritti i passaggi per creare database secondari accessibili e non accessibili in lettura con un database singolo o un database elastico.

> [!NOTE]
> Se nel server partner specificato è presente un database con lo stesso nome del database primario, il comando avrà esito negativo.
> 
> 

### <a name="add-non-readable-secondary-single-database"></a>Aggiungere un database secondario non accessibile in lettura (database singolo)
Usare la procedura seguente per creare un database secondario non accessibile in lettura come database singolo.

1. Usare la versione di SQL Server Management Studio 13.0.600.65 o successiva.
   
   > [!IMPORTANT]
   > Scaricare la versione [più recente](https://msdn.microsoft.com/library/mt238290.aspx) di SQL Server Management Studio. È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti del portale di Azure.
   > 
   > 
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario non leggibile in MySecondaryServer1 dove MySecondaryServer1 è il nome descrittivo del server.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);
4. Fare clic su **Execute** per eseguire la query.

### <a name="add-readable-secondary-single-database"></a>Aggiungere un database secondario accessibile in lettura (database singolo)
Usare la procedura seguente per creare un database secondario accessibile in lettura come database singolo.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario accessibile in lettura in un server secondario.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Fare clic su **Execute** per eseguire la query.

### <a name="add-non-readable-secondary-elastic-database"></a>Aggiungere un database secondario non accessibile in lettura (database elastico)
Usare la procedura seguente per creare un database secondario non accessibile in lettura come database elastico.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario non accessibile in lettura in un pool elastico.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));
4. Fare clic su **Execute** per eseguire la query.

### <a name="add-readable-secondary-elastic-database"></a>Aggiungere un database secondario accessibile in lettura (database elastico)
Usare la procedura seguente per creare un database secondario accessibile in lettura come database elastico.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario accessibile in lettura in un pool elastico.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. Fare clic su **Execute** per eseguire la query.

## <a name="remove-secondary-database"></a>Rimuovere un database secondario
È possibile usare l'istruzione **ALTER DATABASE** per terminare definitivamente la relazione di replica tra un database secondario e il relativo database primario. L'istruzione viene eseguita sul database master in cui risiede il database primario. Dopo la terminazione della relazione, il database secondario diventa un normale database accessibile in lettura e scrittura. Se la connettività al database secondario viene interrotta, il comando riesce ma il database diventerà di nuovo accessibile in lettura e scrittura al ripristino della connettività. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).

Usare la procedura seguente per rimuovere il database secondario con replica geografica da una relazione di replica geografica.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per rimuovere un database secondario con replica geografica.
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. Fare clic su **Execute** per eseguire la query.

## <a name="monitor-geo-replication-configuration-and-health"></a>Monitorare la configurazione della replica geografica e l'integrità
Le attività di monitoraggio includono il controllo della configurazione della replica geografica e dell'integrità della replica dei dati.  È possibile usare la vista a gestione dinamica (DMV) **sys.dm_geo_replication_links** in un database master per restituire informazioni su tutti i collegamenti di replica esistenti per ogni database nel server logico di database SQL di Azure. Questa vista include una riga per ogni collegamento di replica tra i database primari e secondari. È possibile usare la DMV **sys.dm_replication_link_status** per restituire una riga per ogni database SQL di Azure attualmente interessato da un collegamento di replica. Sono inclusi il database primario e i database secondari. Se esiste più di un collegamento di replica continua per un determinato database primario, questa tabella contiene una riga per ogni relazione. La vista viene creata in tutti i database, incluso il database master logico. Eseguendo query su questa vista in tale database, verrà tuttavia restituito un set vuoto. È possibile usare la DMV **sys.dm_operation_status** per visualizzare lo stato di tutte le operazioni di database, incluso lo stato dei collegamenti di replica. Per altre informazioni, vedere [sys.geo_replication_links (database SQL di Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (database SQL di Azure)](https://msdn.microsoft.com/library/mt575504.aspx) e [sys.dm_operation_status (database SQL di Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Usare la procedura seguente per monitorare una relazione di replica geografica.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione seguente per visualizzare tutti i database con collegamenti di replica geografica.
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;
4. Fare clic su **Execute** per eseguire la query.
5. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **MyDB** e quindi scegliere **Nuova query**.
6. Usare l'istruzione seguente per visualizzare gli intervalli di replica e l'ultima ora di replica dei database secondari di MyDB.
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. Fare clic su **Execute** per eseguire la query.
8. Usare l'istruzione seguente per visualizzare le operazioni di replica geografica più recenti associate al database MyDB.
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. Fare clic su **Execute** per eseguire la query.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Aggiornare un database secondario da non leggibile a leggibile
Nell'aprile 2017 il tipo di database secondario non leggibile verrà ritirato e i database non leggibili esistenti verranno aggiornati automaticamente a database secondari accessibili in lettura. Se al momento si utilizzano database secondari non leggibili e si desidera aggiornarli per renderli leggibili, per ogni database secondario è possibile eseguire la semplice procedura descritta di seguito.

> [!IMPORTANT]
> Non esiste alcun metodo self-service per aggiornare sul posto un database secondario da non leggibile a leggibile. Se si elimina l'unico database secondario, il database primario non sarà più protetto fino alla completa sincronizzazione del nuovo database secondario. Se il contratto di servizio dell'applicazione richiede che il database primario sia sempre protetto, prima di applicare le operazioni di aggiornamento descritte in precedenza è consigliabile creare un database secondario parallelo in un altro server. Tenere presente che ogni database primario può avere fino a 4 database secondari.
> 
> 

1. Per prima cosa, eseguire la connessione al server *secondario* ed eliminare il database secondario non leggibile:  
   
        DROP DATABASE <MyNonReadableSecondaryDB>;
2. Dopodiché, stabilire la connessione al server *primario* e aggiungere un nuovo database secondario leggibile.
   
        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)




<!--HONumber=Nov16_HO3-->


