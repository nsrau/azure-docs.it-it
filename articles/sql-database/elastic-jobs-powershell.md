---
title: Creare un agente processo elastico con PowerShell
description: Informazioni su come creare un agente processo elastico tramite PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 74a72df9d8c0bc8a578fea57ab81fb496f8e6add
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420364"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Creare un agente processo elastico con PowerShell

I [processi elastici](sql-database-job-automation-overview.md#elastic-database-jobs-preview) permettono l'esecuzione di uno o più script di Transact-SQL (T-SQL) in parallelo tra molti database.

Questa esercitazione illustra la procedura necessaria per eseguire una query tra più database:

> [!div class="checklist"]
> * Creare un agente processo elastico
> * Creare le credenziali di processo in modo che i processi possano eseguire script nelle relative destinazioni
> * Definire le destinazioni (server, pool elastici, database e mappe delle partizioni) in cui eseguire i processi
> * Creare credenziali con ambito database nei database di destinazione in modo che l'agente possa connettersi ed eseguire processi
> * Creare un processo
> * Aggiungere passaggi del processo a un processo
> * Avviare l'esecuzione di un processo
> * Monitorare un processo

## <a name="prerequisites"></a>Prerequisiti

La versione aggiornata dei processi di database elastico include un nuovo set di cmdlet di PowerShell per l'uso durante la migrazione. Questi nuovi cmdlet consentono di trasferire tutte le credenziali di processo esistenti, le destinazioni (inclusi database, server e raccolte personalizzate), i trigger dei processi, le pianificazioni dei processi, il contenuto dei processi e i processi in un nuovo agente processo elastico.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Installare la versione più recente dei cmdlet per i processi elastici

Se non si ha già una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Installare il modulo **Az.Sql** per ottenere i cmdlet per processi elastici più recenti. Eseguire questi comandi in PowerShell con accesso amministrativo.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Oltre al modulo **Az.Sql**, questa esercitazione richiede anche il modulo *SqlServer* di PowerShell. Per i dettagli, vedere [Installare il modulo SQL Server PowerShell](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Creare le risorse necessarie

La creazione di un agente processo elastico richiede un database (S0 o versione successiva) da usare come [database di processo](sql-database-job-automation-overview.md#job-database).

Lo script seguente crea un nuovo gruppo di risorse, un server e un database da usare come database di processo. Il secondo script crea un secondo server con due database vuoti per l'esecuzione dei processi.

I processi elastici non hanno requisiti di denominazione specifici, pertanto è possibile usare le convenzioni di denominazione desiderate, purché siano conformi ai [requisiti di Azure](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>Usare i processi elastici

Per usare i processi elastici, registrare la funzionalità nella sottoscrizione di Azure eseguendo il comando seguente, una volta sola per la sottoscrizione in cui si intende effettuare il provisioning dell'agente di processo elastico. Le sottoscrizioni che contengono solo database di destinazione del processo non devono essere registrate.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>Creare l'agente processo elastico

Un agente processo elastico è una risorsa di Azure per la creazione, l'esecuzione e la gestione dei processi. L'agente esegue i processi in base a una pianificazione o come processo unico.

Il cmdlet **New-AzSqlElasticJobAgent** richiede un database SQL di Azure già esistente, pertanto i parametri *resourceGroupName*, *serverName* e *databaseName* devono puntare tutti a risorse esistenti.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Creare le credenziali del processo

I processi usano credenziali con ambito database per la connessione ai database di destinazione specificati dal gruppo di destinazione al momento dell'esecuzione ed eseguono gli script. Queste credenziali con ambito database permettono inoltre di connettersi al database master per enumerare tutti i database in un server o in un pool elastico, quando uno di questi viene usato come tipo di membro del gruppo di destinazione.

Le credenziali con ambito database devono essere create nel database di processo. Tutti i database di destinazione devono disporre di un accesso con autorizzazioni sufficienti per completare correttamente il processo.

![Credenziali di processi elastici](media/elastic-jobs-overview/job-credentials.png)

Oltre alle credenziali riportate nella figura, si noti l'aggiunta dei comandi **GRANT** nello script seguente. Queste autorizzazioni sono necessarie per lo script che è stato scelto per il processo di esempio. Poiché nell'esempio viene creata una nuova tabella nei database di destinazione, ogni database di destinazione necessita di autorizzazioni appropriate per la corretta esecuzione.

Per creare le credenziali di processo richieste (nel database di processo), eseguire lo script seguente:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Definire i database di destinazione in cui eseguire il processo

Un [gruppo di destinazione](sql-database-job-automation-overview.md#target-group) definisce il set di uno o più database in cui verrà eseguito il passaggio di un processo.

Il frammento di codice seguente crea due gruppi di destinazione: *serverGroup* e *serverGroupExcludingDb2*. *serverGroup* ha come destinazione tutti i database esistenti nel server durante la fase di esecuzione, mentre *serverGroupExcludingDb2* ha come destinazione tutti i database nel server, ad eccezione di *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Creare un processo e i passaggi

Questo esempio definisce un processo e due passaggi di processo per l'esecuzione del processo. Il primo passaggio di processo (*step1*) crea una nuova tabella (*Step1Table*) in ogni database nel gruppo di destinazione *ServerGroup*. Il secondo passaggio di processo (*step2*) crea una nuova tabella (*Step2Table*) in ogni database ad eccezione di *targetDb2*, perché nel gruppo di destinazione definito in precedenza ne è stata specificata l'esclusione.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>Eseguire il processo

Per avviare immediatamente il processo, eseguire il comando seguente:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Dopo il corretto completamento, dovrebbero essere visibili due nuove tabelle in TargetDb1 e solo una nuova tabella in TargetDb2:

   ![Verifica delle nuove tabelle in SSMS](media/elastic-jobs-overview/job-execution-verification.png)

È anche possibile pianificare l'esecuzione del processo in un secondo momento. Per pianificare l'esecuzione di un processo in un momento specifico, eseguire il comando seguente:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Monitorare lo stato delle esecuzioni del processo

I frammenti di codice seguenti recuperano i dettagli di esecuzione del processo:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

La tabella seguente elenca i possibili stati di esecuzione dei processi:

|Stato|DESCRIZIONE|
|:---|:---|
|**Creato** | L'esecuzione del processo è stata appena creata e non è ancora in corso.|
|**InProgress** | L'esecuzione del processo è attualmente in corso.|
|**WaitingForRetry** | L'esecuzione del processo non è riuscita a completare l'azione ed è in attesa per riprovare.|
|**Completato** | L'esecuzione del processo è stata completata.|
|**SucceededWithSkipped** | L'esecuzione del processo è stata completata, ma alcuni elementi figlio sono stati ignorati.|
|**Non riuscito** | L'esecuzione del processo non è riuscita e ha esaurito i tentativi.|
|**TimedOut** | L'esecuzione del processo ha raggiunto il timeout.|
|**Canceled** | L'esecuzione del processo è stata annullata.|
|**Ignorato** | L'esecuzione del processo è stata ignorata perché un'altra esecuzione dello stesso passaggio del processo era già attiva nella stessa destinazione.|
|**WaitingForChildJobExecutions** | L'esecuzione del processo è in attesa delle esecuzioni figlio prima del completamento.|

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare le risorse di Azure create in questa esercitazione eliminando il gruppo di risorse.

> [!TIP]
> Se si intende continuare a usare questi processi, non è necessario pulire le risorse create in questo articolo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito uno script Transact-SQL in un set di database. Si è appreso come eseguire queste attività:

> [!div class="checklist"]
> * Creare un agente processo elastico
> * Creare le credenziali di processo in modo che i processi possano eseguire script nelle relative destinazioni
> * Definire le destinazioni (server, pool elastici, database e mappe delle partizioni) in cui eseguire i processi
> * Creare credenziali con ambito database nei database di destinazione in modo che l'agente possa connettersi ed eseguire processi
> * Creare un processo
> * Aggiungere un passaggio del processo al processo
> * Avviare un'esecuzione del processo
> * Monitorare il processo

> [!div class="nextstepaction"]
> [Gestire processi elastici con Transact-SQL](elastic-jobs-tsql.md)
