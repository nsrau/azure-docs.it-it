<properties
    pageTitle="Creare un nuovo pool di database elastici con PowerShell | Microsoft Azure"
    description="Informazioni su come usare PowerShell per la scalabilità delle risorse del database SQL di Azure tramite la creazione di un pool di database elastici scalabile per la gestione di più database."
	services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Creare un nuovo pool di database elastici con PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Informazioni su come gestire un [pool di database elastici](sql-database-elastic-pool.md) con i cmdlet di PowerShell.

Per i codici di errore comuni, vedere [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md).

> [AZURE.NOTE] I pool elastici sono disponibili a livello generale in tutte le aree di Azure ad eccezione di Stati Uniti centro-settentrionali e India occidentale, dove sono attualmente in anteprima. La disponibilità generale dei pool elastici in queste aree verrà offerta al più presto. In più, al momento i pool elastici non supportano i database che utilizzano [OLTP o Analytics in memoria](sql-database-in-memory.md).


È necessario eseguire Azure PowerShell 1.0 o versione successiva. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Creare un nuovo pool

Il cmdlet [New AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crea un nuovo pool. I valori per eDTU per pool, DTU min e max sono vincolati dal valore del livello di servizio (Basic, Standard o Premium). Vedere [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Creare un nuovo database elastico in un pool

Usare il cmdlet [New AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e impostare il parametro **ElasticPoolName** per il pool di destinazione. Per spostare un database esistente in un pool, vedere [Spostare un database in un pool elastico](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Creare un pool e popolarlo con più database nuovi 

La creazione di un numero elevato di database in un pool può richiedere tempo quando viene eseguita tramite il portale o i cmdlet di PowerShell che creano un database singolo alla volta. Per automatizzare la creazione in un nuovo pool, vedere [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## Esempio: Creare un pool con PowerShell 

Questo script crea un nuovo gruppo di risorse di Azure e un nuovo server. Quando richiesto, specificare un nome utente dell'amministratore e una password per il nuovo server (non le credenziali di Azure).

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
- [Aumentare il numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze.

<!---HONumber=AcomDC_0907_2016-->