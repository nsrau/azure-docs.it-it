<properties 
	pageTitle="Aggiornare un server SQL di Azure alla versione 12 con PowerShell" 
	description="Aggiornare il server SQL di Azure alla versione 12 con PowerShell." 
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
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# Aggiornare il server SQL di Azure alla versione 12 con PowerShell
 

In questo articolo viene illustrato come aggiornare un server di Database SQL alla versione 12 utilizzando indicazioni sul livello di prezzo e relative al pool elastico.



## Prerequisiti 

Per eseguire aggiornamento di un server alla versione 12 con PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a seconda della versione potrebbe essere necessario passarlo alla modalità di gestione delle risorse per accedere ai cmdlet di PowerShell di Gestione risorse di Azure.

Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

I cmdlet per la creazione e la gestione dei database SQL di Azure si trovano nel modulo di Gestione risorse di Azure. Quando si avvia Azure PowerShell, i cmdlet nel modulo di Azure vengono importati per impostazione predefinita. Per passare al modulo AzureResourceManager, usare il cmdlet **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Se viene visualizzato un avviso che indica 'Il cmdlet Switch-AzureMode è obsoleto e verrà rimosso in una versione futura'. È possibile ignorarlo e passare alla sezione successiva.

Per informazioni dettagliate, vedere [Utilizzo di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).



## Configurazione delle credenziali

Per eseguire i cmdlet PowerShell nella sottoscrizione di Azure, è necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso


## Selezionare la sottoscrizione ad Azure

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


## Aggiornamento di un server SQL di Azure


    # Adding the account
    #
    Add-AzureAccount
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName 
    $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Mapping di aggiornamento personalizzato

Se le indicazioni non sono appropriate per il server e il caso aziendale, è possibile scegliere la modalità di aggiornamento dei database e è possibile eseguirne il mapping a database singoli o elastici.

Aggiornamento di database in un pool di database elastici:

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


Aggiornare i database in singoli database:

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## Informazioni correlate

- [Cmdlet di Gestione risorse del database SQL di Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Cmdlet di Gestione servizi del database SQL di Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO2-->