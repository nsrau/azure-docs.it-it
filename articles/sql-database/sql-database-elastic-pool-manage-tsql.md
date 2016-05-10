<properties 
    pageTitle="Creare o spostare un database SQL di Azure in un pool elastico tramite T-SQL | Microsoft Azure" 
    description="Usare T-SQL per creare un database SQL di Azure in un pool elastico. In alternativa, usare T-SQL per spostare il database all'interno e all'esterno di pool." 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/28/2016"
    ms.author="sidneyh"/>

# Monitorare e gestire un pool di database elastici con Transact-SQL  

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Usare i comandi [Create Database (database SQL di Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Alter Database (database SQL di Azure)](https://msdn.microsoft.com/library/mt574871.aspx) per creare e spostare i database all'interno e all'esterno di pool elastici. Il pool elastico deve essere già disponibile per poter usare questi comandi, che hanno effetto solo sui database. La creazione di nuovi pool e l'impostazione delle proprietà dei pool, ad esempio eDTU min/max, non possono essere modificate con i comandi T-SQL.


> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-portal.md) in un unico passaggio.


## Creare un nuovo database in un pool elastico
Usare il comando CREATE DATABASE con l'opzione SERVICE\_OBJECTIVE.

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

Tutti i database in un pool elastico ereditano il livello di servizio del pool elastico (Basic, Standard o Premium).


## Spostare un database tra pool elastici
Usare il comando ALTER DATABASE con MODIFY e impostare l'opzione SERVICE\_OBJECTIVE come ELASTIC\_POOL. Come nome, impostare il nome del pool di destinazione.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  

## Spostare un database in un pool elastico 
Usare il comando ALTER DATABASE con MODIFY e impostare l'opzione SERVICE\_OBJECTIVE come ELASTIC\_POOL. Come nome, impostare il nome del pool di destinazione.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## Spostare un database da un pool elastico
Usare il comando ALTER DATABASE e impostare SERVICE\_OBJECTIVE su uno dei livelli di prestazioni, ovvero S0, S1 e così via.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## Elencare i database in un pool elastico
Usare la [vista sys.database\_service\_objectives](https://msdn.microsoft.com/library/mt712619) per elencare tutti i database in un pool elastico. Accedere al database master per eseguire query sulla vista.

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## Ottenere dati di utilizzo delle risorse per un pool

Usare la [vista sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx) per esaminare le statistiche di utilizzo delle risorse di un pool elastico in un server logico. Accedere al database master per eseguire query sulla vista.

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## Ottenere l'utilizzo delle risorse per un database elastico

Usare la [vista sys.dm\_ db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) o la [vista sys.resource \_stats](https://msdn.microsoft.com/library/dn269979.aspx) per esaminare le statistiche di utilizzo delle risorse di un database in un pool elastico. Questo processo è simile all'esecuzione di query sull'utilizzo delle risorse per un database singolo.

## Passaggi successivi

Dopo aver creato un pool di database elastici, è possibile gestire i database elastici nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script T-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

Vedere [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze, spostare dati, eseguire query o creare transazioni.

<!---HONumber=AcomDC_0504_2016-->