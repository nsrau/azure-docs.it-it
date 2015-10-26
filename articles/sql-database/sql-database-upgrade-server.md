<properties 
	pageTitle="Aggiornare un server SQL di Azure alla versione V12 con PowerShell" 
	description="Aggiornare il server SQL di Azure alla versione 12 con PowerShell" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# Eseguire l'aggiornamento a SQL Database V12 tramite PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


Questo articolo illustra come effettuare l’aggiornamento a SQL Database V12 tramite PowerShell.

Durante il processo di aggiornamento a SQL Database V12, è inoltre necessario aggiornare i database Web e Business a un nuovo livello di servizio. Le indicazioni riportate di seguito includono l'utilizzo delle raccomandazioni sui piani tariffari e i pool elastici per assistere con [l'aggiornamento dei database Web e Business](sql-database-upgrade-new-service-tiers.md) sul server.


## Prerequisiti 

Per eseguire aggiornamento di un server alla versione 12 con PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a seconda della versione potrebbe essere necessario passarlo alla modalità di gestione delle risorse per accedere ai cmdlet di PowerShell di Gestione risorse di Azure.

> [AZURE.IMPORTANT]Iniziando con la versione di anteprima di Azure PowerShell 1.0, il cmdlet Switch-AzureMode non è più disponibile e i cmdlet che sono stati nel modulo Azure ResourceManager sono stati rinominati. Gli esempi in questo articolo utilizzano nuove convenzioni di denominazione dell’Anteprima di PowerShell 1.0. Per informazioni dettagliate, vedere [Deprecazione di Switch-AzureMode, in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Per eseguire i cmdlet di PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a causa della rimozione di Switch-AzureMode, scaricare e installare la versione più recente di Azure PowerShell eseguendo l’[installazione guidata della piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## Configurare le credenziali e selezionare la sottoscrizione

Per eseguire i cmdlet PowerShell nella sottoscrizione di Azure, è necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso

Per selezionare la sottoscrizione con cui lavorare, è necessario l’ID sottoscrizione (**-SubscriptionId**) o il nome della sottoscrizione (**-SubscriptionName**). che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati.

Eseguire il cmdlet seguente con le informazioni della sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

I comandi seguenti verranno eseguiti con la sottoscrizione appena selezionata.

## Ottenere raccomandazioni

Per ottenere le indicazioni per l'aggiornamento del server eseguire il cmdlet seguente:

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Per ulteriori informazioni, vedere [indicazioni sui pool di database elastici di Database SQL di Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) e [indicazioni sui livelli di prezzo di Database SQL di Azure](sql-database-service-tier-advisor.md).



## Avviare l'aggiornamento

Per avviare l'aggiornamento del server di eseguire il cmdlet seguente:

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando si esegue questo comando, inizia il processo di aggiornamento. È possibile personalizzare l'output dell’indicazione e fornire le indicazioni modificate a questo cmdlet.


## Aggiornare un server


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Mapping di aggiornamento personalizzato

Se le indicazioni non sono appropriate per il server e il caso aziendale, è possibile scegliere la modalità di aggiornamento dei database e è possibile eseguirne il mapping a database singoli o elastici.

I parametri ElasticPoolCollection e DatabaseCollection sono facoltativi:
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## Informazioni correlate

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->