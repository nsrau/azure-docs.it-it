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
	ms.date="09/30/2015" 
	ms.author="sstein"/>

# Eseguire l'aggiornamento a SQL Database V12 tramite PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


Questo articolo illustra come effettuare l’aggiornamento a SQL Database V12 tramite PowerShell.

Durante il processo di aggiornamento a SQL Database V12, è inoltre necessario aggiornare i database Web e Business a un nuovo livello di servizio. Le indicazioni riportate di seguito includono l'utilizzo delle raccomandazioni sui piani tariffari e i pool elastici per assistere con [l'aggiornamento dei database Web e Business](sql-database-upgrade-new-service-tiers.md) sul server.


## Prerequisiti 

Per eseguire aggiornamento di un server alla versione 12 con PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a seconda della versione potrebbe essere necessario passarlo alla modalità di gestione delle risorse per accedere ai cmdlet di PowerShell di Gestione risorse di Azure.

Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

I cmdlet per la creazione e la gestione dei database SQL di Azure si trovano nel modulo di Gestione risorse di Azure. Quando si avvia Azure PowerShell, i cmdlet nel modulo di Azure vengono importati per impostazione predefinita. Per passare al modulo Gestione risorse di Azure, usare il cmdlet **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Se viene visualizzato un avviso che indica 'Il cmdlet Switch-AzureMode è obsoleto e verrà rimosso in una versione futura'. È possibile ignorarlo e passare alla sezione successiva.

Per informazioni dettagliate, vedere [Utilizzo di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).



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

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Per ulteriori informazioni, vedere [indicazioni sui pool di database elastici di Database SQL di Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) e [indicazioni sui livelli di prezzo di Database SQL di Azure](sql-database-service-tier-advisor.md).



## Avviare l'aggiornamento

Per avviare l'aggiornamento del server di eseguire il cmdlet seguente:

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando si esegue questo comando, inizia il processo di aggiornamento. È possibile personalizzare l'output dell’indicazione e fornire le indicazioni modificate a questo cmdlet.


## Aggiornare un server


    # Adding the account
    #
    Add-AzureAccount
    
    # Switch mode
    #
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


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
     
    # Creating single database mapping
    #
    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap.Name = "DBMain" 
    $databaseMap.TargetEdition = "Standard" 
    $databaseMap.TargetServiceLevelObjective = "S0" 
     
    # Starting the upgrade
    #
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool) 
    




- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)



## Informazioni correlate

- [Cmdlet di Gestione risorse del database SQL di Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)

<!---HONumber=Oct15_HO1-->