<properties
	pageTitle="Gestire il database SQL di Azure con PowerShell | Microsoft Azure"
	description="Gestione del database SQL di Azure con PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="sstein"/>

# Gestire il database SQL di Azure con PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

Questo argomento illustra i cmdlet PowerShell che consentono di eseguire molte attività del database SQL di Azure. Per un elenco più completo, vedere [Cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/mt574084.aspx).


## Creare un gruppo di risorse

Creare un gruppo di risorse per il Database SQL e le relative risorse di Azure con il cmdlet [New AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md). Per uno script di esempio, vedere [Creare uno script di PowerShell per database SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## Creare un server di database SQL

Creare un database di SQL Server con il cmdlet [New AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Sostituire *server1* con il nome del server. I nomi dei server devono essere univoci nell'ambito di tutti i server di database SQL di Azure. Se il nome del server è già in uso, viene visualizzato un errore. Il completamento di questo comando può richiedere alcuni minuti. Il gruppo di risorse deve già esistere nella sottoscrizione.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Per altre informazioni, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). Per uno script di esempio, vedere [Creare uno script di PowerShell per database SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Creare una nuova regola del firewall per il server di database SQL

Creare una regola del firewall per accedere al server con il cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il client. Il gruppo di risorse e il server devono già esistere nella sottoscrizione.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Per consentire al server l'accesso ad altri servizi di Azure, creare una regola del firewall e impostare sia `-StartIpAddress` che `-EndIpAddress` su **0.0.0.0**. Questa regola del firewall speciale consente a tutto il traffico di Azure di accedere al server.

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Per uno script di esempio, vedere [Creare uno script di PowerShell per database SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Creare un database SQL (vuoto)

Creare un database con il cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Il gruppo di risorse e il server devono già esistere nella sottoscrizione.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Per altre informazioni, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). Per uno script di esempio, vedere [Creare uno script di PowerShell per database SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Modifica del livello di prestazioni di un database SQL

Ridimensionare il database verso l'alto o verso il basso con il cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). Il gruppo di risorse, il server e il database devono già esistere nella sottoscrizione. Impostare il `-RequestedServiceObjectiveName` a uno spazio singolo (come nel seguente frammento) per il livello di base. Impostarlo su *S0*, *S1*, *P1*, *P6* e così via, come nell'esempio precedente per gli altri livelli.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Per altre informazioni, vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md). Per uno script di esempio, vedere [Script di esempio di PowerShell per modificare il livello di servizio e il livello delle prestazioni del database SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## Copiare un database SQL nello stesso server

Copiare un database SQL nello stesso server con il cmdlet [New AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx). Impostare `-CopyServerName` e `-CopyResourceGroupName` sugli stessi valori del server di database e del gruppo di risorse di origine.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md). Per uno script di esempio, vedere [Copiare uno script di PowerShell per database SQL](sql-database-copy-powershell.md#example-powershell-script).


## Eliminazione di un database SQL

Eliminare un database SQL con il cmdlet [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). Il gruppo di risorse, il server e il database devono già esistere nella sottoscrizione.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Eliminare un server di database SQL

Eliminare un server con il cmdlet [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Creare e gestire pool di database elastici tramite PowerShell

Per informazioni sulla creazione di pool di database elastici mediante PowerShell, vedere [Creare un nuovo pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md).

Per informazioni sulla gestione di pool di database elastici mediante PowerShell, vedere [Monitorare e gestire un pool di database elastici con PowerShell](sql-database-elastic-pool-manage-powershell.md).



## Informazioni correlate

- [Cmdlet del database SQL di Azure.](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)

<!---HONumber=AcomDC_0914_2016-->