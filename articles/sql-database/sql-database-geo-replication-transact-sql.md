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
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: dad35a2b3beb2b07d5b12afb8a04ba48f8b8ef7e
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurare la replica geografica attiva per il database SQL di Azure con Transact-SQL

Questo articolo illustra come configurare la replica geografica attiva per un database SQL di Azure con Transact-SQL.

Per avviare il failover usando Transact-SQL, vedere [Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> Quando si usa la replica geografica attiva (database secondari leggibili) per il ripristino di emergenza è necessario configurare un gruppo di failover per tutti i database all'interno di un'applicazione per consentire il failover automatico e trasparente. Questa funzionalità è in anteprima. Per altre informazioni vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).
> 
> 

Per configurare la replica geografica attiva con Transact-SQL, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Un server di database SQL di Azure logico <MyLocalServer> e un database SQL <MyDB>: il database primario che si vuole replicare
* Uno o più server di database SQL di Azure logici <MySecondaryServer(n)>: i server logici saranno i server partner in cui verranno creati i database secondari
* Un account di accesso DBManager nel server primario
* Avere db_ownership del database locale di cui eseguire la replica geografica
* Essere DBManager nel server partner verso cui si configurerà la replica geografica
* La versione più recente di SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Aggiungere un database secondario
È possibile usare l'istruzione **ALTER DATABASE** per creare un database secondario in un server partner. Eseguire questa istruzione sul database master del server che contiene il database da replicare. Il database con replica geografica, ovvero il "database primario", avrà lo stesso nome del database che viene replicato e, per impostazione predefinita, lo stesso livello di servizio del database primario. Il database secondario può essere leggibile o non leggibile e può essere un database singolo o in un pool elastico. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).
Dopo la creazione e il seeding del database secondario, inizierà la replica asincrona dei dati dal database primario. I passaggi seguenti descrivono come configurare la replica geografica tramite Management Studio. Vengono descritte le procedure per creare database secondari leggibili o non leggibili, come database singoli o in un pool elastico.

> [!NOTE]
> Se nel server partner specificato è presente un database con lo stesso nome del database primario, il comando avrà esito negativo.
> 

### <a name="add-readable-secondary-single-database"></a>Aggiungere un database secondario accessibile in lettura (database singolo)
Usare la procedura seguente per creare un database secondario accessibile in lettura come database singolo.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario accessibile in lettura in un server secondario.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Fare clic su **Execute** per eseguire la query.

### <a name="add-readable-secondary-elastic-pool"></a>Aggiungere un database secondario leggibile (pool elastico)
Usare la procedura seguente per creare un database secondario leggibile in un pool elastico.

1. In Management Studio connettersi al server logico di database SQL di Azure.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Usare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in un database primario con replica geografica con un database secondario accessibile in lettura in un server secondario in un pool elastico.
   
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

## <a name="monitor-active-geo-replication-configuration-and-health"></a>Monitorare l'integrità e la configurazione della replica geografica attiva

Le attività di monitoraggio includono il controllo della configurazione della replica geografica e dell'integrità della replica dei dati.  È possibile usare la vista a gestione dinamica (DMV) **sys.dm_geo_replication_links** in un database master per restituire informazioni su tutti i collegamenti di replica esistenti per ogni database nel server logico di database SQL di Azure. Questa vista include una riga per ogni collegamento di replica tra i database primari e secondari. È possibile usare la DMV **sys.dm_replication_link_status** per restituire una riga per ogni database SQL di Azure attualmente interessato da un collegamento di replica. Sono inclusi il database primario e i database secondari. Se esiste più di un collegamento di replica continua per un determinato database primario, questa tabella contiene una riga per ogni relazione. La vista viene creata in tutti i database, incluso il database master logico. Eseguendo query su questa vista in tale database, verrà tuttavia restituito un set vuoto. È possibile usare la DMV **sys.dm_operation_status** per visualizzare lo stato di tutte le operazioni di database, incluso lo stato dei collegamenti di replica. Per altre informazioni, vedere [sys.geo_replication_links (database SQL di Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (database SQL di Azure)](https://msdn.microsoft.com/library/mt575504.aspx) e [sys.dm_operation_status (database SQL di Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Usare la procedura seguente per monitorare una relazione di replica geografica attiva.

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

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)


