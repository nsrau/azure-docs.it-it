<properties
    pageTitle="Creare un pool di database elastici (PowerShell) | Microsoft Azure"
    description="Informazioni su come usare PowerShell per la scalabilità delle risorse del database SQL di Azure tramite la creazione di un pool di database elastici scalabile per la gestione di più database."
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="03/27/2016"
    ms.author="sstein"/>

# Creare un pool di database elastici con PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Informazioni su come gestire un [pool di database elastici](sql-database-elastic-pool.md) con i cmdlet di PowerShell.

Per i codici di errore comuni, vedere [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md).

> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-portal.md) in un unico passaggio.


È necessario eseguire Azure PowerShell 1.0 o versione successiva. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Creare un pool

Il cmdlet [New AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crea un pool.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Creare un nuovo database elastico in un pool

Per creare un nuovo database direttamente all'interno di un pool, usare il cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e impostare il parametro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Spostare un database autonomo in un pool

Per spostare un database esistente in un pool, usare il cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) e impostare il parametro **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Creare un esempio di pool di PowerShell

Questo script crea un nuovo server, quindi quando viene richiesto un nome utente e una password, immettere un account di accesso e una password amministratore per il nuovo server, non le credenziali di Azure.

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Passaggi successivi

- [Gestire il pool](sql-database-elastic-pool-manage-powershell.md)
- [Creare processi elastici](sql-database-elastic-jobs-overview.md): i processi elastici consentono di eseguire script T-SQL su un numero qualsiasi di database nel pool.

<!---HONumber=AcomDC_0413_2016-->