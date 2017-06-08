---
title: 'T-SQL: gestire un pool elastico di database SQL di Azure | Microsoft Docs'
description: Usare T-SQL per gestire un pool elastico di database SQL di Azure.
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a3acd5f4ec63061254b550737ae9fb4d39b343c6
ms.contentlocale: it-it
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Monitorare e gestire un pool elastico con Transact-SQL
Questo argomento illustra come gestire [pool elastici](sql-database-elastic-pool.md) scalabili con Transact-SQL.  È anche possibile creare e gestire un pool elastico di Azure con il [portale di Azure](https://portal.azure.com/), [PowerShell](sql-database-elastic-pool-manage-powershell.md), l'API REST o [C#](sql-database-elastic-pool-manage-csharp.md). È anche possibile creare e spostare database verso e dai pool elastici usando [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).


Usare i comandi [Create Database (database SQL di Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Alter Database (database SQL di Azure)](https://msdn.microsoft.com/library/mt574871.aspx) per creare e spostare i database all'interno e all'esterno di pool elastici. Il pool elastico deve essere già disponibile per poter usare questi comandi, che hanno effetto solo sui database. La creazione di nuovi pool e l'impostazione delle proprietà dei pool, ad esempio eDTU min/max, non possono essere modificate con i comandi T-SQL.

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Creare un nuovo database in pool in un pool elastico
Usare il comando CREATE DATABASE con l'opzione SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in an elastic named S3M100.

Tutti i database in un pool elastico ereditano il livello di servizio del pool elastico (Basic, Standard o Premium). 

## <a name="move-a-database-between-elastic-pools"></a>Spostare un database tra pool elastici
Usare il comando ALTER DATABASE con MODIFY e impostare l'opzione SERVICE\_OBJECTIVE come ELASTIC\_POOL. Come nome, impostare il nome del pool di destinazione.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to an elastic named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool elastico
Usare il comando ALTER DATABASE con MODIFY e impostare l'opzione SERVICE\_OBJECTIVE come ELASTIC_POOL. Come nome, impostare il nome del pool di destinazione.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to an elastic named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Spostare un database da un pool elastico
Usare il comando ALTER DATABASE e impostare SERVICE_OBJECTIVE su uno dei livelli di prestazioni, ad esempio S0 o S1.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Elencare i database in un pool elastico
Usare la [vista sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) per elencare tutti i database in un pool elastico. Accedere al database master per eseguire query sulla vista.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Ottenere i dati di utilizzo delle risorse per un pool elastico
Usare la [vista sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx) per esaminare le statistiche di utilizzo delle risorse di un pool elastico in un server logico. Accedere al database master per eseguire query sulla vista.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-a-pooled-database"></a>Ottenere l'utilizzo delle risorse per un database in pool
Usare la [vista sys.dm\_db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) o la [vista sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) per esaminare le statistiche di utilizzo delle risorse di un database in un pool elastico. Questo processo è simile all'esecuzione di query sull'utilizzo delle risorse per un database singolo.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un pool elastico, è possibile gestire i database elastici nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script T-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md). 

Vedere [Aumentare il numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze, spostare dati, eseguire query o creare transazioni.


