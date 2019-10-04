---
title: Creare e gestire processi di database elastico di Azure SQL tramite Transact-SQL (T-SQL) | Microsoft Docs
description: Eseguire script in più database con l'agente di processo di database elastico tramite Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d1123affa79f401b5142af604adbd757bdfb7d73
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641050"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Usare Transact-SQL (T-SQL) per creare e gestire processi di database elastico

Questo articolo fornisce numerosi scenari di esempio per iniziare a usare i processi elastici tramite T-SQL.

Gli esempi usano le [stored procedure](#job-stored-procedures) e le [viste](#job-views) disponibili nel [*database dei processi*](sql-database-job-automation-overview.md#job-database).

Transact-SQL (T-SQL) viene usato per creare, configurare, eseguire e gestire i processi. La creazione dell'agente di processo elastico non è supportata in T-SQL, pertanto è innanzitutto necessario creare un *agente di processo elastico* usando il portale o [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Creare le credenziali per l'esecuzione del processo

Le credenziali vengono usate per connettersi ai database di destinazione per l'esecuzione degli script. Per la corretta esecuzione dello script, le credenziali devono disporre delle autorizzazioni appropriate per i database specificati dal gruppo di destinazione. Quando si usa un server e/o un membro del gruppo di destinazione del pool, è consigliabile creare credenziali master da usare per aggiornare le credenziali prima dell'espansione del server e/o del pool al momento dell'esecuzione del processo. Le credenziali con ambito database vengono create nel database dell'agente processo. È necessario usare le stesse credenziali per *creare un account di accesso* e *creare un utente dall'account di accesso per concedere le autorizzazioni per il database dell'account di accesso* nei database di destinazione.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Creare un gruppo di destinazione (server)

L'esempio seguente mostra come eseguire un processo in tutti i database in un server.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Escludere un singolo database

L'esempio seguente mostra come eseguire un processo in tutti i database in un server di database SQL, ad eccezione del database denominato *MappingDB*.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Creare un gruppo di destinazione (pool)

L'esempio seguente mostra come specificare come destinazione tutti i database in uno o più pool elastici.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Distribuire un nuovo schema a più database

L'esempio seguente mostra come distribuire un nuovo schema a tutti i database.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Raccolta dati con i parametri predefiniti

In molti scenari di raccolta dati può essere utile includere alcune di queste variabili di scripting per facilitare la post-elaborazione dei risultati del processo.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Ad esempio, per raggruppare tutti i risultati relativi alla stessa esecuzione del processo, usare *$(job_execution_id)* come illustrato nel comando seguente:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorare le prestazioni del database

Nell'esempio seguente viene creato un nuovo processo per raccogliere i dati sulle prestazioni da più database.

Per impostazione predefinita l'agente processo cercherà di creare la tabella in cui archiviare i risultati. Di conseguenza l'accesso associato alle credenziali usate per le credenziali di output dovrà disporre delle autorizzazioni sufficienti per eseguire questa operazione. Se si vuole creare manualmente la tabella in anticipo, è necessario che disponga delle proprietà seguenti:
1. Colonne con il nome e tipi di dati corretti per il set di risultati.
2. Colonna aggiuntiva per internal_execution_id con il tipo di dati uniqueidentifier.
3. Indice non cluster denominato `IX_<TableName>_Internal_Execution_ID` nella colonna internal_execution_id.

Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire i comandi seguenti:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Visualizzare le definizioni dei processi

L'esempio seguente mostra come visualizzare le definizioni del processo corrente.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>Avviare l'esecuzione ad hoc di un processo

L'esempio seguente mostra come avviare immediatamente un processo.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Pianificare l'esecuzione di un processo

L'esempio seguente mostra come pianificare un processo per l'esecuzione futura.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorare lo stato di esecuzione di un processo

L'esempio seguente mostra come visualizzare i dettagli dello stato di esecuzione per tutti i processi.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Annullare un processo

L'esempio seguente mostra come annullare un processo.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Eliminare la cronologia dei processi meno recente

L'esempio seguente mostra come eliminare la cronologia dei processi precedente a una data specifica.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Eliminare un processo e tutta la relativa cronologia

L'esempio seguente mostra come eliminare un processo e tutta la cronologia correlata.  
Connettersi al [*database dei processi*](sql-database-job-automation-overview.md#job-database) ed eseguire il comando seguente:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Stored procedure per i processi

Le stored procedure seguenti sono disponibili nel [database dei processi](sql-database-job-automation-overview.md#job-database).



|Stored procedure  |Descrizione  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Aggiunge un nuovo processo.    |
|[sp_update_job](#sp_update_job)    |      Aggiorna un processo esistente.   |
|[sp_delete_job](#sp_delete_job)     |      Elimina il processo esistente.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Aggiunge un passaggio a un processo.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aggiorna un passaggio di un processo.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Elimina un passaggio di un processo.    |
|[sp_start_job](#sp_start_job)    |  Avvia l'esecuzione di un processo.       |
|[sp_stop_job](#sp_stop_job)     |     Arresta l'esecuzione di un processo.   |
|[sp_add_target_group](#sp_add_target_group)    |     Aggiunge un gruppo di destinazione.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Elimina un gruppo di destinazione.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Aggiunge un database o un gruppo di database a un gruppo di destinazione.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Rimuove un membro del gruppo di destinazione da un gruppo di destinazione.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Rimuove i record della cronologia per un processo.     |





### <a name="sp_add_job"></a>sp_add_job

Aggiunge un nuovo processo. 
  
#### <a name="syntax"></a>Sintassi  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argomenti  

**[\@job_name =** ]' job_name '  
Nome del processo. Il nome deve essere univoco e non può contenere il carattere di percentuale (%). job_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@Description =** ]' Description '  
Descrizione del processo. description è di tipo nvarchar(512) e il valore predefinito è NULL. Se viene omessa la descrizione, viene usata una stringa vuota.

**[\@Enabled =** ] abilitato  
Specifica se la pianificazione del processo è abilitata. enabled è di tipo bit e il valore predefinito è 0 (disabilitato). Se il valore è 0, il processo non è abilitato e non viene eseguito in base alla relativa pianificazione, tuttavia è possibile eseguirlo manualmente. Se il valore è 1, il processo viene eseguito in base alla relativa pianificazione e può anche essere eseguito manualmente.

**[\@schedule_interval_type =** ] schedule_interval_type  
Valore che indica quando deve essere eseguito il processo. schedule_interval_type è di tipo nvarchar(50), con un valore predefinito di Once, e può avere uno dei valori seguenti:
- "Once",
- "Minutes",
- "Hours",
- "Days",
- "Weeks",
- "Months"

**[\@schedule_interval_count =** ] schedule_interval_count  
Numero di periodi schedule_interval_count tra ogni esecuzione del processo. schedule_interval_count è di tipo int e il valore predefinito è 1. Il valore deve essere maggiore o uguale a 1.

**[\@schedule_start_time =** ] schedule_start_time  
Data dalla quale è possibile avviare l'esecuzione del processo. schedule_start_time è di tipo DATETIME2 e il valore predefinito è 00.00.00.0000000 0001-01-01.

**[\@schedule_end_time =** ] schedule_end_time  
Data in cui è possibile arrestare l'esecuzione del processo. schedule_end_time è di tipo DATETIME2 e il valore predefinito è 9999-12-31 11:59:59.0000000. 

**[\@job_id =** ] job_id output  
Numero di identificazione del processo assegnato al processo se viene creato correttamente. job_id è una variabile di output di tipo uniqueidentifier.

#### <a name="return-code-values"></a>Valori del codice restituito

0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
sp_add_job deve essere eseguito dal database dell'agente processo specificato al momento della creazione dell'agente processo.
Dopo l'esecuzione di sp_add_job per aggiungere un processo, è possibile usare sp_add_jobstep per aggiungere i passaggi che eseguono le attività per il processo. Il numero di versione iniziale del processo è 0, che verrà incrementato a 1 quando si aggiunge il primo passaggio.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:

- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

### <a name="sp_update_job"></a>sp_update_job

Aggiorna un processo esistente.

#### <a name="syntax"></a>Sintassi

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argomenti
**[\@job_name =** ]' job_name '  
Nome del processo da aggiornare. job_name è di tipo nvarchar(128).

**[\@new_name =** ]' new_name '  
Nuovo nome del processo. new_name è di tipo nvarchar(128).

**[\@Description =** ]' Description '  
Descrizione del processo. description è di tipo nvarchar(512).

**[\@Enabled =** ] abilitato  
Specifica se la pianificazione del processo è abilitata (1) o disabilitata (0). enabled è di tipo bit.

**[\@schedule_interval_type =** ] schedule_interval_type  
Valore che indica quando deve essere eseguito il processo. schedule_interval_type è di tipo nvarchar(50) e può avere uno dei valori seguenti:

- "Once",
- "Minutes",
- "Hours",
- "Days",
- "Weeks",
- "Months"

**[\@schedule_interval_count =** ] schedule_interval_count  
Numero di periodi schedule_interval_count tra ogni esecuzione del processo. schedule_interval_count è di tipo int e il valore predefinito è 1. Il valore deve essere maggiore o uguale a 1.

**[\@schedule_start_time =** ] schedule_start_time  
Data dalla quale è possibile avviare l'esecuzione del processo. schedule_start_time è di tipo DATETIME2 e il valore predefinito è 00.00.00.0000000 0001-01-01.

[ **\@schedule_end_time=** ] schedule_end_time  
Data in cui è possibile arrestare l'esecuzione del processo. schedule_end_time è di tipo DATETIME2 e il valore predefinito è 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
Dopo l'esecuzione di sp_add_job per aggiungere un processo, è possibile usare sp_add_jobstep per aggiungere i passaggi che eseguono le attività per il processo. Il numero di versione iniziale del processo è 0, che verrà incrementato a 1 quando si aggiunge il primo passaggio.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.



### <a name="sp_delete_job"></a>sp_delete_job

Elimina il processo esistente.

#### <a name="syntax"></a>Sintassi

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argomenti
**[\@job_name =** ]' job_name '  
Nome del processo da eliminare. job_name è di tipo nvarchar(128).

**[\@Force =** ] forza  
Specifica se eseguire l'eliminazione e annullare le eventuali esecuzioni del processo in corso (1) o non eseguire l'eliminazione se sono in corso esecuzioni del processo (0). force è di tipo bit.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
La cronologia del processo viene eliminata automaticamente quando si elimina un processo.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.



### <a name="sp_add_jobstep"></a>sp_add_jobstep

Aggiunge un passaggio a un processo.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argomenti

**[\@job_name =** ]' job_name '  
Nome del processo a cui aggiungere il passaggio. job_name è di tipo nvarchar(128).

**[\@step_id =** ] step_id  
Numero di identificazione della sequenza per il passaggio del processo. I numeri di identificazione dei passaggi iniziano da 1 e vengono incrementati senza interruzioni. Se l'ID è già assegnato a un passaggio esistente, l'ID di questo passaggio e di tutti i passaggi successivi verrà incrementato in modo da inserire il nuovo passaggio nella sequenza. Se non è specificato, step_id verrà assegnato automaticamente all'ultimo passaggio della sequenza. step_id è di tipo int.

**[\@step_name =** ] step_name  
Nome del passaggio. È necessario specificare il nome, tranne che per il primo passaggio di un processo che, per praticità, ha il nome predefinito "JobStep". step_name è di tipo nvarchar(128).

**[\@command_type =** ]' command_type '  
Tipo di comando eseguito da questo passaggio del processo. command_type è di tipo nvarchar(50) e il valore predefinito è TSql, ovvero il valore del parametro @command_type è uno script T-SQL.

Se specificato, il valore deve essere TSql.

**[\@command_source =** ]' command_source '  
Tipo di posizione in cui è archiviato il comando. command_source è di tipo nvarchar(50) e il valore predefinito è Inline, ovvero il valore del parametro @command_source è il testo del comando.

Se specificato, il valore deve essere Inline.

**[\@Command =** ]' Command '  
Il comando deve essere uno script T-SQL valido, che verrà eseguito da questo passaggio del processo. command è di tipo nvarchar(max) e il valore predefinito è NULL.

**[\@credential_name =** ]' credential_name '  
Nome delle credenziali con ambito database archiviate in questo database di controllo dei processi usato per connettersi a ognuno dei database di destinazione all'interno del gruppo di destinazione quando viene eseguito questo passaggio. credential_name è di tipo nvarchar(128).

**[\@target_group_name =** ]' target-group_name '  
Nome del gruppo di destinazione che contiene i database di destinazione in cui verrà eseguito il passaggio del processo. target_group_name è di tipo nvarchar(128).

**[\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Ritardo prima del primo tentativo, se il passaggio del processo non riesce durante il tentativo di esecuzione iniziale. initial_retry_interval_seconds è di tipo int e il valore predefinito è 1.

**[\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Ritardo massimo tra i tentativi. Se il ritardo tra i tentativi supera questo valore, viene limitato in base al valore. maximum_retry_interval_seconds è di tipo int e il valore predefinito è 120.

**[\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Moltiplicatore da applicare al ritardo tra i tentativi se più tentativi di esecuzione del passaggio del processo hanno esito negativo. Ad esempio, se il primo tentativo ha un ritardo di 5 secondi e il moltiplicatore di backoff è 2.0, il secondo tentativo avrà un ritardo di 10 e il terzo un ritardo di 20 secondi. retry_interval_backoff_multiplier è di tipo real e il valore predefinito è 2.0.

**[\@retry_attempts =** ] retry_attempts  
Numero di tentativi di esecuzione se il tentativo iniziale non riesce. Ad esempio, se il valore retry_attempts è 10, verranno eseguiti 10 tentativi dopo il tentativo iniziale, per un totale di 11 tentativi. Se il tentativo finale non riesce, l'esecuzione del processo termina con un ciclo di vita Failed. retry_attempts è di tipo int e il valore predefinito è 10.

**[\@step_timeout_seconds =** ] step_timeout_seconds  
Tempo massimo consentito per l'esecuzione del passaggio. Se questo tempo viene superato, l'esecuzione del processo termina con un ciclo di vita TimedOut. step_timeout_seconds è di tipo int e il valore predefinito è 43.200 secondi (12 ore).

[ **\@output_type =** ] 'output_type'  
Se non è Null, il tipo di destinazione in cui viene scritto il primo set di risultati del comando. output_type è di tipo nvarchar(50) e il valore predefinito è NULL.

Se specificato, il valore deve essere SqlDatabase.

**[\@output_credential_name =** ]' output_credential_name '  
Se non è Null, il nome delle credenziali con ambito database usato per la connessione al database di destinazione di output. È necessario specificare il nome se output_type è uguale a SqlDatabase. output_credential_name è di tipo nvarchar(128) e il valore predefinito è NULL.

**[\@output_subscription_id =** ]' output_subscription_id '  
Descrizione da inserire.

**[\@output_resource_group_name =** ]' output_resource_group_name '  
Descrizione da inserire.

**[\@output_server_name =** ]' output_server_name '  
Se non è Null, il nome DNS completo del server che contiene il database di destinazione di output. È necessario specificare il nome se output_type è uguale a SqlDatabase. output_server_name è di tipo nvarchar(256) e il valore predefinito è NULL.

**[\@output_database_name =** ]' output_database_name '  
Se non è Null, il nome del database che contiene la tabella di destinazione di output. È necessario specificare il nome se output_type è uguale a SqlDatabase. output_database_name è di tipo nvarchar(128) e il valore predefinito è NULL.

**[\@output_schema_name =** ]' output_schema_name '  
Se non è Null, il nome dello schema SQL che contiene la tabella di destinazione di output. Se output_type è uguale a SqlDatabase, il valore predefinito è dbo. output_schema_name è di tipo nvarchar(128).

**[\@output_table_name =** ]' output_table_name '  
Se non è Null, il nome della tabella in cui verrà scritto il primo set di risultati del comando. Se la tabella non esiste già, verrà creata in base allo schema del set di risultati restituito. È necessario specificare il nome se output_type è uguale a SqlDatabase. output_table_name è di tipo nvarchar(128) e il valore predefinito è NULL.

**[\@job_version =** ] job_version output  
Parametro di output che verrà assegnato al nuovo numero di versione del processo. job_version è di tipo int.

**[\@max_parallelism =** ] max_parallelism output  
Livello massimo di parallelismo per ogni pool elastico. Se impostato, il passaggio del processo sarà limitato in modo da eseguire solo il numero massimo di database per ogni pool elastico. Questo vale per ogni pool elastico, incluso direttamente nel gruppo di destinazione o all'interno di un server incluso nel gruppo di destinazione. max_parallelism è di tipo int.


#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
Quando sp_add_jobstep ha esito positivo, viene incrementato il numero di versione corrente del processo. Alla successiva esecuzione del processo, verrà usata la nuova versione. Se il processo è in esecuzione, l'esecuzione non conterrà il nuovo passaggio.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:  

- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.



### <a name="sp_update_jobstep"></a>sp_update_jobstep

Aggiorna un passaggio di un processo.

#### <a name="syntax"></a>Sintassi

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argomenti
**[\@job_name =** ]' job_name '  
Nome del processo a cui appartiene il passaggio. job_name è di tipo nvarchar(128).

**[\@step_id =** ] step_id  
Numero di identificazione per il passaggio del processo da modificare. È necessario specificare step_id o step_name. step_id è di tipo int.

**[\@step_name =** ]' step_name '  
Nome del passaggio da modificare. È necessario specificare step_id o step_name. step_name è di tipo nvarchar(128).

[ **\@new_id =** ] new_id  
Nuovo numero di identificazione della sequenza per il passaggio del processo. I numeri di identificazione dei passaggi iniziano da 1 e vengono incrementati senza interruzioni. Se un passaggio viene riordinato, gli altri passaggi verranno rinumerati automaticamente.

**[\@new_name =** ]' new_name '  
Nuovo nome del passaggio. new_name è di tipo nvarchar(128).

**[\@command_type =** ]' command_type '  
Tipo di comando eseguito da questo passaggio del processo. command_type è di tipo nvarchar(50) e il valore predefinito è TSql, ovvero il valore del parametro @command_type è uno script T-SQL.

Se specificato, il valore deve essere TSql.

**[\@command_source =** ]' command_source '  
Tipo di posizione in cui è archiviato il comando. command_source è di tipo nvarchar(50) e il valore predefinito è Inline, ovvero il valore del parametro @command_source è il testo del comando.

Se specificato, il valore deve essere Inline.

**[\@Command =** ]' Command '  
Il comando deve essere uno script T-SQL valido, che verrà eseguito da questo passaggio del processo. command è di tipo nvarchar(max) e il valore predefinito è NULL.

**[\@credential_name =** ]' credential_name '  
Nome delle credenziali con ambito database archiviate in questo database di controllo dei processi usato per connettersi a ognuno dei database di destinazione all'interno del gruppo di destinazione quando viene eseguito questo passaggio. credential_name è di tipo nvarchar(128).

**[\@target_group_name =** ]' target-group_name '  
Nome del gruppo di destinazione che contiene i database di destinazione in cui verrà eseguito il passaggio del processo. target_group_name è di tipo nvarchar(128).

**[\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Ritardo prima del primo tentativo, se il passaggio del processo non riesce durante il tentativo di esecuzione iniziale. initial_retry_interval_seconds è di tipo int e il valore predefinito è 1.

**[\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Ritardo massimo tra i tentativi. Se il ritardo tra i tentativi supera questo valore, viene limitato in base al valore. maximum_retry_interval_seconds è di tipo int e il valore predefinito è 120.

**[\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Moltiplicatore da applicare al ritardo tra i tentativi se più tentativi di esecuzione del passaggio del processo hanno esito negativo. Ad esempio, se il primo tentativo ha un ritardo di 5 secondi e il moltiplicatore di backoff è 2.0, il secondo tentativo avrà un ritardo di 10 e il terzo un ritardo di 20 secondi. retry_interval_backoff_multiplier è di tipo real e il valore predefinito è 2.0.

**[\@retry_attempts =** ] retry_attempts  
Numero di tentativi di esecuzione se il tentativo iniziale non riesce. Ad esempio, se il valore retry_attempts è 10, verranno eseguiti 10 tentativi dopo il tentativo iniziale, per un totale di 11 tentativi. Se il tentativo finale non riesce, l'esecuzione del processo termina con un ciclo di vita Failed. retry_attempts è di tipo int e il valore predefinito è 10.

**[\@step_timeout_seconds =** ] step_timeout_seconds  
Tempo massimo consentito per l'esecuzione del passaggio. Se questo tempo viene superato, l'esecuzione del processo termina con un ciclo di vita TimedOut. step_timeout_seconds è di tipo int e il valore predefinito è 43.200 secondi (12 ore).

[ **\@output_type =** ] 'output_type'  
Se non è Null, il tipo di destinazione in cui viene scritto il primo set di risultati del comando. Per reimpostare il valore di output_type su NULL, impostare il valore del parametro su '' (stringa vuota). output_type è di tipo nvarchar(50) e il valore predefinito è NULL.

Se specificato, il valore deve essere SqlDatabase.

**[\@output_credential_name =** ]' output_credential_name '  
Se non è Null, il nome delle credenziali con ambito database usato per la connessione al database di destinazione di output. È necessario specificare il nome se output_type è uguale a SqlDatabase. Per reimpostare il valore di output_credential_name su NULL, impostare il valore del parametro su '' (stringa vuota). output_credential_name è di tipo nvarchar(128) e il valore predefinito è NULL.

**[\@output_server_name =** ]' output_server_name '  
Se non è Null, il nome DNS completo del server che contiene il database di destinazione di output. È necessario specificare il nome se output_type è uguale a SqlDatabase. Per reimpostare il valore di output_server_name su NULL, impostare il valore del parametro su '' (stringa vuota). output_server_name è di tipo nvarchar(256) e il valore predefinito è NULL.

**[\@output_database_name =** ]' output_database_name '  
Se non è Null, il nome del database che contiene la tabella di destinazione di output. È necessario specificare il nome se output_type è uguale a SqlDatabase. Per reimpostare il valore di output_database_name su NULL, impostare il valore del parametro su '' (stringa vuota). output_database_name è di tipo nvarchar(128) e il valore predefinito è NULL.

**[\@output_schema_name =** ]' output_schema_name '  
Se non è Null, il nome dello schema SQL che contiene la tabella di destinazione di output. Se output_type è uguale a SqlDatabase, il valore predefinito è dbo. Per reimpostare il valore di output_schema_name su NULL, impostare il valore del parametro su '' (stringa vuota). output_schema_name è di tipo nvarchar(128).

**[\@output_table_name =** ]' output_table_name '  
Se non è Null, il nome della tabella in cui verrà scritto il primo set di risultati del comando. Se la tabella non esiste già, verrà creata in base allo schema del set di risultati restituito. È necessario specificare il nome se output_type è uguale a SqlDatabase. Per reimpostare il valore di output_server_name su NULL, impostare il valore del parametro su '' (stringa vuota). output_table_name è di tipo nvarchar(128) e il valore predefinito è NULL.

**[\@job_version =** ] job_version output  
Parametro di output che verrà assegnato al nuovo numero di versione del processo. job_version è di tipo int.

**[\@max_parallelism =** ] max_parallelism output  
Livello massimo di parallelismo per ogni pool elastico. Se impostato, il passaggio del processo sarà limitato in modo da eseguire solo il numero massimo di database per ogni pool elastico. Questo vale per ogni pool elastico, incluso direttamente nel gruppo di destinazione o all'interno di un server incluso nel gruppo di destinazione. Per reimpostare il valore di max_parallelism su Null, impostare il valore del parametro su -1. max_parallelism è di tipo int.


#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
Le eventuali esecuzioni in corso del processo non saranno interessate. Quando sp_update_jobstep ha esito positivo, viene incrementato il numero di versione del processo. Alla successiva esecuzione del processo, verrà usata la nuova versione.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:

- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.




### <a name="sp_delete_jobstep"></a>sp_delete_jobstep

Rimuove un passaggio da un processo.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argomenti
**[\@job_name =** ]' job_name '  
Nome del processo da cui rimuovere il passaggio. job_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@step_id =** ] step_id  
Numero di identificazione per il passaggio del processo da eliminare. È necessario specificare step_id o step_name. step_id è di tipo int.

**[\@step_name =** ]' step_name '  
Nome del passaggio da eliminare. È necessario specificare step_id o step_name. step_name è di tipo nvarchar(128).

**[\@job_version =** ] job_version output  
Parametro di output che verrà assegnato al nuovo numero di versione del processo. job_version è di tipo int.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
Le eventuali esecuzioni in corso del processo non saranno interessate. Quando sp_update_jobstep ha esito positivo, viene incrementato il numero di versione del processo. Alla successiva esecuzione del processo, verrà usata la nuova versione.

Gli altri passaggi del processo verranno rinumerati automaticamente per colmare il vuoto lasciato dal passaggio del processo eliminato.
 
#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.






### <a name="sp_start_job"></a>sp_start_job

Avvia l'esecuzione di un processo.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argomenti
**[\@job_name =** ]' job_name '  
Nome del processo da cui rimuovere il passaggio. job_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@job_execution_id =** ] job_execution_id output  
Parametro di output che verrà assegnato all'ID dell'esecuzione del processo. job_version è di tipo uniqueidentifier.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
No.
 
#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

### <a name="sp_stop_job"></a>sp_stop_job

Arresta l'esecuzione di un processo.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argomenti
**[\@job_execution_id =** ] job_execution_id  
Numero di identificazione dell'esecuzione del processo da arrestare. job_execution_id è di tipo uniqueidentifier e il valore predefinito è NULL.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
No.
 
#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.


### <a name="sp_add_target_group"></a>sp_add_target_group

Aggiunge un gruppo di destinazione.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argomenti
**[\@target_group_name =** ]' target_group_name '  
Nome del gruppo di destinazione da creare. target_group_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@target_group_id =** ] target_group_id output del numero di identificazione del gruppo di destinazione assegnato al processo, se creato correttamente. target_group_id è una variabile di output di tipo uniqueidentifier e il valore predefinito è NULL.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
I gruppi di destinazione offrono un modo semplice per specificare una raccolta di database come destinazione di un processo.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

### <a name="sp_delete_target_group"></a>sp_delete_target_group

Elimina un gruppo di destinazione.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argomenti
**[\@target_group_name =** ]' target_group_name '  
Nome del gruppo di destinazione da eliminare. target_group_name è di tipo nvarchar(128), senza alcun valore predefinito.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
No.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

### <a name="sp_add_target_group_member"></a>sp_add_target_group_member

Aggiunge un database o un gruppo di database a un gruppo di destinazione.

#### <a name="syntax"></a>Sintassi

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argomenti
**[\@target_group_name =** ]' target_group_name '  
Nome del gruppo di destinazione a cui verrà aggiunto il membro. target_group_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@membership_type =** ]' membership_type '  
Specifica se il membro del gruppo di destinazione deve essere incluso o escluso. target_group_name è di tipo nvarchar(128) e il valore predefinito è "Include". I valori validi per target_group_name sono "Include" o "Exclude".

**[\@target_type =** ]' target_type '  
Tipo di database o raccolta di database di destinazione, inclusi tutti i database in un server, tutti i database in un pool elastico, tutti i database in una mappa partizioni o un singolo database. target_type è di tipo nvarchar(128), senza alcun valore predefinito. I valori validi per target_type sono "SqlServer", "SqlElasticPool", "SqlDatabase" o "SqlShardMap". 

**[\@refresh_credential_name =** ]' refresh_credential_name '  
Nome del server di database SQL. refresh_credential_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@nome_server =** ]' nome_server '  
Nome del server di database SQL da aggiungere al gruppo di destinazione specificato. È necessario specificare server_name se target_type è "SqlServer". server_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@database_name =** ]' database_name '  
Nome del database da aggiungere al gruppo di destinazione specificato. È necessario specificare database_name se target_type è "SqlDatabase". database_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@elastic_pool_name =** ]' elastic_pool_name '  
Nome del pool elastico da aggiungere al gruppo di destinazione specificato. È necessario specificare elastic_pool_name se target_type è "SqlElasticPool". elastic_pool_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@shard_map_name =** ]' shard_map_name '  
Nome del pool della mappa partizioni da aggiungere al gruppo di destinazione specificato. È necessario specificare elastic_pool_name se target_type è "SqlSqlShardMap". shard_map_name è di tipo nvarchar(128), senza alcun valore predefinito.

**[\@target_id =** ] target_group_id output  
Numero di identificazione della destinazione assegnato al membro del gruppo di destinazione se viene creato e aggiunto correttamente al gruppo di destinazione. target_id è una variabile di output di tipo uniqueidentifier e il valore predefinito è NULL.
Valori del codice restituito 0 (operazione completata) o 1 (operazione non riuscita)

#### <a name="remarks"></a>Note
Un processo viene eseguito in tutti i database singoli in un server di database SQL o un pool elastico al momento dell'esecuzione quando un server di database SQL o un pool elastico è incluso nel gruppo di destinazione.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

#### <a name="examples"></a>Esempi
Nell'esempio seguente vengono aggiunti tutti i database nei server di Londra e New York al gruppo Servers Maintaining Customer Information. È necessario connettersi al database dei processi specificato al momento della creazione dell'agente processo, in questo caso ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Rimuove un membro del gruppo di destinazione da un gruppo di destinazione.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argomenti [ @target_group_name = ] 'target_group_name'  
Nome del gruppo di destinazione da cui rimuovere il membro del gruppo di destinazione. target_group_name è di tipo nvarchar(128), senza alcun valore predefinito.

[ @target_id = ] target_id  
 Numero di identificazione della destinazione assegnato al membro del gruppo di destinazione da rimuovere. target_id è di tipo uniqueidentifier e il valore predefinito è NULL.

#### <a name="return-code-values"></a>Valori restituiti
0 (esito positivo) o 1 (esito negativo)

#### <a name="remarks"></a>Note
I gruppi di destinazione offrono un modo semplice per specificare una raccolta di database come destinazione di un processo.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

#### <a name="examples"></a>Esempi
Nell'esempio seguente viene rimosso il server di Londra dal gruppo Servers Maintaining Customer Information. È necessario connettersi al database dei processi specificato al momento della creazione dell'agente processo, in questo caso ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Rimuove i record della cronologia per un processo.

#### <a name="syntax"></a>Sintassi


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argomenti
**[\@job_name =** ]' job_name '  
Nome del processo per cui eliminare i record della cronologia. job_name è di tipo nvarchar(128) e il valore predefinito è NULL. È necessario specificare job_id o job_name, ma non è possibile specificarli entrambi.

[ **\@job_id =** ] job_id  
 Numero di identificazione del processo per i record da eliminare. job_id è di tipo uniqueidentifier e il valore predefinito è NULL. È necessario specificare job_id o job_name, ma non è possibile specificarli entrambi.

**[\@oldest_date =** ] oldest_date  
 Record meno recente da conservare nella cronologia. oldest_date è DATETIME2 e il valore predefinito è NULL. Quando viene specificato oldest_date, sp_purge_jobhistory rimuove solo i record meno recenti rispetto al valore specificato.

#### <a name="return-code-values"></a>Valori del codice restituito
0 (operazione completata) o 1 (operazione non riuscita) I gruppi di destinazione offrono un modo semplice per specificare una raccolta di database come destinazione di un processo.

#### <a name="permissions"></a>Autorizzazioni
Per impostazione predefinita, i membri del ruolo predefinito del server sysadmin possono eseguire questa stored procedure. Per fare in modo che un utente possa solo monitorare i processi, è possibile includere l'utente nel ruolo del database seguente nel database dell'agente processo specificato al momento della creazione dell'agente processo:
- jobs_reader

Per informazioni dettagliate sulle autorizzazioni di questi ruoli, vedere la sezione Autorizzazioni di questo documento. Solo i membri del ruolo sysadmin possono usare questa stored procedure per modificare gli attributi dei processi di proprietà di altri utenti.

#### <a name="examples"></a>Esempi
Nell'esempio seguente vengono aggiunti tutti i database nei server di Londra e New York al gruppo Servers Maintaining Customer Information. È necessario connettersi al database dei processi specificato al momento della creazione dell'agente processo, in questo caso ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Viste dei processi

Le viste seguenti sono disponibili nel [database dei processi](sql-database-job-automation-overview.md#job-database).


|Visualizza  |Descrizione  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Mostra la cronologia di esecuzione dei processi.      |
|[jobs](#jobs-view)     |   Mostra tutti i processi.      |
|[job_versions](#job_versions-view)     |   Mostra tutte le versioni dei processi.      |
|[jobsteps](#jobsteps-view)     |     Mostra tutti i passaggi nella versione corrente di ogni processo.    |
|[jobstep_versions](#jobstep_versions-view)     |     Mostra tutti i passaggi in tutte le versioni di ogni processo.    |
|[target_groups](#target_groups-view)     |      Mostra tutti i gruppi di destinazione.   |
|[target_group_members](#target_groups_members-view)     |   Mostra tutti i membri di tutti i gruppi di destinazione.      |


### <a name="job_executions-view"></a>visualizzazione job_executions

[processi]. [job_executions]

Mostra la cronologia di esecuzione dei processi.


|Nome colonna|   Tipo di dati   |DESCRIZIONE|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  ID univoco di un'istanza di un'esecuzione di un processo.
|**job_name**   |nvarchar(128)  |Nome del processo.
|**job_id** |uniqueidentifier|  ID univoco del processo.
|**job_version**    |int    |Versione del processo (aggiornata automaticamente ogni volta che viene modificato il processo).
|**step_id**    |int|   Identificatore univoco (a livello di processo) per il passaggio. NULL indica che si tratta dell'esecuzione del processo padre.
|**is_active**| bit |Indica se le informazioni sono attive o inattive. 1 indica i processi attivi, mentre 0 indica quelli inattivi.
|**lifecycle**| nvarchar(50)|Valore che indica lo stato del processo: "Created", "In Progress", "Failed", "Succeeded", "Skipped", "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   Data e ora in cui è stato creato il processo.
|**start_time** |datetime2(7)|  Data e ora di avvio dell'esecuzione del processo. NULL se il processo non è ancora stato eseguito.
|**end_time**|  datetime2(7)    |Data e ora di completamento dell'esecuzione del processo. NULL se il processo non è ancora stato eseguito o l'esecuzione non è ancora stata completata.
|**current_attempts**   |int    |Numero di tentativi di esecuzione del passaggio. Il valore è 0 per il processo padre oppure 1 o superiore per le esecuzioni dei processi figlio, in base ai criteri di esecuzione.
|**current_attempt_start_time** |datetime2(7)|  Data e ora di avvio dell'esecuzione del processo. NULL indica che si tratta dell'esecuzione del processo padre.
|**last_message**   |nvarchar(max)| Messaggio della cronologia dei processi o dei passaggi. 
|**target_type**|   nvarchar(128)   |Tipo di database o raccolta di database di destinazione, inclusi tutti i database in un server, tutti i database in un pool elastico o un database. I valori validi per target_type sono "SqlServer", "SqlElasticPool" o "SqlDatabase". NULL indica che si tratta dell'esecuzione del processo padre.
|**target_id**  |uniqueidentifier|  ID univoco del membro del gruppo di destinazione.  NULL indica che si tratta dell'esecuzione del processo padre.
|**target_group_name**  |nvarchar(128)  |Nome del gruppo di destinazione. NULL indica che si tratta dell'esecuzione del processo padre.
|**target_server_name**|    nvarchar(256)|  Nome del server di database SQL contenuto nel gruppo di destinazione. Specificato solo se target_type è "SqlServer". NULL indica che si tratta dell'esecuzione del processo padre.
|**target_database_name**   |nvarchar(128)| Nome del database contenuto nel gruppo di destinazione. Specificato solo quando target_type è "SqlDatabase". NULL indica che si tratta dell'esecuzione del processo padre.


### <a name="jobs-view"></a>Vista jobs

[jobs].[jobs]

Mostra tutti i processi.

|Nome colonna|   Tipo di dati|  DESCRIZIONE|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome del processo.|
|**job_id**|    uniqueidentifier    |ID univoco del processo.|
|**job_version**    |int    |Versione del processo (aggiornata automaticamente ogni volta che viene modificato il processo).|
|**description**    |nvarchar(512)| Descrizione del processo. Bit enabled    Indica se il processo è abilitato o disabilitato. 1 indica i processi abilitati, mentre 0 indica processi disabilitati.|
|**schedule_interval_type** |nvarchar(50)   |Valore che indica quando deve essere eseguito il processo: "Once", "Minutes", "Hours", "Days", "Weeks", "Months"
|**schedule_interval_count**|   int|    Numero di periodi schedule_interval_type tra ogni esecuzione del processo.|
|**schedule_start_time**    |datetime2(7)|  Data e ora di avvio dell'ultima esecuzione del processo.|
|**schedule_end_time**| datetime2(7)|   Data e ora di completamento dell'ultima esecuzione del processo.|


### <a name="job_versions-view"></a>visualizzazione job_versions

[jobs].[job_versions]

Mostra tutte le versioni dei processi.

|Nome colonna|   Tipo di dati|  DESCRIZIONE|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome del processo.|
|**job_id**|    uniqueidentifier    |ID univoco del processo.|
|**job_version**    |int    |Versione del processo (aggiornata automaticamente ogni volta che viene modificato il processo).|


### <a name="jobsteps-view"></a>Vista jobsteps

[jobs].[jobsteps]

Mostra tutti i passaggi nella versione corrente di ogni processo.

|Nome colonna    |Tipo di dati| Descrizione|
|------|------|-------|
|**job_name**   |nvarchar(128)| Nome del processo.|
|**job_id** |uniqueidentifier   |ID univoco del processo.|
|**job_version**|   int|    Versione del processo (aggiornata automaticamente ogni volta che viene modificato il processo).|
|**step_id**    |int    |Identificatore univoco (a livello di processo) per il passaggio.|
|**step_name**  |nvarchar(128)  |Nome univoco (a livello di processo) per il passaggio.|
|**command_type**   |nvarchar(50)   |Tipo di comando da eseguire nel passaggio del processo. Per la versione 1, il valore deve essere uguale a "TSql", che è anche il valore predefinito.|
|**command_source** |nvarchar(50)|  Posizione del comando. Per la versione 1, "Inline" è il valore predefinito e l'unico valore accettato.|
|**command**|   nvarchar(max)|  I comandi da eseguire dai processi elastici tramite command_type.|
|**credential_name**|   nvarchar(128)   |Nome delle credenziali con ambito database usate per l'esecuzione del processo.|
|**target_group_name**| nvarchar(128)   |Nome del gruppo di destinazione.|
|**target_group_id**|   uniqueidentifier|   ID univoco del gruppo di destinazione.|
|**initial_retry_interval_seconds**|    int |Ritardo prima del primo tentativo. Il valore predefinito è 1.|
|**maximum_retry_interval_seconds** |int|   Ritardo massimo tra i tentativi. Se il ritardo tra i tentativi supera questo valore, viene limitato in base al valore. Il valore predefinito è 120.|
|**retry_interval_backoff_multiplier**  |real|  Moltiplicatore da applicare al ritardo tra i tentativi se più tentativi di esecuzione del passaggio del processo hanno esito negativo. Il valore predefinito è 2.0.|
|**retry_attempts** |int|   Numero di tentativi da eseguire se il passaggio ha esito negativo. Il valore predefinito è 10, che indica nessun tentativo.|
|**step_timeout_seconds**   |int|   Quantità di tempo in minuti tra i tentativi. Il valore predefinito è 0, che indica un intervallo di 0 minuti.|
|**output_type**    |nvarchar(11)|  Posizione del comando. Nell'anteprima corrente, "Inline" è il valore predefinito e l'unico valore accettato.|
|**output_credential_name**|    nvarchar(128)   |Nome delle credenziali da usare per la connessione al server di destinazione per l'archiviazione del set di risultati.|
|**output_subscription_id**|    uniqueidentifier|   ID univoco della sottoscrizione del server\database di destinazione per il set di risultati ottenuto dall'esecuzione della query.|
|**output_resource_group_name** |nvarchar(128)| Nome del gruppo di risorse in cui si trova il server di destinazione.|
|**output_server_name**|    nvarchar(256)   |Nome del server di destinazione per il set di risultati.|
|**output_database_name**   |nvarchar(128)| Nome del database di destinazione per il set di risultati.|
|**output_schema_name** |nvarchar(max)| Nome logico dello schema di destinazione. Se non è specificato, il valore predefinito è dbo.|
|**output_table_name**| nvarchar(max)|  Nome della tabella per archiviare il set di risultati ottenuto dai risultati della query. La tabella verrà creata automaticamente in base allo schema del set di risultati, se non esiste già. Lo schema deve corrispondere allo schema del set di risultati.|
|**max_parallelism**|   int|    Numero massimo di database per ogni pool elastico in cui il passaggio del processo verrà eseguito contemporaneamente. Il valore predefinito è NULL, che indica nessun limite. |


### <a name="jobstep_versions-view"></a>visualizzazione jobstep_versions

[jobs].[jobstep_versions]

Mostra tutti i passaggi in tutte le versioni di ogni processo. Lo schema è identico a [jobsteps](#jobsteps-view).

### <a name="target_groups-view"></a>visualizzazione target_groups

[jobs].[target_groups]

Elenca tutti i gruppi di destinazione.

|Nome colonna|Tipo di dati| Descrizione|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Nome del gruppo di destinazione, una raccolta di database. 
|**target_group_id**    |uniqueidentifier   |ID univoco del gruppo di destinazione.

### <a name="target_groups_members-view"></a>visualizzazione target_groups_members

[jobs].[target_groups_members]

Mostra tutti i membri di tutti i gruppi di destinazione.

|Nome colonna|Tipo di dati| Descrizione|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|Nome del gruppo di destinazione, una raccolta di database. |
|**target_group_id**    |uniqueidentifier   |ID univoco del gruppo di destinazione.|
|**membership_type**    |int|   Specifica se il membro del gruppo di destinazione è incluso o escluso nel gruppo di destinazione. I valori validi per target_group_name sono "Include" o "Exclude".|
|**target_type**    |nvarchar(128)| Tipo di database o raccolta di database di destinazione, inclusi tutti i database in un server, tutti i database in un pool elastico o un database. I valori validi per target_type sono "SqlServer", "SqlElasticPool", "SqlDatabase" o "SqlShardMap".|
|**target_id**  |uniqueidentifier|  ID univoco del membro del gruppo di destinazione.|
|**refresh_credential_name**    |nvarchar(128)  |Nome delle credenziali con ambito database usate per la connessione al membro del gruppo di destinazione.|
|**subscription_id**    |uniqueidentifier|  ID univoco della sottoscrizione.|
|**resource_group_name**    |nvarchar(128)| Nome del gruppo di risorse in cui si trova il membro del gruppo di destinazione.|
|**server_name**    |nvarchar(128)  |Nome del server di database SQL contenuto nel gruppo di destinazione. Specificato solo se target_type è "SqlServer". |
|**database_name**  |nvarchar(128)  |Nome del database contenuto nel gruppo di destinazione. Specificato solo quando target_type è "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| Nome del pool elastico contenuto nel gruppo di destinazione. Specificato solo quando target_type è "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| Nome della mappa partizioni contenuta nel gruppo di destinazione. Specificato solo quando target_type è "SqlShardMap".|


## <a name="resources"></a>Risorse

 - ![Icona di collegamento a un argomento](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Icona di collegamento a un argomento")[Convenzioni della sintassi Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Passaggi successivi

- [Creare e gestire processi elastici usando PowerShell](elastic-jobs-powershell.md)
- [Autorizzazioni in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
