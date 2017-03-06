---
title: 'PowerShell: introduzione al database SQL di Azure | Documentazione Microsoft'
description: Come creare un server logico di database SQL, una regola del firewall a livello di server e un database usando PowerShell. Viene anche illustrato come effettuare una query dei database usando PowerShell.
keywords: creazione di un nuovo database sql,configurazione del database
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 02/23/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 78d9194f50bcdc4b0db7871f2480f59b26cfa8f6
ms.openlocfilehash: 7b7273edfa33f297cb5dc30ef380b6a737787b33
ms.lasthandoff: 02/27/2017


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>Esercitazione: Effettuare il provisioning e accedere a un database SQL di Azure usando PowerShell

In questa esercitazione introduttiva si apprenderà come usare PowerShell per:

* Creare un nuovo gruppo di risorse di Azure
* Creare un server logico di Azure SQL
* Visualizzare le proprietà del server di Azure SQL
* Creare una regola del firewall a livello di server
* Creare il database di esempio AdventureWorksLT come database singolo
* Visualizzare le proprietà del database di esempio Adventure Works LT
* Creare un database singolo vuoto

In questa esercitazione si apprenderà anche come:

* Connettersi al server logico e al database master
* Visualizzare le proprietà del database
* Connettersi al database di esempio
* Visualizzare le proprietà del database utente

Al termine di questa esercitazione, si disporrà di un database di esempio e di un database vuoto in esecuzione in un gruppo di risorse di Azure e collegato a un server logico. Sarà inoltre disponibile una regola del firewall a livello di server configurata per abilitare l'entità di sicurezza a livello di server per accedere al server da un indirizzo IP specificato (o da un intervallo di indirizzi IP). 

**Tempo stimato**: per questa esercitazione saranno necessari circa 30 minuti (presupponendo che i prerequisiti siano già soddisfatti).


> [!TIP]
> Per eseguire queste stesse attività in un'esercitazione introduttiva, è possibile usare [il portale di Azure](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Prerequisiti

* È necessario un account Azure. È possibile [aprire un account Azure gratuito](https://azure.microsoft.com/free/) o [attivare i benefici della sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* È necessario aver eseguito l'accesso usando un account membro del ruolo proprietario o collaboratore della sottoscrizione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione alla gestione degli accessi nel portale di Azure](../active-directory/role-based-access-control-what-is.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Creare un nuovo server logico di SQL con Azure PowerShell

È necessario un gruppo di risorse che contenga il server e il primo passaggio prevede quindi di creare un nuovo gruppo di risorse e un server ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)) o di ottenere riferimenti a gruppi di risorse e server esistenti ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).


I frammenti di codice seguenti consentono di creare un gruppo di risorse e un server di Azure SQL, se non esistono già:

Per un elenco dei formati di stringa e dei percorsi di Azure validi, vedere la sezione [Frammenti di codice di supporto](#helper-snippets) più avanti nell'articolo.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Visualizzare le proprietà logiche di SQL Server con Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Creare una regola del firewall a livello di server con Azure PowerShell

È necessario conoscere il proprio indirizzo IP pubblico per impostare la regola del firewall. È possibile ottenere l'indirizzo IP usando un browser qualsiasi (chiedere "qual è il mio indirizzo IP"). Per informazioni dettagliate, vedere [regole del firewall](sql-database-firewall-configure.md).

Nell'esempio vengono usati i cmdlet [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) e [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) per ottenere un riferimento o creare una nuova regola. Per questo frammento di codice, se la regola esiste già, si limita a ottenere un riferimento a esso, senza aggiornare gli indirizzi IP iniziale e finale. È sempre possibile modificare la clausola **else** per usare la regola [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) per creare o aggiornare la funzionalità.

> [!NOTE] 
> È possibile aprire il firewall del database SQL nel server a un singolo indirizzo IP o a un intero intervallo di indirizzi. L'apertura del firewall consente agli amministratori SQL e agli utenti di accedere a qualsiasi database nel server per cui hanno credenziali valide.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule

# Allow Azure services to access the server
$serverFirewallRuleName2 = "allowAzureServices"
$serverFirewallStartIp2 = "0.0.0.0"
$serverFirewallEndIp2 = "0.0.0.0"

$myFirewallRule2 = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName2 -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule2)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName2"
   $myFirewallRule2 = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName2 -StartIpAddress $serverFirewallStartIp2 -EndIpAddress $serverFirewallEndIp2
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName2 already exists:"
}
$myFirewallRule2

```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Connettersi al server SQL con Azure PowerShell

Si procederà ora a eseguire una query rapida sul database master per verificare che sia possibile connettersi al server. Il frammento seguente usa il [provider .NET Framework per SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) per connettersi al server ed eseguire query sul database master. Crea una stringa di connessione sulla base delle variabili usate nei frammenti di codice precedenti. Sostituire i segnaposto con il nome utente e la password amministratore di SQL Server usati per creare il server nei passaggi precedenti.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Creare un nuovo database di esempio AdventureWorksLT con Azure PowerShell

Il frammento di codice seguente importa un file bacpac del database di esempio AdventureWorksLT mediante il cmdlet [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport). Il file bacpac si trova in un account di archiviazione BLOB di Azure pubblico e di sola lettura. Dopo aver eseguito il cmdlet di importazione, è possibile monitorare lo stato di avanzamento dell'operazione di importazione usando il cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).


```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "SharedAccessKey"
$storageUri = "https://sqldbtutorial.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac"
$storageKey = "?"

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Visualizzare le proprietà del database con Azure PowerShell

Dopo aver creato il database, è possibile visualizzarne alcune delle proprietà.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Connettersi al database di esempio ed eseguire la query con Azure PowerShell

Si procederà ora a eseguire una query rapida sul database AdventureWorksLT per verificare che sia possibile la connessione. Il frammento seguente usa il [provider .NET Framework per SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) per connettersi al database ed eseguire la query. Crea una stringa di connessione sulla base delle variabili usate nei frammenti di codice precedenti. Sostituire il segnaposto con la password amministratore di SQL server.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Creare un nuovo database vuoto con Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Completare lo script di Azure PowerShell per creare un server, una regola del firewall e un database



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"


# Storage account details for locating
# and accessing the sample .bacpac 
# Do Not Edit for this tutorial
$myStorageKeyType = "SharedAccessKey"
$myStorageUri = "https://sqldbtutorial.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac"
$myStorageKey = "?"



# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin

# Create or update server firewall rules
########################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule

# Allows Azure services to access the server
$serverFirewallRuleName2 = "allowAzureServices"
$serverFirewallStartIp2 = "0.0.0.0"
$serverFirewallEndIp2 = "0.0.0.0"

$myFirewallRule2 = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName2 -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule2)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName2"
   $myFirewallRule2 = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName2 -StartIpAddress $serverFirewallStartIp2 -EndIpAddress $serverFirewallEndIp2
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName2 already exists:"
}
$myFirewallRule2


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> È possibile risparmiare durante l'esercitazione eliminando i database che non si siano usano. È possibile ripristinare i database dell'edizione Basic entro sette giorni. Tuttavia, non eliminare un server. In tal caso, è possibile ripristinare il server o i relativi database eliminati.

## <a name="helper-snippets"></a>Frammenti di codice di supporto

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> È possibile risparmiare durante l'esercitazione eliminando i database che non si siano usano. È possibile ripristinare i database dell'edizione Basic in sette giorni. Tuttavia, non eliminare il server. In tal caso, è possibile ripristinare il server o i relativi database eliminati.
>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa prima esercitazione introduttiva e aver creato un database con alcuni dati di esempio, sono disponibili numerose altre esercitazioni che può essere opportuno consultare per mettere in pratica i concetti appresi in questa esercitazione. 

- Per un'introduzione all'esercitazione sull'autenticazione di SQL Server, vedere [SQL authentication and authorization](sql-database-control-access-sql-authentication-get-started.md) (Autenticazione e autorizzazione di SQL).
- Per un'introduzione all'esercitazione sull'autenticazione di Azure Active Directory, vedere [Azure AD authentication and authorization](sql-database-control-access-aad-authentication-get-started.md) (Autenticazione e autorizzazione di Azure AD).
* Per eseguire query sul database di esempio nel portale di Azure, vedere [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) (Anteprima pubblica: esperienza di query interattiva per i database SQL).
* Se si ha familiarità con Excel, vedere l'esercitazione [Connettere Excel a un database SQL di Azure e creare un report](sql-database-connect-excel.md).
* Se si è pronti per iniziare a scrivere codice, scegliere il linguaggio di programmazione in [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).
* Per spostare i database SQL Server locali in Azure, vedere [Migrating a database to SQL Database](sql-database-cloud-migrate.md) (Migrazione di un database al database SQL).
* Per caricare alcuni dati in una nuova tabella da un file CSV usando lo strumento da riga di comando BCP, vedere [Caricare dati da CSV in Azure SQL Data Warehouse (file flat)](sql-database-load-from-csv-with-bcp.md).
* Per iniziare a creare tabelle e altri oggetti, vedere l'argomento "Per creare una tabella" in [Esercitazione per la creazione di una tabella](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
[Informazioni sul database SQL](sql-database-technical-overview.md)

